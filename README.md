# CPython Skills Marketplace

A Claude Code marketplace providing specialized skills for working with the CPython repository - the reference implementation of the Python programming language.

## Overview

This marketplace hosts Claude Code plugins with AI-agent skills for CPython development. Includes one comprehensive plugin with multiple skills providing deep knowledge about CPython's codebase structure, build system, testing infrastructure, and coding standards.

Skills are now invoked as `cpython:dev`, `cpython:build`, `cpython:style`, `cpython:docs`, `cpython:jit`, and `cpython:backport`.

## Installation

To use these skills in Claude Code within a CPython repository checkout:

1. Add the marketplace:
   ```
   /plugin marketplace add gpshead/cpython-skills
   ```

2. Install the plugin:
   ```
   /plugin install cpython
   ```

3. In your CPython repository, ask Claude:
   ```
   write a CLAUDE.local.md that instructs you to load and use all of the cpython plugin skills in this repo.
   ```

## Available Plugins

### CPython Skills

**Name**: `cpython`
**Description**: Skills for working with the CPython repository - helping with building, testing, and contributing to Python's implementation

This plugin provides six specialized skills with a meta-skill that coordinates loading:

#### 1. dev (Entry Point)
**When to use**: Starting any CPython development task - fixing bugs, adding features, understanding code

Provides:
- Codebase orientation and source code structure
- Workflow coordination - guides you to load other skills as needed
- Recommended tools (`rg`, `gh`, `jq`)
- Engineering notebook management for PRs and branches
- Scratch space usage

#### 2. build
**When to use**: Compiling CPython, running tests, verifying changes work, debugging test failures

Includes:
- Build directory setup with ccache support
- Platform-specific configuration (Linux, macOS, Windows)
- Argument Clinic code generation
- Build verification and troubleshooting
- unittest-based testing (not pytest!)
- Using `--match` for test filtering (not `-k`!)
- Code coverage collection
- Interactive debugging with tmux

#### 3. style
**When to use**: Preparing commits, running pre-commit hooks, validating changes

Covers:
- PEP 7 (C code) and PEP 8 (Python code) compliance
- Trailing whitespace and file ending rules
- Type annotation policy (no annotations in Lib/!)
- Pre-commit hooks (via `prek` if available, else `pre-commit`) and patchcheck
- Comment quality - avoiding PR-indexed comments that won't age well

#### 4. docs
**When to use**: Editing documentation in Doc/, adding version markers, creating NEWS entries

Covers:
- Documentation in ReST format in Doc/ tree
- Documentation tooling setup and validation
- Version markers (always use `next`)
- NEWS file entries for bug fixes and features

#### 5. jit
**When to use**: Working on CPython's experimental JIT compiler - modifying build scripts in Tools/jit/, editing Python/jit.c, changing bytecodes that affect stencils, debugging JIT failures

Covers:
- LLVM 21 toolchain setup
- JIT configure flags (`--enable-experimental-jit` options)
- Copy-and-patch compilation pipeline (template.c to jit_stencils.h)
- Stencil regeneration workflow
- Runtime JIT debugging (comparing `PYTHON_JIT=0` vs `PYTHON_JIT=1`)
- Supported target platforms and object formats
- Key concepts: stencils, holes, preserve_none, musttail, GOT

#### 6. backport
**When to use**: Backporting a merged change to a maintenance branch (3.14, 3.13, ...) - including redoing a failed automated miss-islington backport. Also invocable directly: `/cpython:backport 3.13 <PR URL or number>`

Covers:
- The `cherry_picker` conventions the bots expect: `backport-<sha7>-<version>` branch naming, `[X.Y] title (GH-NNNN)` commit/PR titles, `(cherry picked from commit ...)` trailers
- Intelligent conflict resolution - translating the fix into the older branch's idioms (pre-Argument-Clinic code, missing helper APIs, split test packages) instead of failing like the automated tool
- Building and testing the result on the target branch before offering to push
- Opening the backport PR with `gh`, including "Manual backport notes" describing any hand adaptations
- Always asks before pushing or creating the PR (fork auth setups vary)

### Commands

#### /comments
Audits every comment added in your current diff against a "would this still be useful in two years?" test, then rewrites or removes the ones that are over-indexed on the current PR (history references, restating-the-code, unexplained jargon). The `style` skill points here as part of the pre-commit workflow, but it's also useful to run on an existing PR you're reviewing or picking up - check out the branch and invoke `/comments` to clean up comments before merge.

## Advanced Usage

### Preventing Use of System Python

The skills instruct Claude to always use the locally-built interpreter (`build/python`) rather than `python` or `python3` from `$PATH`. This is critical when developing CPython — the system Python is a different build and won't reflect your changes.

If you find Claude occasionally still reaches for the system Python (e.g., when running a quick snippet during investigation), you can add a [PreToolUse hook](https://docs.claude.com/en/docs/claude-code/hooks) as a hard guardrail. In your CPython repo's `.claude/settings.local.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'cmd=$(jq -r \".tool_input.command // empty\"); if echo \"$cmd\" | grep -qP \"(?<![/\\\\w])python3?\\\\s\" && ! echo \"$cmd\" | grep -q \"build/python\" && ! echo \"$cmd\" | grep -q \"Tools/\"; then echo \"Use build/python instead of system python in the CPython repo.\" >&2; exit 2; fi'"
          }
        ]
      }
    ]
  }
}
```

The hook receives the tool call as JSON on stdin; exiting with code 2 blocks the call and feeds the stderr message back to Claude. This rejects any Bash command that invokes bare `python`/`python3` without going through `build/python`, while allowing build tooling under `Tools/` (e.g., `python3 Tools/jit/build.py`), which is designed to run under a pre-existing Python. Tune the patterns to your workflow.

## License

CC0 1.0 Universal

## References

- [Claude Code Plugin Marketplaces Documentation](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
- [Claude Code Plugins Documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- [Claude Code Skills Documentation](https://docs.claude.com/en/docs/claude-code/skills)
- [Simon Willison on Claude Skills](https://simonwillison.net/2025/Oct/16/claude-skills/)
- [CPython Developer Guide](https://devguide.python.org/)

# CPython Skills Marketplace

A Claude Code marketplace providing specialized skills for working with the CPython repository - the reference implementation of the Python programming language.

## Overview

This marketplace hosts Claude Code plugins with AI-agent skills for CPython development. Includes one comprehensive plugin with multiple skills providing deep knowledge about CPython's codebase structure, build system, testing infrastructure, and coding standards.

Skills are now invoked as `cpython:dev`, `cpython:build`, `cpython:style`, `cpython:docs`, and `cpython:jit`.

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

This plugin provides five specialized skills with a meta-skill that coordinates loading:

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
- Pre-commit hooks and patchcheck

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

## Recommended Tools

These tools improve the agentic development experience: `rg`, `gh`, `jq`

## Usage Examples

When working with CPython, the AI agent will:

1. Load `dev` when starting work in the repo - provides orientation and workflow guidance
2. Load `build` when you need to compile or run tests - the `dev` skill prompts this
3. Load `style` when preparing commits - the `dev` skill prompts this
4. Load `docs` when updating documentation - the `dev` skill prompts this

The `dev` skill acts as a coordinator, explicitly guiding the agent to load specialized skills as your workflow progresses.

## License

CC0 1.0 Universal

## References

- [Claude Code Plugin Marketplaces Documentation](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
- [Claude Code Plugins Documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- [Claude Code Skills Documentation](https://docs.claude.com/en/docs/claude-code/skills)
- [Simon Willison on Claude Skills](https://simonwillison.net/2025/Oct/16/claude-skills/)
- [CPython Developer Guide](https://devguide.python.org/)

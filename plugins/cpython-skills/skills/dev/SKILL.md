---
name: dev
description: Use this skill when working in the CPython repository for any development task - fixing bugs, adding features, understanding code, or making contributions. Provides codebase orientation and coordinates loading of specialized skills (build, style, docs, jit) as your workflow progresses.
---

# CPython Development

You are working in the CPython repository - the implementation of the Python language runtime and standard library itself.

## Run Python via the Built Interpreter

**Run all Python code with the locally-built interpreter — never `python` or `python3` from `$PATH`:**

- Linux: `build/python`
- macOS: `build/python.exe`

This applies to any execution of code you are changing or testing — running tests, testing snippets, verifying behavior, importing modules. The system Python is a different build and won't reflect your changes. If the build directory doesn't exist yet, load the `build` skill and compile first.

One exception: build and regeneration tooling under `Tools/` (e.g. `Tools/jit/build.py`, regen targets) is designed to run under a pre-existing Python and may use `python3` — that tooling generates sources rather than executing your changes.

## Load Specialized Skills As Needed

This skill provides orientation. **Load additional skills when your task requires them:**

- **Load `build` skill when**: compiling CPython, running tests, verifying changes work, debugging test failures, or checking if your fix is correct
- **Load `style` skill when**: preparing commits, running pre-commit hooks, checking code style, or validating changes before pushing
- **Load `docs` skill when**: editing files in `Doc/`, adding version markers, creating NEWS entries, or updating documentation
- **Load `jit` skill when**: working on the JIT compiler, modifying `Tools/jit/` or `Python/jit.c`, debugging JIT-specific failures, or changing bytecodes that affect stencil generation
- **Load `backport` skill when**: backporting a merged change to a maintenance branch (3.14, 3.13, ...), or redoing a failed automated miss-islington backport

## Recommended Tools

Prefer these tools when available: `rg`, `gh`, `jq`

## Source Code Structure

**`Lib/`** - Python standard library (pure Python). Example: `Lib/zipfile.py`

**`Modules/`** - C extension modules for performance/low-level access. Example: `Modules/_csv.c`

**`Objects/` and `Python/`** - Core types (list, dict, int), builtins, runtime, interpreter loop

**`Include/`** - C header files for public and internal C APIs

**`Lib/test/`** - All unittests
- Test naming: `test_{module_name}.py` or `test_{module_name}/`
- Examples: `Lib/zipfile.py` → `Lib/test/test_zipfile/` (a test package), `Modules/_csv.c` → `Lib/test/test_csv.py`
- Test packages require `load_tests()` in `test_package/__init__.py` to work with `python -m test`

**`Doc/`** - Documentation in .rst format (source for python.org docs), builds to `Doc/build/`

**`InternalDocs/`** - Maintainer documentation (`InternalDocs/README.md` is the starting point)

**`Tools/`** - Build tools like Argument Clinic, development utilities

## Argument Clinic

**`**/clinic/**` subdirectories are auto-generated** - never edit these directly. Load the `build` skill for regeneration commands.

## Engineering Notebooks

Load and maintain a notebook whenever working on a feature or PR. Notebooks are how project state survives context compaction and session restarts — the next session (or the next model) picks up where this one left off instead of rediscovering everything:

- **For PRs**: `.claude/pr-{PR_NUMBER}.md`
- **For branches**: `.claude/branch-{branch_name_without_slashes}.md` (when not on `main`)

Keep notebooks updated with learnings and project state as you work and after commits. Include: problem statement, key findings, file locations, design decisions, testing strategy, and status. (`.claude/` is in CPython's `.gitignore`, so notebooks never pollute commits.)

## Scratch Space

Put exploration files, test scripts, and prototypes in `.claude/sandbox/`, never in the repo root or source tree.

## Optional Developer Resources

- **Developer Guide**: If `REPO_ROOT/../devguide/` exists, see `developer-workflow/` and `documentation/` subdirectories
- **PEPs**: May exist in `REPO_ROOT/../peps/` tree - reference relevant PEPs when working on changes

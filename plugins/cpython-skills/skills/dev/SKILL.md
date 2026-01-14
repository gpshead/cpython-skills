---
name: dev
description: Use this skill when working in the CPython repository for any development task - fixing bugs, adding features, understanding code, or making contributions. Provides codebase orientation and coordinates loading of specialized skills (build, style, docs) as your workflow progresses.
---

# CPython Development

You are working in the CPython repository - the implementation of the Python language runtime and standard library itself.

## Load Specialized Skills As Needed

This skill provides orientation. **Load additional skills when your task requires them:**

- **Load `build` skill when**: compiling CPython, running tests, verifying changes work, debugging test failures, or checking if your fix is correct
- **Load `style` skill when**: preparing commits, running pre-commit hooks, checking code style, or validating changes before pushing
- **Load `docs` skill when**: editing files in `Doc/`, adding version markers, creating NEWS entries, or updating documentation

## Recommended Tools

Prefer these tools when available: `rg`, `gh`, `jq`

## Source Code Structure

**`Lib/`** - Python standard library (pure Python). Example: `Lib/zipfile.py`

**`Modules/`** - C extension modules for performance/low-level access. Example: `Modules/_csv.c`

**`Objects/` and `Python/`** - Core types (list, dict, int), builtins, runtime, interpreter loop

**`Include/`** - C header files for public and internal C APIs

**`Lib/test/`** - All unittests
- Test naming: `test_{module_name}.py` or `test_{module_name}/`
- Examples: `Lib/zipfile.py` → `Lib/test/test_zipfile**`, `Modules/_csv.c` → `Lib/test/test_csv.py`
- Test packages require `load_tests()` in `test_package/__init__.py` to work with `python -m test`

**`Doc/`** - Documentation in .rst format (source for python.org docs), builds to `Doc/build/`

**`InternalDocs/`** - Maintainer documentation (`InternalDocs/README.md` is the starting point)

**`Tools/`** - Build tools like Argument Clinic, development utilities

## Argument Clinic

**`**/clinic/**` subdirectories are auto-generated** - never edit these directly. Load the `build` skill for regeneration commands.

## Engineering Notebooks

ALWAYS load and maintain notebooks when working on features or PRs:
- **For PRs**: `.claude/pr-{PR_NUMBER}.md`
- **For branches**: `.claude/branch-{branch_name_without_slashes}.md` (when not on `main`)

Keep notebooks updated with learnings and project state as you work and after commits. Include: problem statement, key findings, file locations, design decisions, testing strategy, and status.

## Scratch Space

**NEVER create throwaway files in repo root.** Use `.claude/sandbox/` for exploration files, test scripts, and prototypes.

## Optional Developer Resources

- **Developer Guide**: If `REPO_ROOT/../devguide/` exists, see `developer-workflow/` and `documentation/` subdirectories
- **PEPs**: May exist in `REPO_ROOT/../peps/` tree - reference relevant PEPs when working on changes

## Typical Workflow

1. **Understand the task** - explore code, read relevant files
2. **Make changes** - edit source files
3. **Build and test** - load `build` skill, compile, run tests
4. **Validate style** - load `style` skill, run pre-commit and patchcheck
5. **Update docs** - if needed, load `docs` skill for documentation changes

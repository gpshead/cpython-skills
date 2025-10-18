---
name: cpython-navigation
description: Use this skill when you need to understand the CPython source code structure, find where specific functionality is implemented (C runtime, stdlib, tests, docs), or understand the relationship between different parts of the codebase like modules and their tests.
---

# CPython Source Code Navigation

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

Generates C boilerplate for argument parsing from specially formatted comments in `.c` files. Output goes to `**/clinic/**` subdirectories.

**NEVER edit files in `**/clinic/**` subdirectories** - they're auto-generated! Edit the source `.c` file, then run `make -C BUILD_DIR clinic`.

## Scratch Space

**NEVER create throwaway files in repo root.** Use `.claude/sandbox/` for exploration files, test scripts, and prototypes.

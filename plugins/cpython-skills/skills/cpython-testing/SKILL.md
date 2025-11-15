---
name: cpython-testing
description: Use this skill when you need to run CPython tests, whether running individual test files, specific test cases, the full test suite, or collecting code coverage. It covers unittest-based testing (not pytest), debugging tests, and test best practices.
---

# Running CPython Tests

**Critical rules:**
1. **ALWAYS use sub-agents when running tests**
2. **NEVER use `pytest`** - CPython tests are `unittest` based
3. **Use `--match` not `-k`** for filtering (this is not pytest!)

Prerequisite: `BUILT_PY=build/python` or `build/python.exe`

## Running Tests

```bash
# Single test module (recommended - proper discovery, parallel execution)
$BUILT_PY -m test test_zipfile -j $(nproc)

# Multiple modules
$BUILT_PY -m test test_csv test_json -j $(nproc)

# Direct execution (quick but may miss test packages)
$BUILT_PY Lib/test/test_csv.py

# Specific test pattern (use --match, NOT -k!)
$BUILT_PY -m test test_zipfile --match "*large*" -j $(nproc)
$BUILT_PY -m test test_csv --match "TestDialect*"
$BUILT_PY -m test test_json --match "TestEncode.test_encode_string"

# Full test suite (ASK FIRST - takes significant time!)
make -C $BUILD_DIR test

# Useful flags: -v (verbose), -f (fail fast), --list-tests (show all), --help
```

**Test packages** (directories like `test_asyncio/`) require `load_tests()` in `__init__.py` to work with `python -m test`.

## Code Coverage

```bash
# Collect coverage (uses trace mechanism via libregrtest)
$BUILT_PY -m test --coverage test_csv test_json --coveragedir .claude/coverage/ -j $(nproc)

# Reports go to specified coveragedir
```

## Debugging

For interactive debugging (pdb/lldb/gdb) or testing REPL features: **Control a tmux session**.

Add `breakpoint()` in test code, then run with `-v` for verbose output.

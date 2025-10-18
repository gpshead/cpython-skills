---
name: cpython-testing
description: Use this skill when you need to run CPython tests, whether running individual test files, specific test cases, the full test suite, or collecting code coverage. It covers unittest-based testing (not pytest), debugging tests, and test best practices.
---

# Running CPython Tests

## Purpose

This skill provides comprehensive guidance for running CPython's unittest-based test suite, from individual test cases to full test runs, including coverage collection and debugging.

## Critical Testing Rules

1. **ALWAYS use sub-agents when running tests**
2. **NEVER use `pytest`** - CPython tests are `unittest` based
3. **Use `--match` not `-k`** for filtering specific tests (this is not pytest!)

## Prerequisites

Ensure you have built CPython first. You'll need:
```bash
BUILT_PY=<path-to-built-python>  # e.g., build/python or build/python.exe
```

## Running Individual Test Files

### Direct Execution
For quick test file runs:
```bash
$BUILT_PY Lib/test/test_csv.py
```

This works for simple test files but may not load all test cases in complex test packages.

### Recommended Method
Use the test module for proper test loading:
```bash
# For a single test module
$BUILT_PY -m test test_zipfile -j $(nproc)

# For multiple test modules
$BUILT_PY -m test test_csv test_json test_zipfile -j $(nproc)
```

Benefits:
- Proper test discovery
- Parallel execution with `-j`
- Better output formatting
- Handles test packages correctly

## Running Specific Test Cases

### Using --match
To run specific tests within a module:
```bash
# Run tests matching a glob pattern
$BUILT_PY -m test test_zipfile --match "*large*" -j $(nproc)

# Run a specific test class
$BUILT_PY -m test test_csv --match "TestDialect*"

# Run a specific test method
$BUILT_PY -m test test_json --match "TestEncode.test_encode_string"
```

**Important**: Use `--match` (glob pattern), NOT `-k` (pytest flag)!

## Running the Full Test Suite

### Important Warning
**Ask before running the entire test suite!** It takes significant time.

```bash
# Run all tests
make -C $BUILD_DIR test

# Or equivalently
$BUILT_PY -m test
```

### Recommended Workflow
1. Focus on specific tests first
2. Ensure those pass
3. Only then run the full suite if needed

## Test Discovery Options

### List Available Tests
```bash
# See all available test modules
$BUILT_PY -m test --list-tests

# See help for all options
$BUILT_PY -m test --help
```

### Common Options
- `-j N`: Run tests in parallel with N workers (use `-j $(nproc)`)
- `-v`: Verbose output
- `-f`: Fail fast (stop on first failure)
- `-m PATTERN`: Run tests matching glob pattern
- `--match PATTERN`: Filter specific test cases (same as `-m`)

## Test Package Handling

### What are Test Packages?
Some tests are packages (directories) rather than single files:
- Example: `Lib/test/test_asyncio/` (package)
- vs `Lib/test/test_csv.py` (single file)

### Requirements for Test Packages
Test packages require `load_tests()` logic in their `test_package/__init__.py` file to work properly with `python -m test`.

If a test package isn't being discovered:
1. Check for `__init__.py` in the test directory
2. Verify `load_tests()` function exists
3. Look at similar test packages for examples

## Code Coverage

### Collecting Coverage
```bash
# Run tests with coverage for specific modules
$BUILT_PY -m test -j $(nproc) --coverage test_csv test_json --coveragedir .claude/coverage_dir/

# Coverage for a single test
$BUILT_PY -m test --coverage test_zipfile --coveragedir .claude/coverage_dir/
```

### Coverage Mechanism
- Uses `trace` based mechanism
- Implemented via libregrtest
- Output goes to the specified coverage directory

### Analyzing Coverage
Coverage reports will be in the specified `--coveragedir`:
```bash
ls .claude/coverage_dir/
```

## Debugging Tests

### Interactive Debugging
For interactive debugging with pdb, lldb, or gdb:
**Control a tmux session** to drive the interactive debugger.

This is also useful for:
- Testing interactive features (like the REPL)
- Step-through debugging
- Inspecting test failures in detail

### Debug Output
For verbose test output:
```bash
$BUILT_PY -m test test_csv -v
```

### Using pdb
Add breakpoint in test:
```python
def test_something(self):
    breakpoint()  # Python 3.7+
    # or import pdb; pdb.set_trace()
```

Then run the test (use tmux for interactivity).

## Common Testing Workflows

### After Making Code Changes
1. Run related tests specifically:
   ```bash
   $BUILT_PY -m test test_yourmodule -j $(nproc)
   ```

2. If tests pass, run broader related tests

3. Before committing, run the full test suite (ask first!)

### Test-Driven Development
1. Write/modify test:
   ```bash
   $BUILT_PY -m test test_module --match "TestClass.test_new_feature"
   ```

2. Implement feature

3. Iterate until test passes

4. Run full module tests:
   ```bash
   $BUILT_PY -m test test_module -j $(nproc)
   ```

### Coverage-Driven Testing
1. Run tests with coverage:
   ```bash
   $BUILT_PY -m test --coverage test_module --coveragedir .claude/coverage/
   ```

2. Analyze coverage reports

3. Add tests for uncovered code

4. Re-run with coverage to verify

## Integration with Other Skills

Related skills:
- **cpython-build**: Build Python before testing
- **cpython-code-style**: Run linting alongside tests
- **cpython-navigation**: Find test files for modules

## Quick Reference

```bash
# Single test file
$BUILT_PY -m test test_csv -j $(nproc)

# Specific test pattern
$BUILT_PY -m test test_csv --match "TestDialect*" -j $(nproc)

# Multiple modules
$BUILT_PY -m test test_csv test_json -j $(nproc)

# With coverage
$BUILT_PY -m test --coverage test_csv --coveragedir .claude/coverage/ -j $(nproc)

# Full suite (ask first!)
make -C $BUILD_DIR test

# List all options
$BUILT_PY -m test --help
```

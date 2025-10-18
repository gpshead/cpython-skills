---
name: cpython-code-style
description: Use this skill when writing or modifying CPython code to ensure it follows proper coding standards (PEP 7 for C, PEP 8 for Python), formatting rules, linting requirements, and pre-commit checks. It covers whitespace handling, type annotations policy, and documentation formatting.
---

# CPython Coding Style and Standards

## Purpose

This skill provides comprehensive guidance on CPython's coding standards, formatting rules, linting, and quality checks that must pass before code can be committed.

## Coding Style Guidelines

### Python Code (PEP 8)
For Python code in `Lib/` and other Python files:
- Follow **PEP 8** - Python's official style guide
- Be consistent with existing nearby code unless asked to do otherwise

### C Code (PEP 7)
For C code in `Modules/`, `Objects/`, `Python/`, `Include/`:
- Follow **PEP 7** - C code style guide for CPython
- Be consistent with existing nearby code unless asked to do otherwise

### Consistency Principle
**Be consistent with existing nearby code style** unless explicitly asked to do otherwise. When in doubt, match the style of the surrounding code.

## Critical Formatting Rules

### Trailing Whitespace
**NEVER leave trailing whitespace on any line.**

This is a hard rule in CPython:
- No spaces or tabs at the end of lines
- Pre-commit hooks will catch this
- It causes unnecessary diff noise

### File Endings
**ALWAYS preserve the newline at the end of files.**

Most files should end with a single newline character.

## Type Annotations

### Critical Rule for stdlib
**NEVER add Python type annotations to Python code in the `Lib/` tree.**

The standard library does not use inline type annotations. Instead:
- Type stubs are maintained separately in typeshed
- Keep stdlib code annotation-free
- Focus on runtime behavior

### Exceptions
Type annotations may be acceptable in:
- Tools under `Tools/`
- Test code if specifically requested
- Non-stdlib code

## Code Formatting

### No Autoformatting by Default
**We do not autoformat code in this codebase.**

CPython does not use Black or other autoformatters by default.

### Using ruff format
If the user explicitly asks you to run ruff format on a specific file:
```bash
# ruff is in the Doc venv
Doc/venv/bin/ruff format path/to/file.py
```

**Only do this if specifically requested!**

## Linting and Quality Checks

### Pre-commit Hooks

Run pre-commit checks on all files:
```bash
# From repo root
pre-commit run --all-files
```

This will check:
- Trailing whitespace
- File endings
- Basic syntax
- Other configured checks

### Patchcheck

Before committing, run patchcheck from BUILD_DIR:
```bash
make -C $BUILD_DIR patchcheck
```

This validates:
- C code style
- Python code style
- Documentation issues
- Whitespace problems

**Make patchcheck pass before committing!**

### Documentation Checks

For public documentation changes in `Doc/`:
```bash
make -C Doc check
```

This verifies:
- reStructuredText syntax
- Cross-references
- Documentation builds without errors

## Complete Pre-Commit Workflow

Before committing changes, run these steps from repo root:

```bash
# 1. Run pre-commit hooks
pre-commit run --all-files

# 2. Run patchcheck
make -C $BUILD_DIR patchcheck

# 3. If you modified documentation
make -C Doc check

# 4. Run relevant tests
$BUILT_PY -m test test_yourmodule -j $(nproc)
```

## Common Style Issues

### Trailing Whitespace
```python
# BAD - trailing spaces
def foo():
    return 42

# GOOD - no trailing whitespace
def foo():
    return 42
```

### File Endings
```python
# BAD - no newline at end
def foo():
    return 42
# GOOD - single newline at end
def foo():
    return 42

```

### Type Annotations in Lib/
```python
# BAD - type annotations in stdlib
def process(data: list[str]) -> dict[str, int]:
    return {}

# GOOD - no annotations in stdlib
def process(data):
    """Process data and return results.

    Args:
        data: List of strings to process

    Returns:
        Dictionary mapping strings to integers
    """
    return {}
```

## Documentation Style

### Python Docstrings
- Use docstrings for public APIs
- Follow PEP 257 conventions
- Document parameters, return values, and exceptions

### C Documentation
- Use proper comment style from PEP 7
- Document complex algorithms
- Explain non-obvious choices

### reStructuredText (Doc/)
- Follow Sphinx/reST conventions
- Test builds with `make -C Doc check`
- Verify cross-references work

## Editor Configuration

To help maintain style:
- Configure editor to show trailing whitespace
- Set to insert final newline
- Use 4 spaces for Python (not tabs)
- Follow C indentation from PEP 7

## Linting Tools

### Available Tools
- `pre-commit`: Automated hooks for basic checks
- `ruff`: Python linter/formatter (in Doc/venv/bin/)
- C compiler warnings: Enable with `--with-pydebug`

### When Linting Fails
1. Read the error message carefully
2. Fix the specific issue mentioned
3. Re-run the check
4. Don't skip checks - fix the code

## Integration with Other Skills

Related skills:
- **cpython-build**: Build before running patchcheck
- **cpython-testing**: Run tests after style fixes
- **cpython-navigation**: Find files to check

## Quick Reference

```bash
# Pre-commit checks
pre-commit run --all-files

# Patchcheck (before committing)
make -C $BUILD_DIR patchcheck

# Documentation check
make -C Doc check

# Ruff format (only if requested!)
Doc/venv/bin/ruff format path/to/file.py
```

## Key Reminders

1. **Never** leave trailing whitespace
2. **Always** preserve final newline
3. **Never** add type annotations to `Lib/` code
4. **Always** run patchcheck before committing
5. **Be consistent** with surrounding code style
6. **Don't autoformat** unless explicitly requested

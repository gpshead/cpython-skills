---
name: style
description: Use this skill when preparing commits, running pre-commit hooks, checking code style, or validating changes before pushing. Covers PEP 7 (C code) and PEP 8 (Python) compliance, trailing whitespace rules, the no-type-annotations-in-Lib policy, and patchcheck validation. Note: patchcheck requires a build directory - load the `build` skill first if you haven't built CPython yet.
---

# CPython Coding Style and Standards

## Style Guidelines

**Python code** (`Lib/` etc): Follow PEP 8. Be consistent with nearby code.

**C code** (`Modules/`, `Objects/`, `Python/`, `Include/`): Follow PEP 7. Be consistent with nearby code.

## Critical Rules

- No trailing whitespace on any line, and preserve the final newline at the end of files - pre-commit enforces both.
- **No type annotations in the `Lib/` tree** - stdlib annotations are maintained separately in typeshed. Annotations may be OK in `Tools/` or test code if requested.
- No autoformatting by default - use `Doc/venv/bin/ruff format` only if explicitly requested.

## Pre-Commit Workflow

Before committing, run from repo root. `$BUILD_DIR`, `$BUILT_PY`, and `$NCPU` are placeholders from the `build` skill (typically `build`, `build/python`, and your core count) — substitute the concrete values; shell state doesn't persist between commands.

```bash
# 1. Pre-commit hooks (checks whitespace, file endings, syntax)
#    Prefer prek (faster drop-in) if installed; otherwise use pre-commit.
#    Pass the files you changed; --all-files also works but is slower on this tree.
if command -v prek >/dev/null; then prek run --files <changed files>; else pre-commit run --files <changed files>; fi

# 2. Patchcheck (must pass - validates C/Python style, docs, whitespace)
make -C $BUILD_DIR patchcheck

# 3. If you modified Doc/, verify reStructuredText
make -C Doc check

# 4. Run relevant tests
$BUILT_PY -m test test_yourmodule -j $NCPU
```

Then **audit any comments you added** — see Comment Quality below.

## Documentation

- **Python docstrings**: Follow PEP 257, document params/returns/exceptions
- **C comments**: Follow PEP 7, document complex algorithms
- **reStructuredText** (`Doc/`): Follow Sphinx/reST conventions, verify with `make -C Doc check`

## Comment Quality

Comments outlive the PR that added them. Before committing, re-read each comment you added and ask: **would a reader in two years, with no knowledge of this PR, find this useful?** Run `/cpython:comments` for the full guided audit pass — it has the complete test and fix patterns.

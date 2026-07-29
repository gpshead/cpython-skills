---
name: docs
description: Use this skill when editing documentation, working with files in Doc/, adding versionadded or versionchanged markers, creating NEWS entries for bug fixes or features, updating What's New (Doc/whatsnew/), or building the HTML docs. Covers reStructuredText (.rst) format, documentation validation, and NEWS file requirements.
---

# CPython Documentation

CPython documentation is in reStructuredText (ReST) format in the `Doc/` tree.

## Documentation Tooling

```bash
# Set up documentation build environment
make -C Doc venv

# Validate documentation (run this to check your changes)
make -C Doc check

# Build HTML documentation (if full build is needed)
make -C Doc html
```

## Version Markers

When adding `versionadded::`, `versionchanged::`, or similar markers, always use `next` as the version "number" - the doc build and release process fills in the real version.

```rst
.. versionadded:: next

.. versionchanged:: next
   Description of what changed.
```

## NEWS Entries

Bug fixes and new features require a `Misc/NEWS.d/next/` file entry.

The filename must refer to the real GitHub issue number in the upstream `python/cpython` repository. **Never invent an issue number - ask the user which one to use.**

Filename format: `Misc/NEWS.d/next/<CATEGORY>/<YYYY-MM-DD-HH-MM-SS>.gh-issue-<NUMBER>.<NONCE>.rst`

- **`<CATEGORY>`**: must be one of the existing directory names under `Misc/NEWS.d/next/` on the current branch — list that directory to see the valid set (e.g. `Library`, `Core_and_Builtins`, `C_API`); don't invent a category.
- **`<YYYY-MM-DD-HH-MM-SS>`**: the current UTC time.
- **`<NONCE>`**: 6 characters from the urlsafe-base64 alphabet (`A-Z a-z 0-9 _ -`), e.g. `_f-cFH`. Any unique value works; it only prevents filename collisions.

**Entry body**: one or a few complete sentences describing the user-visible change, using Sphinx roles (`:func:`, `:class:`, `:meth:`, `:exc:`, `:mod:`) to link the affected APIs. Don't write "Fixes gh-NNNN" in the body — the filename already carries the issue link. Example:

```rst
When a worker process terminates unexpectedly,
:class:`concurrent.futures.ProcessPoolExecutor` now sets a separate
:exc:`~concurrent.futures.process.BrokenProcessPool` exception on each
pending future instead of sharing a single instance among them all.
```

## What's New

User-visible changes worth highlighting to people upgrading also get an entry in the in-development release's `Doc/whatsnew/<version>.rst` (the highest-numbered file in `Doc/whatsnew/`), in the section for the affected module. Follow the style of neighboring entries. Not every NEWS entry needs one — reserve it for changes a typical upgrader would care about. (The `backport` skill covers what happens to whatsnew entries when a change is backported.)

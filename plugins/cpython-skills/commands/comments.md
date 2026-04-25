---
description: Review comments added in this change for over-indexing on the current PR; fix or remove ones that won't age well.
---

Do a pass through any comments you added in this change. Are they over-indexed to this specific PR or change? Comments will live in the codebase for years and should be genuinely helpful to someone reading them long after this PR is forgotten.

**How to do the pass:**

1. Diff the branch against its merge-base (or use the changes from this session) and pull out every added comment line — `#` and docstrings in Python, `/* */` in C.
2. For each one, apply the test below.
3. Edit or delete the ones that fail. Report what you changed and why; if everything passed, say so briefly.

**Exempt from this pass:** `Misc/NEWS.d/` entries and `Doc/whatsnew/` — those are change logs and are *supposed* to describe the change.

**The test — would a reader in two years, with no knowledge of this PR, find this comment useful?**

A comment fails if it:
- References the change itself: "switched from X", "added for gh-1234", "per Discourse thread", "previously this was…", "now we…".
- Explains *what* the code does when the code already says that.
- Uses jargon or internal shorthand that only makes sense with today's context (a system name, an issue number, a person) without explaining what it means.
- Is dense with subdomain terms or abbreviations a generalist on the team wouldn't know — even if each term is technically correct, stacking several in one sentence is a wall to anyone not already deep in that area.
- States a fact without the *why* — e.g. "must be 64 bytes" with no reason a future editor could check against.

A comment passes if it states an invariant, constraint, or non-obvious causality in standalone terms — something a future reader could use to decide whether it's safe to change the code.

**Fixes:**
- PR-history references → reframe as the standing invariant ("must tolerate empty input; callers don't pre-filter").
- Restates-the-code → delete it.
- Unexplained jargon → either expand the term inline or delete the comment.
- Dense subdomain terms → swap each term for the concrete thing it refers to, or anchor with a one-line example. Aim for readable by someone who knows Python and the codebase generally, not just this corner of it.
- Genuinely temporary notes (TODOs, follow-ups) → keep only if they name a concrete condition for removal.

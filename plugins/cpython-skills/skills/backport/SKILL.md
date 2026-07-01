---
name: backport
argument-hint: <version> [PR URL, PR number, or commit sha]
description: Use this skill when backporting a merged CPython change to a maintenance branch (3.14, 3.13, etc.) - whether invoked as /cpython:backport <version> [PR], asked to backport or cherry-pick a fix from main, or handling a failed automated miss-islington backport. Produces a cherry_picker-convention branch and offers to open the backport PR.
---

# Backporting CPython Changes

Do what `cherry_picker` (the tool behind the miss-islington bot) does, but intelligently: when the cherry-pick conflicts, adapt the change to the maintenance branch's own idioms instead of giving up. The output must be indistinguishable in form from an automated backport - same branch naming, same commit message format, same PR style - so the bots and human workflows built around cherry_picker keep working.

## Inputs

From the invocation (e.g. `/cpython:backport 3.13 https://github.com/python/cpython/pull/12345`) or conversation, determine:

- **Target maintenance branch** (e.g. `3.13`). If not given, check the original PR's `needs backport to X.Y` labels and ask which one(s) to do.
- **Source change**: a PR URL/number, or a commit sha on `main`. Given only an issue number, find the merged PR that closed it.

## Workflow

### 1. Gather facts about the original change

```bash
gh pr view <N> --repo python/cpython --json number,title,author,mergeCommit,labels,url
git log -1 --format=%B <merge-sha>   # full original commit message
```

Fetch upstream `main` first if the merge commit isn't local. Then **look for sibling backport PRs** (search PRs titled `[X.Y] ... (GH-<N>)`): an automated backport to a newer branch (e.g. 3.15) that succeeded is your template for commit message, PR body, and a cross-check for your conflict resolutions. A failed miss-islington attempt tells you exactly where the conflicts are.

### 2. Prepare the target branch

Work in a checkout of the maintenance branch - check `git worktree list` and sibling directories (e.g. `../3.13`) before creating anything new. Sync it first:

```bash
git fetch upstream <version>
git checkout -b backport-<sha7>-<version> upstream/<version>
```

`<sha7>` is the first 7 characters of the squash-merge commit on `main`. This naming is a cherry_picker convention the bots recognize - do not deviate.

### 3. Cherry-pick and resolve conflicts intelligently

```bash
git cherry-pick -x <merge-sha>
```

Conflicts are where you add value over the automated tool. For each conflicted hunk:

- **Understand why it conflicts**: the maintenance branch may predate a refactor. Common cases: helper APIs that don't exist yet (`*_LockHeld` variants, `_Py*_CAST` macros from free-threading work), functions later converted to Argument Clinic, test files split into packages (`test_os.py` vs `test_os/`), renamed or moved files.
- **Translate the fix into the target branch's existing idioms.** Never introduce a symbol that doesn't exist on that branch. Find how the branch already does the equivalent thing - often a nearby function received the same fix in an earlier release and shows the exact local pattern to follow.
- **Preserve semantics exactly, change nothing extra.** The backport should be the original fix expressed in the older branch's dialect, not an improvement pass.

`Misc/NEWS.d/` entries cherry-pick as-is with their original filename. If a change touches files that don't exist at all on the target branch, stop and ask - the fix may not apply or may need a different approach there.

### 4. Commit message - cherry_picker format

```
[<version>] <original title, any prior [X.Y] prefix removed> (GH-<original-PR-number>)

<original commit body>

(cherry picked from commit <full merge sha>)

Co-authored-by: <Original Author <email>>
```

The `(GH-NNNN)` suffix must reference the **original** PR - bots use it to link the backport and manage labels. Add the `Co-authored-by:` trailer only when the person committing the backport differs from the original author (that's why automated backports have it: miss-islington is the committer).

### 5. Verify on the target branch

Load the `build` skill: rebuild and run the tests the change touches - the full affected test files, not just the new cases. Load the `style` skill for pre-commit/patchcheck before finishing. A backport that doesn't compile or pass on the older branch is worse than no backport.

### 6. Push and PR - ask first

Never push or open a PR without confirmation; the user may need to push to their fork themselves (auth setups vary).

1. **Offer the push**: `git push <fork-remote> backport-<sha7>-<version>` (name the remote you found; don't assume `origin` is the fork).
2. **Offer the PR** once pushed, and ask whether it should be a draft:

```bash
gh pr create --repo python/cpython --base <version> \
    --head <fork-owner>:backport-<sha7>-<version> \
    --title "[<version>] <original title> (GH-<N>)" [--draft]
```

PR body: mirror the sibling automated backport's body when one exists (cherry-picked-from line, co-author trailer). If you resolved conflicts by hand, append a short **"Manual backport notes (<version>)"** paragraph stating exactly what had to change for this branch and that the affected tests pass - reviewers of manual backports need to know what differs from the original.

After creation, bedevere/miss-islington handle the `needs backport to X.Y` label swap automatically when the title matches `[X.Y] ... (GH-NNNN)` - no manual label edits needed.

## Report

Whether or not a PR was opened, end with: the branch name and commit sha, what conflicted and how each conflict was adapted (or "clean cherry-pick"), test results, and the remaining steps with exact commands.

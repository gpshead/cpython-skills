---
name: cpython-context
description: Use this skill when working in the CPython repository. It provides essential context about the CPython codebase, recommended tools, and best practices for maintaining engineering notebooks while working on Python runtime and standard library development.
---

# CPython Context

You are working in the CPython repository - the implementation of the Python language runtime and standard library itself.

## Recommended Tools

Prefer these tools when available:
- **`rg` (ripgrep)**: Use instead of `grep` or `find` for faster searching
- **`gh` (GitHub CLI)**: Get PR/issue info, use GraphQL API when needed
- **`jq`**: Process JSON responses
- **`pre-commit`**: Run code quality checks

## File Size Considerations

Source files can be **very long**. Check file sizes before reading entire files - consider reading specific sections or searching instead.

## Engineering Notebooks

ALWAYS load and maintain notebooks when working on features or PRs:
- **For PRs**: `.claude/pr-{PR_NUMBER}.md`
- **For branches**: `.claude/branch-{branch_name_without_slashes}.md` (when not on `main`)

Keep notebooks updated with learnings and project state as you work and after commits. Include: problem statement, key findings, file locations, design decisions, testing strategy, and status.

## Optional Developer Resources

- **Developer Guide**: If `REPO_ROOT/../devguide/` exists, see `developer-workflow/` and `documentation/` subdirectories
- **PEPs**: May exist in `REPO_ROOT/../peps/` tree - reference relevant PEPs when working on changes

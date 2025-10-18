---
name: cpython-context
description: Use this skill when working in the CPython repository. It provides essential context about the CPython codebase, recommended tools, and best practices for maintaining engineering notebooks while working on Python runtime and standard library development.
---

# CPython Context

## Purpose

You are working in the CPython repository, which contains the implementation of the Python language runtime and standard library itself. This skill provides essential context and best practices for navigating and contributing to this codebase.

## Essential Tools

### GitHub CLI (`gh`)
- Use the `gh` tool to get information about issues or PRs in the repo
- Use the GraphQL API via `gh` when appropriate
- If `gh` is not found, ask the user to install it

### Ripgrep (`rg`)
- ALWAYS prefer using `rg` (ripgrep) rather than `find` or `grep`
- If `rg` is not found, ask the user to install it

### Other Required Tools
- `jq`: JSON processing
- `pre-commit`: Code quality checks

If any of these tools are not found, ask the user to install them.

## File Size Considerations

Source files in the CPython repo can be **very long**. Always check file sizes before reading entire files to determine if you really need to read the whole thing. Consider reading specific sections or using grep/search instead.

## Knowledge Expansion: Engineering Notebooks

### Branch and PR Tracking

ALWAYS load and maintain engineering notebooks when working on features or PRs:

- **For PRs**: Load or create `.claude/pr-{PR_NUMBER}.md` when you are told a PR number
- **For branches**: Load or create `.claude/branch-{branch_name_without_slashes}.md` when the current git branch is not `main`

### Notebook Maintenance

Keep the notebook file up to date as an engineering notebook of your learnings and project state:
- Update it as you work on a task
- Update it after you commit
- The goal is to make it easier to pick up and resume work later

### Structure Your Notebook

Include sections such as:
- Problem statement and goals
- Key findings and learnings
- Important file locations
- Design decisions made
- Testing strategy
- Current status and next steps

## Optional Developer Resources

### Developer Guide
If `REPO_ROOT/../devguide/` exists:
- `developer-workflow/` subdirectory contains detailed info on how to work on code
- `documentation/` subdirectory contains info on documentation practices

### PEPs (Python Enhancement Proposals)
- PEPs might exist in a `REPO_ROOT/../peps/` tree
- Reference relevant PEPs when working on features or changes

## Integration with Other Skills

This skill provides foundational context. When you need specific guidance on:
- **Code structure**: Use the `cpython-navigation` skill
- **Building**: Use the `cpython-build` skill
- **Testing**: Use the `cpython-testing` skill
- **Code style**: Use the `cpython-code-style` skill

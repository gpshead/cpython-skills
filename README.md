# CPython Skills Plugin

A Claude Code plugin providing specialized skills for working with the CPython repository - the reference implementation of the Python programming language.

## Overview

This plugin provides AI agents (Claude and others) with deep knowledge about CPython's codebase structure, build system, testing infrastructure, and coding standards. While expressed in Claude Skills format, they are designed to be usable by any AI agent, not just Claude.

## Skills Included

### 1. cpython-context
**When to use**: Working in the CPython repository, managing engineering notebooks

Provides essential context including:
- Recommended tools (gh, ripgrep, jq, pre-commit)
- File size considerations
- Engineering notebook management for PRs and branches as a task specific long term memory.
- Developer guide and PEP references

### 2. cpython-navigation
**When to use**: Finding code, understanding codebase structure, locating tests

Covers:
- Source code organization (Lib/, Modules/, Objects/, Python/, Include/)
- Test location and naming conventions
- Argument Clinic overview
- Scratch space usage
- Documentation structure

### 3. cpython-build
**When to use**: Configuring, building, or rebuilding CPython

Includes:
- Build directory setup
- Platform-specific configuration (Linux, macOS, Windows)
- Incremental builds
- Argument Clinic code generation
- Build verification and troubleshooting

### 4. cpython-testing
**When to use**: Running tests, collecting coverage, debugging test failures

Details:
- unittest-based testing (not pytest!)
- Running individual tests and test suites
- Using `--match` for test filtering
- Code coverage collection
- Test package handling
- Interactive debugging with tmux

### 5. cpython-code-style
**When to use**: Writing code, ensuring style compliance, preparing commits

Covers:
- PEP 7 (C code) and PEP 8 (Python code) compliance
- Trailing whitespace and file ending rules
- Type annotation policy (no annotations in Lib/!)
- Pre-commit hooks and patchcheck
- Documentation formatting

## Recommended Tools

While the skills are designed to work with or without these tools, having them installed can significantly improves the agentic development experience:

### ripgrep (`rg`)
**Why**: Significantly faster than `grep` or `find` for searching large codebases
- CPython has extensive C and Python source files
- ripgrep is optimized for source code searching with smart defaults
- Install: Most package managers have `ripgrep` (command is `rg`)

### GitHub CLI (`gh`)
**Why**: Streamlined access to PR and issue information
- Query PR details, comments, and status directly from command line
- Use GraphQL API for complex queries
- Install: https://cli.github.com/
- You must manually pre-authorize this in line with your comfort level. For example, you could use a fine grained GitHub access token if you want to limit it to read-only access or only some of your repos if you are afraid of giving less trustworthy agents write access to interact with the world.

### jq
**Why**: Process JSON responses from APIs and tools
- Parse and filter GitHub and other tool and API responses
- Process structured tool output
- Install: Available in most package managers

### pre-commit
**Why**: Automated code quality checks before commits
- Catches trailing whitespace, file endings, and syntax errors
- Runs configured linters and formatters
- Prevents common mistakes from being committed
- Install: `pip install pre-commit` or via package manager

**Note**: The skills gracefully handle missing tools, but will recommend installation when relevant tasks arise.

## Installation

### For Claude Code

1. Clone or copy this directory to your Claude Code plugins location
2. The plugin will be automatically discovered if placed in the right location
3. Claude will automatically invoke these skills when relevant

### For Other AI Agents

This plugin follows the [Claude Skills specification](https://simonwillison.net/2025/Oct/16/claude-skills/), making it usable by any AI agent that supports this format.

Each skill is a self-contained directory under `skills/` with a `SKILL.md` file containing:
- YAML frontmatter with `name` and `description`
- Markdown content with detailed instructions

Agents can:
1. Read the skill descriptions to determine when to use them
2. Load the skill content when relevant to the task
3. Follow the instructions within each skill

## Structure

```
cpython-skills/
├── plugin.json          # Plugin manifest
├── README.md           # This file
└── skills/             # Skills directory
    ├── cpython-context/
    │   └── SKILL.md
    ├── cpython-navigation/
    │   └── SKILL.md
    ├── cpython-build/
    │   └── SKILL.md
    ├── cpython-testing/
    │   └── SKILL.md
    └── cpython-code-style/
        └── SKILL.md
```

## Origin

This plugin was converted from the original `cpython/CLAUDE.local.md` file, which provided similar context as a monolithic Markdown document. The skills-based approach provides:

- **Modularity**: Each skill focuses on a specific domain
- **Discoverability**: AI agents can find relevant skills based on task descriptions
- **Reusability**: Skills can be used independently or together
- **Interoperability**: Works with any agent supporting Claude Skills format

## Usage Examples

When working with CPython, the AI agent will automatically:

1. Load `cpython-context` when starting work in the repo
2. Use `cpython-navigation` to find relevant source files
3. Apply `cpython-build` knowledge when compiling
4. Follow `cpython-testing` guidance when running tests
5. Enforce `cpython-code-style` rules when writing code

## Contributing

To extend these skills:

1. Add new skills in the `skills/` directory
2. Follow the SKILL.md format with YAML frontmatter
3. Write clear descriptions so agents know when to use the skill
4. Include examples and common workflows

## License

MIT

## References

- [Claude Code Plugins Documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- [Claude Code Skills Documentation](https://docs.claude.com/en/docs/claude-code/skills)
- [Simon Willison on Claude Skills](https://simonwillison.net/2025/Oct/16/claude-skills/)
- [CPython Developer Guide](https://devguide.python.org/)

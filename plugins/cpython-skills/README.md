# CPython Skills

This directory contains specialized skills for working with the CPython repository - the reference implementation of the Python programming language.

## Overview

These skills provide AI agents (Claude and others) with deep knowledge about CPython's codebase structure, build system, testing infrastructure, and coding standards. While expressed in Claude Skills format, they are designed to be usable by any AI agent, not just Claude.

## Skills Included

### 1. cpython-context
**When to use**: Working in the CPython repository, managing engineering notebooks

Provides essential context including:
- Recommended tools (gh, ripgrep, jq, pre-commit)
- File size considerations
- Engineering notebook management for PRs and branches as a task specific long term memory
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

## Skills Format

This plugin follows the [Claude Skills specification](https://simonwillison.net/2025/Oct/16/claude-skills/), making it usable by any AI agent that supports this format.

Each skill is a self-contained directory under `skills/` with a `SKILL.md` file containing:
- YAML frontmatter with `name` and `description`
- Markdown content with detailed instructions

## Adding a New Skill

To add a new skill to this plugin:

1. **Create a skill directory**: Add a new directory under `skills/` with a descriptive name using lowercase and hyphens (e.g., `cpython-debugging`)

2. **Create SKILL.md file**: In your skill directory, create a `SKILL.md` file with the following structure:

   ```markdown
   ---
   name: skill-name
   description: Brief description of when to use this skill
   ---

   # Skill Name

   [Detailed instructions and content here]
   ```

3. **Write clear instructions**: Include:
   - When to use the skill
   - Relevant commands and workflows
   - Examples and common patterns
   - Links to relevant documentation

4. **Follow existing patterns**: Look at the existing skills for examples of:
   - YAML frontmatter format
   - Content organization
   - Level of detail
   - Tone and style

## Structure

```
skills/
├── README.md                 # This file
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

## Usage

When working with CPython, AI agents will automatically:

1. Load `cpython-context` when starting work in the repo
2. Use `cpython-navigation` to find relevant source files
3. Apply `cpython-build` knowledge when compiling
4. Follow `cpython-testing` guidance when running tests
5. Enforce `cpython-code-style` rules when writing code

## Origin

This plugin was converted from the original `cpython/CLAUDE.local.md` file, which provided similar context as a monolithic Markdown document. The skills-based approach provides:

- **Modularity**: Each skill focuses on a specific domain
- **Discoverability**: AI agents can find relevant skills based on task descriptions
- **Reusability**: Skills can be used independently or together
- **Interoperability**: Works with any agent supporting Claude Skills format

## References

- [Claude Code Skills Documentation](https://docs.claude.com/en/docs/claude-code/skills)
- [Simon Willison on Claude Skills](https://simonwillison.net/2025/Oct/16/claude-skills/)
- [CPython Developer Guide](https://devguide.python.org/)

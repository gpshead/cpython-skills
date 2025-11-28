# CPython Skills Marketplace

A Claude Code marketplace providing specialized skills for working with the CPython repository - the reference implementation of the Python programming language.

## Overview

This marketplace hosts Claude Code plugins with AI-agent skills for CPython development. Currently includes one comprehensive plugin with multiple skills providing deep knowledge about CPython's codebase structure, build system, testing infrastructure, and coding standards.

## Installation

To use these skills in Claude Code within a CPython repository checkout:

1. Add the marketplace:
   ```
   /plugin marketplace add gpshead/cpython-skills
   ```

2. Install the plugin:
   ```
   /plugin install cpython-skills
   ```

3. In your CPython repository, ask Claude:
   ```
   write a CLAUDE.local.md that instructs you to load and use all of the cpython-skills plugin in this repo.
   ```

## Available Plugins

### CPython Skills

**Name**: `cpython-skills`
**Description**: Skills for working with the CPython repository - helping with building, testing, and contributing to Python's implementation

This plugin provides four specialized skills:

#### 1. cpython-codebase
**When to use**: Working in the CPython repository, understanding codebase structure, managing engineering notebooks

Provides:
- Recommended tools (`rg`, `gh`, `jq`)
- Source code organization (Lib/, Modules/, Objects/, Python/, Include/)
- Test location and naming conventions
- Argument Clinic overview
- Engineering notebook management for PRs and branches as task-specific long term memory
- Scratch space usage
- Developer guide and PEP references

#### 2. cpython-build-and-test
**When to use**: Configuring, building, rebuilding CPython, running tests, collecting coverage, debugging test failures

Includes:
- Build directory setup
- Platform-specific configuration (Linux, macOS, Windows)
- Incremental builds
- Argument Clinic code generation
- Build verification and troubleshooting
- unittest-based testing (not pytest!)
- Running individual tests and test suites
- Using `--match` for test filtering
- Code coverage collection
- Test package handling
- Interactive debugging with tmux

#### 3. cpython-code-style
**When to use**: Writing code, ensuring style compliance, preparing commits

Covers:
- PEP 7 (C code) and PEP 8 (Python code) compliance
- Trailing whitespace and file ending rules
- Type annotation policy (no annotations in Lib/!)
- Pre-commit hooks and patchcheck
- Documentation formatting

#### 4. cpython-docs
**When to use**: Working with CPython documentation, adding version markers, creating NEWS entries

Covers:
- Documentation in ReST format in Doc/ tree
- Documentation tooling setup and validation
- Version markers (always use `next`)
- NEWS file entries for bug fixes and features

## Recommended Tools

These tools improve the agentic development experience: `rg`, `gh`, `jq`

## Marketplace Structure

```
cpython-skills/
├── .claude-plugin/
│   └── marketplace.json    # Marketplace manifest
├── plugins/
│   └── cpython-skills/     # CPython skills plugin
│       ├── plugin.json     # Plugin manifest
│       └── skills/         # Individual skills
│           ├── cpython-codebase/
│           │   └── SKILL.md
│           ├── cpython-build-and-test/
│           │   └── SKILL.md
│           ├── cpython-code-style/
│           │   └── SKILL.md
│           └── cpython-docs/
│               └── SKILL.md
├── LICENSE
└── README.md               # This file
```

## For Plugin Developers

### Adding New Plugins to This Marketplace

To add a new plugin to this marketplace:

1. Create a new directory under `plugins/` with your plugin name
2. Add your plugin files (skills, commands, agents, etc.)
3. Create a `plugin.json` manifest in your plugin directory
4. Update `.claude-plugin/marketplace.json` to include your plugin entry

Example marketplace.json plugin entry:
```json
{
  "name": "your-plugin-name",
  "source": "./plugins/your-plugin-name",
  "description": "Brief description of what your plugin does"
}
```

### Skills Format

This marketplace follows the [Claude Skills specification](https://simonwillison.net/2025/Oct/16/claude-skills/), making plugins usable by any AI agent that supports this format.

Each skill is a self-contained directory with a `SKILL.md` file containing:
- YAML frontmatter with `name` and `description`
- Markdown content with detailed instructions

## Usage Examples

When working with CPython, the AI agent will automatically:

1. Load `cpython-codebase` when starting work in the repo
2. Apply `cpython-build-and-test` knowledge when compiling or running tests
3. Enforce `cpython-code-style` rules when writing code
4. Follow `cpython-docs` guidance when updating documentation

## Origin

This marketplace was created from the original CPython skills plugin, which was converted from the `cpython/CLAUDE.local.md` file. The marketplace-based approach provides:

- **Modularity**: Each skill focuses on a specific domain
- **Discoverability**: AI agents can find relevant skills based on task descriptions
- **Reusability**: Skills can be used independently or together
- **Extensibility**: Easy to add new plugins to the marketplace
- **Interoperability**: Works with any agent supporting Claude Skills format

## License

CC0 1.0 Universal

## References

- [Claude Code Plugin Marketplaces Documentation](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces)
- [Claude Code Plugins Documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- [Claude Code Skills Documentation](https://docs.claude.com/en/docs/claude-code/skills)
- [Simon Willison on Claude Skills](https://simonwillison.net/2025/Oct/16/claude-skills/)
- [CPython Developer Guide](https://devguide.python.org/)

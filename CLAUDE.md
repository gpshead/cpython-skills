# Claude Agent Instructions

This repository contains Claude Code skills for CPython development, packaged as a plugin marketplace.

## Repository Structure

- `plugins/cpython-skills/skills/` - Individual skill definitions (SKILL.md files)
- `.claude-plugin/marketplace.json` - Marketplace manifest
- `README.md` - User documentation and installation instructions

## For Agents Updating Skills

When modifying skills in this repository:

1. **Each skill is a directory** under `plugins/cpython-skills/skills/` containing a `SKILL.md` file
2. **SKILL.md format**: YAML frontmatter (`name`, `description`) followed by markdown instructions
3. **Keep skills focused**: Each skill should cover a specific domain (build, test, style, docs)
4. **Descriptions are critical**: The `description` is the primary triggering mechanism - include specific contexts and triggers
5. **No extraneous files**: Don't add README.md or other auxiliary files in skill directories
6. **Avoid duplication**: Information should live in one skill only, not repeated across skills
7. **Keep SKILL.md concise**: Under 500 lines; split to reference files if needed
8. **Update top-level README.md** if adding new skills or changing skill purposes

### Best Practices Reference

Load the [skill-creator](https://github.com/anthropics/skills/blob/main/skill-creator/SKILL.md) skill for comprehensive guidance on skill design patterns, progressive disclosure, and bundled resources.

## Official Anthropic Documentation

### Claude Skills
- [Agent Skills Overview](https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills/overview) - Full documentation
- [Introducing Agent Skills](https://www.anthropic.com/news/skills) - Announcement and overview
- [anthropics/skills](https://github.com/anthropics/skills) - Official skills repository and examples

### Claude Code Plugins & Marketplaces
- [Claude Code Plugins](https://www.anthropic.com/news/claude-code-plugins) - Plugin system announcement
- [Claude Code Overview](https://docs.anthropic.com/en/docs/claude-code/overview) - Main Claude Code docs

This repository packages Claude Code skills and slash commands for CPython development as a plugin marketplace. Those are the product; there is no code to build or run here.

## Repository Structure

- `plugins/cpython-skills/skills/<name>/SKILL.md` — skill definitions (YAML frontmatter + markdown)
- `plugins/cpython-skills/commands/<name>.md` — slash commands shipped with the plugin
- `plugins/cpython-skills/plugin.json` — plugin manifest (lists skills and commands)
- `.claude-plugin/marketplace.json` — marketplace manifest
- `README.md` — user-facing install and usage docs

## Editing Skills and Commands

- **One domain per skill** (build, style, docs, jit, dev). Don't repeat content across skills; cross-reference instead.
- **The `description` frontmatter is the trigger.** Be specific about *when* the skill applies, not just what it contains.
- **Keep `SKILL.md` under ~500 lines.** No README or auxiliary files inside skill directories.
- **When adding a skill or command**, register it in `plugin.json` and update the corresponding section of `README.md`.

The `skill-creator` plugin is enabled for this repo (see `.claude/settings.json`). Use its skill when authoring or restructuring skills here — it covers design patterns, progressive disclosure, description tuning, and evals. Source: [anthropics/claude-plugins-official/skill-creator](https://github.com/anthropics/claude-plugins-official/blob/main/plugins/skill-creator/skills/skill-creator/SKILL.md).

## Versioning

`plugins/cpython-skills/plugin.json` and `.claude-plugin/marketplace.json` versions are kept **in lockstep**. Bump both to the same value for any user-visible change.

## Validation

No test suite. Before committing, confirm both manifests parse: `jq . plugins/cpython-skills/plugin.json .claude-plugin/marketplace.json`

## References

- [Agent Skills Overview](https://docs.anthropic.com/en/docs/agents-and-tools/agent-skills/overview)
- [Claude Code Plugins](https://www.anthropic.com/news/claude-code-plugins)
- [Claude Code Overview](https://docs.anthropic.com/en/docs/claude-code/overview)

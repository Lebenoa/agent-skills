# agent-skills

Skills for AI coding agents, following the standard [skills layout](https://skills.sh): one directory per skill under `skills/`, each with a `SKILL.md` (YAML frontmatter + instructions).

## Skills

### [codebase-review](skills/codebase-review)

Review an entire codebase as it stands — no diff, no history, no fixed point. Partitions the repo into module slices, runs parallel read-only review sub-agents, and aggregates one deduplicated findings report grouped by severity (bug / risk / smell).

User-invoked only (`disable-model-invocation: true`): fires when you type its name, zero always-loaded context cost.

### [readme-pro-max](skills/readme-pro-max)

Generate, audit, or polish a project README. Data-driven: matches the project type against CSV tables of section skeletons, hero patterns, badge sets, tone rules, and code-snippet conventions.

Model-invoked: fires on "write/fix/improve my README".

## Install

```bash
npx skills add Lebenoa/agent-skills@codebase-review
```

Or copy/symlink manually into your agent's skills directory:

```
~/.agents/skills/codebase-review
```

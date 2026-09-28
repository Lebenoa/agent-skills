# agent-skills

Curated skills for AI coding agents — the standard [skills layout](https://skills.sh): one directory per skill under `skills/`, each with a `SKILL.md` (YAML frontmatter + instructions) and optional data files.

## Skills

| Skill | Fires | What it does |
|---|---|---|
| [codebase-review](skills/codebase-review) | when you type its name (user-invoked) | Reviews the whole repo as it stands — no diff — by slicing it into modules, running parallel read-only review sub-agents, and aggregating one deduplicated findings report (bug / risk / smell). |
| [readme-pro-max](skills/readme-pro-max) | automatically on "write/fix/improve my README" (model-invoked) | Generates or repairs a README by matching the project type against CSV tables of section skeletons, hero patterns, badge sets, tone rules, and code-snippet conventions. |

## Install

Any skill in this repo, by name:

```bash
npx skills add Lebenoa/agent-skills@<skill-name>
```

Or copy/symlink a skill directory into your agent's skills path manually:

```bash
git clone https://github.com/Lebenoa/agent-skills.git
ln -s "$(pwd)/agent-skills/skills/codebase-review" ~/.agents/skills/codebase-review
```

## Usage

Skills are invoked from your agent session, not the shell:

- **Model-invoked skills** fire on their own when a request matches the skill's description — say "improve this README" and readme-pro-max runs.
- **User-invoked skills** (`disable-model-invocation: true`) run only when you type the skill name — enter `codebase-review` in the session.

## Skill directory layout

```text
skills/<name>/
├── SKILL.md          # YAML frontmatter (name, description, flags) + workflow
├── agents/openai.yaml  # optional: display metadata
└── data/             # optional: CSV/lookup tables the workflow consults
```

Frontmatter keys: `name` (directory name), `description` (quoted; the model's trigger), `disable-model-invocation: true` for user-invoked-only skills.

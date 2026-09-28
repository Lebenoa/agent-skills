# codebase-review

Agent skill: review an entire codebase as it stands — no diff, no history. Partitions the repo into module slices, runs parallel read-only review sub-agents, and aggregates one deduplicated findings report grouped by severity (bug / risk / smell).

User-invoked only (`disable-model-invocation: true`): fires when you type its name, zero always-loaded context cost.

## Install

Copy or symlink this folder into your agent's skills directory:

```
~/.agents/skills/codebase-review
```

## Use

Type `codebase-review` in a session. Optionally state scope exclusions ("skip static/ and seed.surql"). The skill is read-only — it produces findings, not fixes.

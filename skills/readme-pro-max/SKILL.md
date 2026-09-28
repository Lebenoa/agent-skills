---
name: readme-pro-max
description: "Generate, audit, or polish a project README.md. Data-driven: matches the project type (CLI, library, app, API) against curated CSV tables of section skeletons, hero patterns, badge sets, tone rules, and code-snippet conventions, then writes or rewrites the README section by section. Use when the user asks to write, fix, improve, restructure, or review a README or project documentation front page."
---

Produce or repair a README by consulting the knowledge base under `data/`, never by freelancing structure. Every table row you adopt is a decision the data already made; the workflow's job is matching, not inventing.

## Workflow

### 1. Fingerprint the project

Identify, before writing anything: project type (CLI tool / library / application / API / monorepo / other), language + ecosystem, install mechanism, and the one-sentence "what it does." Source of truth is the code and manifests (`Cargo.toml`, `package.json`, `pyproject.toml`, route definitions, `--help` output). If an existing README claims something the code contradicts, the code wins.

Completion criterion: a four-field fingerprint (type, ecosystem, install path, one-liner) the user could correct in one line if wrong.

### 2. Choose the skeleton

Read `data/sections.csv`. Filter to rows where `project_type` matches the fingerprint, order by `priority`, and lay out the README as that section list — each section gets the row's `purpose` as its content contract. Sections marked `required=1` are non-negotiable; `required=0` sections are dropped unless their `trigger` condition holds.

### 3. Write the hero block

Read `data/hero_patterns.csv`. Pick the pattern whose `best_for` matches the fingerprint and audience (short scan vs. detailed pitch). The hero is: title, one-liner, badges, at most one visual (banner, screenshot, or demo GIF) — the pattern row specifies which. Write the one-liner under `data/tone_rules.csv` rules (see step 5).

### 4. Write install and usage from code

Read `data/code_style.csv`. Every fenced block obeys its rows: language tag always, first command runnable as-is (copy-paste test — run it if the project builds locally), one-liner before multi-line, output shown when it disambiguates. Derive install/usage commands from the manifest and entry points; a command invented from memory of "how projects like this usually install" is a bug.

### 5. Enforce tone

Read `data/tone_rules.csv`. Sweep every written line against the `avoid → instead` pairs. The rule class: claims carry evidence (numbers, behaviors, links); marketing adjectives are replaced by the spec that makes them true; feature lists describe what the user can *do*, not what the project *has*.

Completion criterion: no written sentence fails an `avoid → instead` pair; every claim traces to a code or manifest fact.

### 6. Audit pass (existing READMEs only)

When repairing, diff the old README against the new skeleton: list sections dropped (with reason), added, and rewritten. Preserve any existing content the data has no opinion on (contribution notes, credits, changelogs) unless it violates a tone rule. Never silently delete user-written sections — report them.

## Data files

| File | Columns | Consulted at |
|---|---|---|
| `data/sections.csv` | `project_type,section,priority,required,purpose,trigger` | step 2 |
| `data/hero_patterns.csv` | `pattern,structure,best_for,visual,example` | step 3 |
| `data/badge_sets.csv` | `badge,provider,url_template,applies_when` | step 3 |
| `data/tone_rules.csv` | `class,avoid,instead,example` | steps 3, 5 |
| `data/code_style.csv` | `rule,applies_to,detail,example` | step 4 |

Search the CSVs with grep/row filters; read only matching rows, not whole files into context.

## Output

The README is written to the project root as `README.md`. For an audit, the findings list (dropped/added/rewritten/preserved) precedes it. Length target: scannable in two minutes — the data files bound each section; exceed them only when the project genuinely needs it.

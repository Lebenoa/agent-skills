---
name: codebase-review
disable-model-invocation: true
description: Review an entire codebase as it stands — no diff, no history, no fixed point. Partitions the repo into module slices, runs parallel read-only review sub-agents, then aggregates one deduplicated report grouped by severity. Read-only: findings, not fixes.
---

Review every line of code in this repository as it exists now. There is no diff: the whole tree is the review target. Read-only — the deliverable is a findings report, and fixes happen only if the user asks afterward.

## 1. Bound the target

Enumerate the repo's code surface with the user's scope statement as the base. Classify every top-level directory or module as one of:

- **In scope** — hand-written source the project ships or runs.
- **Out** — vendored code, generated code, lockfiles, build output, committed seed/data dumps, third-party assets. Name them in the report so silence reads as deliberate exclusion, never oversight.

Completion criterion: a written scope list where every top-level entry is classified, and the count of in-scope source files is known.

## 2. Map, then partition

Build a one-screen map of the in-scope surface: entry points, module boundaries, shared types/contracts, data layer. Then cut it into **slices** — partitions a single reviewer can hold in context, typically one module or one route surface each, sized so a slice's review prompt fits comfortably in one sub-agent.

Two rules bind the partition:

- **Slices are self-contained**: each names its own files plus the shared contracts it must understand (which live in other slices — the sub-agent reads them but does not re-review them).
- **Add one cross-cutting slice** for what per-module slices structurally miss: duplicated logic across modules, inconsistent naming for the same concept, one module bypassing the shared layer another establishes, dead code reachable from nowhere.

Completion criterion: a slice list where every in-scope file appears in exactly one primary slice, and the cross-cutting slice states which concerns it owns.

## 3. Dispatch parallel review sub-agents

Spawn one read-only sub-agent per slice, in one batch. Each prompt carries: the slice's file list, the shared contracts it may read, and this brief:

> Review these files for correctness (logic errors, unhandled edge cases, error paths that fail open), security of every external input (validate where it enters, not where it is used), resource handling (allocation, unbounded growth, leaked state), and conformance to this repo's documented conventions. Cite `file:line` for every finding. Label each finding **bug** (defensible as wrong), **risk** (defensible as fragile), or **smell** (judgement call). Skip anything tooling already enforces. No fixes — findings only. Under 600 words.

The sub-agents run read-only. Their brief is the exhaustiveness bar: every file in the slice accounted for, every finding cited — a slice with zero findings reports that explicitly with the files it examined.

Completion criterion: every slice has returned a report that names its files and cites locations.

## 4. Aggregate

Merge the reports into one, in this order:

1. **Verify before promoting**: spot-check every **bug** finding against the cited lines before it enters the final report. A sub-agent claim you cannot confirm drops to **risk**; a claim the code contradicts is deleted with a note. Sub-agent output is a lead, not a fact.
2. **Deduplicate**: one defect reported by two slices (e.g. a shared validation gap each module inherits) appears once, listing all affected sites.
3. **Route cross-cutting findings to their locus**: a duplication is reported where the fix would land, not once per copy.

Present grouped by severity — **bug**, **risk**, **smell** — each finding as: location, what is wrong, why it is wrong, one-line direction for a fix. End with a count per severity and the single worst finding overall.

Do not merge severities to flatten the report, and do not re-rank smells above risks to seem balanced.

## Why read-only and sliced

A whole-repo review exceeds one context; slicing is how exhaustiveness survives the size, and the cross-cutting slice is how it survives the partition. Read-only keeps the review honest: the reviewer never softens a finding to spare a fix it would have to make.

# build-spec — Absorb spec-docs, Add Doc Update Phase

_Date: 2026-04-06_

## What Happened

The workflow had a handoff problem: build-spec would finish building, then the user had to run `/spec-docs` separately to update docs. spec-docs would re-read the build plan, summaries, and code to figure out what changed — duplicating work that build-spec had just done. The builder already had the freshest, most accurate picture of what changed. Making a separate skill re-derive that context was redundant and introduced drift risk.

## Reasoning

build-spec is the natural home for doc updates because:
1. It just touched every file — it knows exactly what changed
2. It has the target state summary loaded and verified against reality
3. Every context handoff between skills loses fidelity; fewer handoffs = less drift
4. The pipeline simplifies from 5 steps to 4 (write spec → plan → build+verify+docs → optional full audit)

The two-phase approve-then-edit pattern from spec-docs was preserved because doc changes shouldn't happen silently. The user needs to see what's being proposed and approve it — different from code changes which follow an explicit plan.

Doc reference tables (pre/post) mirror the code reference tables in spec-to-build, giving the builder a concrete inventory of what docs exist and what will need updating, rather than discovering this during the doc phase.

spec-docs is retired. `/docs` remains for full project-wide audits not scoped to a single build.

## Result

Pending — not yet tested with the updated skill.

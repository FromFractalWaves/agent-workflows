# spec-docs — Retired

_Date: 2026-04-06_

## What Happened

spec-docs was absorbed into build-spec v2. The doc update responsibility now lives in build-spec's Phase 3, which runs after building and verification.

## Why

spec-docs existed as a separate step because the original build workflow didn't include doc updates. When build-spec was created to handle the build+verify cycle, it became clear that the builder already has the freshest context about what changed. Making a separate skill re-derive that by reading build plans and summaries was redundant work with drift risk.

## What Was Preserved

- Two-phase propose-then-approve pattern for doc edits
- Verify against code, not just specs
- Preserve doc voice and scope
- Always check CLAUDE.md
- Don't do full audits (that's `/docs`)

## What Was Added

- Pre/post doc reference tables (inventoried during the reference read, not derived later)
- Hard boundary: deviations must be resolved before doc updates proceed

## Migration

- `/spec-docs` no longer exists as a skill
- Use `/build-spec` — doc updates are Phase 3 of that workflow
- Use `/docs` for full project-wide audits not scoped to a single build

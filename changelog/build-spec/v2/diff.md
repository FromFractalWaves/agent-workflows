# build-spec — Absorb spec-docs, Add Doc Update Phase

_Date: 2026-04-06_

## What Changed

**Added Phase 3: Update docs.** build-spec now owns the full cycle: build, verify, and update docs. This replaces the separate `/spec-docs` skill. The doc update phase uses the two-phase propose-then-approve pattern from spec-docs — propose changes, wait for user approval, then edit.

**Added doc reference tables to step 2.** During the reference read, build-spec now inventories pre-build doc references (docs describing the affected area) and post-build doc references (new docs needed). Always includes CLAUDE.md. These tables ground the doc update phase in what was actually found, not guesses.

**Restructured workflow into 4 phases.** Build → Verify → Update docs → Report. Each phase has a clear boundary. The verify-to-docs boundary is hard: deviations must be resolved before doc updates proceed.

**Updated description** to include doc updates.

**Updated workflow context** to remove spec-docs from the pipeline. The pipeline is now: spec-to-build → build-spec → docs (optional).

**Replaced "Don't update docs" rule** with a Documenting rules section covering: two-phase boundary, verify against code, don't expand scope, preserve voice, always check CLAUDE.md, don't do full audits.

**Updated report step** to include doc update summary and spec file cleanup list.

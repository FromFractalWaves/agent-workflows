# Multi-Step Spec Awareness — Update Outline

## Problem

spec-to-build assumes each spec is a standalone file in `specs/`. Some tasks are too large for a single spec. The indicator: the build plan has many steps and the test suite is thin relative to complexity.

## Solution: Multi-Step Specs

When a task is too big for one spec, it gets a subfolder:

```
specs/capture_transplant/
  overview.md          # Full picture
  step_01.md           # First unit of work
  step_01_buildplan.md # (generated)
  step_01_summaries.md # (generated)
  step_02.md           # Second unit — may be updated after step 01 completes
  step_03.md           # Rough until its turn comes
```

## Changes

1. Subfolder awareness in step 1: read overview.md, scan for completed step artifacts
2. Output files note: same naming convention, just scoped to the subfolder
3. Scope signal rule: flag thin test suites relative to plan complexity

## What does NOT change

- Standalone specs work exactly as before
- Output naming convention unchanged
- Skill rules, structure, and workflow unchanged
- No new output files

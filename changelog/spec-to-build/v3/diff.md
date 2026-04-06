# spec-to-build — Multi-Step Spec Awareness

_Date: 2026-04-06_

## What Changed

**Workflow step 1 (Read the spec)** gained a "Subfolder awareness" block. When the spec lives in a subdirectory of `specs/` rather than directly in it, the skill now:
- Reads `overview.md` in the same directory first (if it exists) to understand the broader task
- Scans for completed step artifacts (buildplans, summaries, misalignments from previous steps) and uses them as context — avoiding re-planning completed work and treating previous target state summaries as the current step's starting point

**Output files section** added a note clarifying that for specs in subdirectories, outputs are written as siblings in that subdirectory with no change to the naming convention.

**Rules section** added a new "Watch for scope signals" rule: if the build plan has many steps and the test suite feels thin relative to complexity, flag it to the user and suggest breaking the spec into a multi-step subfolder.

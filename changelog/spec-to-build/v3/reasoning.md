# spec-to-build — Multi-Step Spec Awareness

_Date: 2026-04-06_

## What Happened

Some tasks are too large for a single spec. The symptom: the build plan has many steps and the generated test suite is thin relative to the complexity — the agent spends its attention budget on implementation steps and treats testing as an afterthought. This is a scope signal. The solution is multi-step specs in subdirectories, but the skill had no awareness of this structure.

## Reasoning

Rather than changing the skill's core workflow or output format, this adds a single new behavior: subfolder awareness. When the spec is in a subdirectory of `specs/`, the skill reads the overview and previous step artifacts before proceeding with its normal workflow. This gives the agent the context it needs to plan the current step without re-planning completed work.

The scope signal rule makes the skill self-diagnosing — when the output looks like a scope problem, it tells the user instead of silently producing a thin plan.

Key design choice: no new output files, no new workflow steps. The existing workflow handles everything once the agent has the right context loaded. The change is purely about what gets read before step 2 begins.

## Result

Pending — not yet tested with the updated skill.

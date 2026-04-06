# spec-to-build v3 — Multi-Step Spec Reasoning

_Date: 2026-04-06_

## What Happened

During the capture transplant — the first real use of the repo-transplant workflow — the spec extracted from the albatross1a repo was too large for a single spec-to-build pass. The build plan it generated had an excessive number of steps, and the test suite was noticeably thin relative to the complexity of the plan.

The thin test suite was the diagnostic signal. The agent wasn't being lazy — it was stretched. When a spec consumes the agent's full attention budget on implementation planning, testing becomes an afterthought. The tests come out generic or incomplete because the agent has already spent its depth on figuring out what to build. This is a scope problem, not a quality problem.

## The Fix

Break oversized specs into a subfolder with an overview and numbered steps:

- `overview.md` holds the full picture — what the task is, how the steps connect, what the end state looks like
- `step_01.md`, `step_02.md`, etc. are individual work orders sized for one spec-to-build pass each
- Later step specs are living documents — they get updated as earlier steps complete and the reality of what got built changes what comes next

The skill needs one new behavior: when a spec lives in a subdirectory of `specs/`, read the overview first and scan for completed step artifacts (buildplans, summaries) to understand what's already been done. This prevents re-planning completed work and gives the agent context about where the current step fits.

## Why Not Just Write Smaller Specs?

The initial spec was written from a source repo exploration — it captured the full scope of what the capture module needs to do. That's the right level of detail for understanding the task. But it's the wrong level of detail for executing it. The overview preserves the full picture while the step specs make it buildable.

Updating downstream step specs after each build is intentionally not rigid. Some builds won't change anything about later steps. Some will. The workflow is: update the next step before you run it, check the overview still holds, let the rest stay rough until their turn. Trying to keep all steps perfectly synchronized would be busywork — more time maintaining specs than building. The misalignments step catches stale assumptions anyway.

## Scope Signal

The heuristic that triggered this change: if the generated test suite is thin relative to the build plan's complexity, the spec has too much scope. This is observable and concrete — you don't need to guess whether a spec is too big. The test quality tells you.

## Result

Pending — outline written, skill update not yet applied.
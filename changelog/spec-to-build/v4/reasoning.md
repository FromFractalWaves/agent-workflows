# spec-to-build — Add Post-Build Verification Step

_Date: 2026-04-06_

## What Happened

In multi-step spec workflows, the build for step N sometimes deviates from the spec — a method named `send()` instead of `emit()`, a field named differently, a class split into two. The next step's spec still references the original names. Without a verification pass, the build plan for step N+1 works from stale assumptions, causing misaligned plans and wasted effort fixing name mismatches mid-build.

## Reasoning

The gap is between "plan" and "next plan." The skill already handles spec-vs-repo misalignment (step 4), but that only runs at planning time against the repo's *pre-build* state. Nothing checks the *post-build* state against the spec's expectations.

A post-build verification step closes this loop. It runs after the build completes, reads back what was actually created, and diffs it against the spec's naming and interface assumptions. This is cheaper than discovering the drift mid-build on the next step.

The deviation report is a separate file (like misalignments) rather than inline in the build plan because the user needs to make a decision: fix the code to match the spec, or update remaining specs to match the code. That's a human judgment call, not something the skill should automate.

The subfolder awareness update ensures that if the user chooses to keep the code as-is and move forward, the next step's planning will use the actually-built names rather than the stale spec names.

## Result

Pending — not yet tested with the updated skill.

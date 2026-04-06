# spec-to-build — Move Post-Build Verification to build-spec

_Date: 2026-04-06_

## What Happened

The post-build verification step added in v4 made more sense as part of a dedicated build skill. spec-to-build is the planner — it shouldn't also own post-build verification, which only runs after someone else executes the plan.

## Reasoning

Clean separation of concerns: spec-to-build plans, build-spec executes and verifies. The planner still reads deviation files from previous steps (it needs that context), but it no longer produces them. This avoids the awkward "this step runs later when the user comes back" framing that v4 had.

## Result

Pending — not yet tested with the updated skill.

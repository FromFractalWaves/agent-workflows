# Iterate — Created

_Date: 2026-04-06_

## What It Does
Takes a testing goal spec from `specs/` and works toward it iteratively. When something breaks, it recognizes the bug, makes the minimal fix, updates affected docs, and asks the user before continuing. When the session stops, it writes a session report that captures everything needed to resume in a future conversation.

## Why It Exists
Without this skill, bug-driven development sessions accumulate context without checkpoints. Multiple bugs get fixed in a single pass, details get lost, docs drift, and when context grows too large and the session ends, there's no structured record of what happened. Resuming in a new session means replaying discovery work.

The skill enforces a discipline: one bug, one fix, one checkpoint, one confirmation. Session reports bridge the gap between conversations.

## Design Decisions
- **One bug at a time with hard stops.** The user controls the pace. Each checkpoint is a natural boundary where the session can end without losing information.
- **Session reports as the persistence mechanism.** Written to `specs/` alongside the goal spec. The next session reads the most recent report to resume. No external tracking system needed.
- **Targeted doc updates, not full audits.** Each checkpoint only updates docs invalidated by that specific fix. Full audits are the `/docs` skill's job.
- **Minimal fixes only.** The skill is about reaching the testing goal, not improving the codebase. This keeps scope tight and prevents fix-driven refactoring.
- **Spec-driven entry point.** Takes a goal spec from `specs/`, consistent with the existing spec-to-build/build-spec workflow. The difference is these specs describe testing goals, not build plans.

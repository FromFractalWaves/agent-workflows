---
name: iterate
description: Work toward a testing goal from specs/, fixing bugs one at a time with doc updates and user checkpoints between each fix. Usage: /iterate specs/my-goal.md
---

You are the Iterator. Your job is to work toward a testing goal one bug at a time, with documentation checkpoints and user confirmation between each fix. You never batch bugs. You never skip checkpoints. You stop when the user says stop.

The user will provide a path to a goal spec (e.g., `specs/my-goal.md`).

## Workflow context

This skill is for empirical, bug-driven development. You try the thing, something breaks, you fix it, you document it, you ask if you should keep going. The user controls the pace — sessions can stop at any natural boundary without losing context.

Session reports bridge conversations. When work stops, the report captures everything needed to resume — no context replay required.

## Your workflow

### Phase 0: Orient

1. **Read the goal.** Read the spec file. Understand the success condition — what does "working" look like?

2. **Check for prior sessions.** Look for existing session reports (`specs/[goal-name]_session_*.md`). If any exist, read the most recent one. Resume from where it left off — don't re-discover bugs that were already found and fixed.

### Phase 1: Attempt

3. **Try the thing.** Do what the spec describes — run the code, hit the endpoint, execute the test, whatever "trying it" means for this goal. Observe what happens.

4. **Something breaks.** Recognize it as a bug. Stop and name it clearly:
   - **What broke?** The specific error, failure, or unexpected behavior.
   - **What was expected?** What should have happened according to the goal.
   - **What actually happened?** The actual output, error message, or behavior.

### Phase 2: Fix

5. **Fix the bug.** Make the minimal fix. Don't refactor, don't improve adjacent code, don't add scope — just fix what broke.

### Phase 3: Checkpoint

6. **Update supporting files.** Before continuing, update anything affected by the fix:
   - Docs that describe changed behavior or interfaces
   - `CLAUDE.md` if the fix changed architecture, conventions, or running instructions
   - Any other supporting files that now describe something that's no longer true

   This is targeted — only update what this specific fix invalidated. Not a full audit.

7. **Ask the user.** Report what you found and fixed, then ask: "Continue working toward the goal?" **Stop and wait.** Do not continue without explicit confirmation.

### Phase 4: Loop or Stop

8. **If the user says continue:** Go back to step 3. Try the thing again.

9. **If the user says stop:** Write a session report (see below) and summarize progress.

10. **If the goal is achieved:** No more bugs — the thing works. Say so, do a final targeted doc pass on everything that changed during the session, and write a final session report marking the goal as reached.

## Session report format

When stopping (step 9) or completing (step 10), write to `specs/[goal-name]_session_[YYYY-MM-DD].md`:

```markdown
# Session Report: [Goal Name]

_Date: [today's date] — Spec: `specs/[goal-name].md`_

## Goal
[What we were trying to achieve — the success condition]

## Bugs Found & Fixed
- **[Bug 1]:** [What broke, what the fix was, what files changed]
- **[Bug 2]:** ...

## Current State
[What works now. What you can do that you couldn't before this session.]

## Still Broken / Not Yet Reached
[What's still failing or untested. The next bug or obstacle if known. "None — goal achieved" if complete.]

## Files Changed
[List of files modified during this session and why]

## Resumption Notes
[Anything the next session needs to know — workarounds in place, order of operations, gotchas discovered. "N/A — goal complete" if finished.]
```

If multiple sessions happen on the same date, append a counter: `_session_2026-04-06_2.md`.

## Rules

- **One bug at a time.** Fix it, document it, checkpoint. No batching multiple fixes between checkpoints.
- **Minimal fixes only.** The goal is to reach the testing target, not to improve the codebase. Fix what broke, nothing more.
- **Don't skip the checkpoint.** Context grows and details get lost — the checkpoint is the whole point of this skill.
- **Don't continue without confirmation.** Step 7 is a hard stop. Wait for the user.
- **Read prior session reports.** When starting, always check for and read existing reports. Don't repeat solved work.
- **Keep session reports honest.** Write what actually happened, not what was supposed to happen. If a fix was a workaround, say so.
- **Targeted doc updates only.** Update what this fix invalidated. Don't do a full doc audit — that's the `/docs` skill's job.

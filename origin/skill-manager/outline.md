# skill-manager Skill — Outline

## What It Does

A meta-skill for creating new skills, updating existing skills, and documenting every change with reasoning. This is the skill that manages all other skills.

Three modes: `create`, `update`, `changelog`.

---

## Mode: Create

`/skill-manager create [name]`

Scaffolds a new skill from scratch.

1. Ask what the skill should do — what task, what inputs, what outputs
2. Ask what failure modes to guard against — what has gone wrong without this skill?
3. Read existing skills in `.claude/skills/` to understand conventions and patterns
4. Write the skill file to `.claude/skills/[name]/SKILL.md`
5. Save a copy as `v1` in the workflow repo's `skills/` directory (if configured)
6. Write an initial changelog entry: what the skill does, why it was created, what problem it solves

---

## Mode: Update

`/skill-manager update [name]`

Guided skill update with automatic change capture.

1. Read the current skill file from `.claude/skills/[name]/SKILL.md`
2. Save a snapshot of the current version (for diff)
3. Ask: what behavior prompted this change? What did the agent do wrong?
4. Ask: what should change? (Or accept a description and propose the edit)
5. Apply the update to the skill file
6. Generate a diff between old and new
7. Automatically write a changelog entry (see Changelog mode)

---

## Mode: Changelog

`/skill-manager changelog [name]`

Documents a skill change that was already made (for cases where you edited the skill manually or in another session).

1. Read the current skill file
2. Get the previous version via `git diff` or a saved snapshot
3. Ask: what agent behavior prompted the change?
4. Ask: what was the expected result of the change?
5. Write a changelog entry to the configured location

### Changelog Entry Format

```markdown
# [Skill Name] — [Short Description of Change]

_Date: [date]_

## What Happened
[The specific agent behavior that prompted the change]

## The Diff
[Summary of what changed in the skill — not a raw diff, but a human-readable description of what was added/removed/modified]

## Reasoning
[Why this change and not a different one. What principle or pattern does it enforce?]

## Result
[Did it fix the behavior? Partially? Unknown yet? To be updated after testing.]
```

---

## Paths

- Skills live in each repo's `.claude/skills/`
- Changelogs write to `~/projects/agent-workflows/changelog/[skill-name]/`
- Skill snapshots (for diffing) save to `~/projects/agent-workflows/skills/`

All paths are hardcoded. This is a personal tool, not a distributed one.

---

## What It Does NOT Do

- Doesn't evaluate skill quality — that's your job after running the skill and seeing results
- Doesn't auto-detect when a skill needs updating — you decide when to run it
- Doesn't modify any project code — only touches skill files and changelog entries

---

## Why This Exists

The feedback loop for improving skills is: run → observe failure → identify behavior → update skill → document change. Steps 1-3 are manual (you have to notice the problem). Steps 4-5 are where this skill helps — it handles the mechanics of updating and documenting so you don't skip the documentation step when you're in the middle of building.

The changelog is the highest-value output. A skill without a changelog is a black box — you know what it does but not why each rule exists. The changelog turns skills into case study collections that other people can learn from.
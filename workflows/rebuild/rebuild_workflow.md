# Rebuild Workflow

A repeatable process for cleanly rebuilding broken or drifted components using Claude Code — instead of debugging, rebuild from a clean spec.

---

## When to use this

- A component is broken after a restructure
- Something has drifted from intended design
- Debugging is leading to regression loops — fixing one thing breaks another
- You'd rather rebuild against the current codebase than patch the old implementation

---

## Step 1: Generate a reference doc

Use `/plan` in Claude Code to explore the system end-to-end before touching anything.

**Prompt template:**
```
ok here is the /plan i want you to look at the docs and code base and closely examine 
how [FEATURE] works, from [ENTRY POINT] through [INTERMEDIATE LAYERS] to [OUTPUT/ENDPOINT]. 
then explain how it should all work end-to-end in a doc you will write 
as specs/[feature]_fix_info.md
```

**What it does:**
- Spins up parallel explore agents to trace the full flow
- Writes a comprehensive reference doc to `specs/[feature]_fix_info.md`
- No code changes — pure exploration and documentation

**Keep this doc.** It won't go stale if you don't change the feature. It saves the full exploration pass if you need to come back later.

---

## Step 2: Write the spec

Write a clean rebuild spec in Claude chat (not Claude Code). Reference the fix_info doc.

**Key things the spec must include:**
- Current state (what exists, what's broken, what's working)
- What's being rebuilt (routes, modules, functions — whatever the scope is)
- Data shapes and interfaces (point to fix_info doc, don't duplicate)
- Expected behavior — inputs, outputs, edge cases, error states
- What the spec does NOT include (scope boundary)

**Prompt to Claude chat:**
> "do you have enough to write the spec or do you need anything?"

Drop the spec into `specs/[feature]_rebuild.md`.

---

## Step 3: Run the skill

In Claude Code:
```
/spec-to-build specs/[feature]_rebuild.md
```

This produces:
- `specs/[feature]_rebuild_misalignments.md` — review this first
- `specs/[feature]_rebuild_buildplan.md` — the build plan

**Review misalignments before proceeding.** They catch assumptions in the spec that don't match reality. If something major is wrong, update the spec and re-run.

---

## Step 4: Pre-flight

Ask Claude Code:
```
any comments, suggestions, or advice before i run?
```

It will flag edge cases, scope the steps, and suggest whether a full rewrite or targeted edit is better.

---

## Step 5: Build

Let Claude Code execute the build plan. Verify the result.

**If it fails:** `git checkout .` then `git clean -fd` to revert. Review what went wrong, update the spec or build plan, try again on a branch.

---

## Change Notes

Keep a running `specs/change_notes.md` for small issues you notice during testing. These feed into future specs or can be batched into a corrections pass.

---

## File Conventions

| File | Purpose |
|------|---------|
| `specs/[feature]_fix_info.md` | Reference doc — end-to-end system explanation |
| `specs/[feature]_rebuild.md` | Spec — intent and design |
| `specs/[feature]_rebuild_misalignments.md` | Auto-generated — review before building |
| `specs/[feature]_rebuild_buildplan.md` | Auto-generated — Claude Code executes this |
| `specs/change_notes.md` | Running list of small issues to fix |

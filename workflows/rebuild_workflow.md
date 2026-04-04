# UI Rebuild Workflow

A repeatable process for cleanly rebuilding broken or drifted UI pages using Claude Code.

---

## When to use this

- A page is broken after a restructure
- A page has drifted from intended design
- You want to rebuild a page in the context of the current UI rather than debug the old one

---

## Step 1: Generate a reference doc

Use `/plan` in Claude Code to explore the system end-to-end before touching anything.

**Prompt template:**
```
ok here is the /plan i want you to look at the docs and code base and closely examine 
how [FEATURE] works, from [DATA/ENTRY POINT] through the API endpoints to the [PAGE] 
in the webui. then explain how it should all work end-to-end in a doc you will write 
as specs/[feature]_fix_info.md
```

**What it does:**
- Spins up parallel explore agents (data/API side + web UI side)
- Writes a comprehensive reference doc to `specs/[feature]_fix_info.md`
- No code changes — pure exploration and documentation

**Keep this doc.** It won't go stale if you don't change the feature. It saves the full exploration pass if you need to come back later.

---

## Step 2: Write the spec

Write a clean rebuild spec in Claude chat (not Claude Code). Reference the fix_info doc.

**Key things the spec must include:**
- Current state (what exists, what's broken, what's working)
- Route(s) being rebuilt
- Data shapes (point to fix_info doc, don't duplicate)
- Layout and behavior per route
- Error/loading/empty states — all three
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

Let Claude Code execute the build plan. Run the dev server and verify manually.

**If it fails:** `git checkout .` then `git clean -fd` to revert. Review what went wrong, update the spec or build plan, try again on a branch.

---

## Change Notes

Keep a running `specs/change_notes.md` for small UI issues you notice during testing (e.g. "Albatross logo in top left should link to `/`"). These feed into future specs or can be batched into a corrections pass.

---

## File Conventions

| File | Purpose |
|------|---------|
| `specs/[feature]_fix_info.md` | Reference doc — end-to-end system explanation |
| `specs/[feature]_rebuild.md` | Spec — intent and design |
| `specs/[feature]_rebuild_misalignments.md` | Auto-generated — review before building |
| `specs/[feature]_rebuild_buildplan.md` | Auto-generated — Claude Code executes this |
| `specs/change_notes.md` | Running list of small UI issues to fix |

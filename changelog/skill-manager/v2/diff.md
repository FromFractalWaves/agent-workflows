# Skill Manager — Nested Changelog Structure

_Date: 2026-04-04_

## What Changed

Changelog entries now write to `changelog/[name]/v[N]/` as a directory containing separate files, instead of a single `changelog/[name]/[name]-update-v[N].md` file.

**Standard files per version:**
- `diff.md` — what changed in the skill
- `reasoning.md` — what prompted the change and why this approach

**Optional files:**
- `outline.md` — planning notes before the update
- `idea.md` — initial idea or motivation

Updated all three modes (create, update, changelog) to use the new directory structure. Updated the Paths table to reflect `changelog/[name]/v[N]/`.

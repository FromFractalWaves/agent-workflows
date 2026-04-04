# Skill Manager — Nested Changelog Structure

_Date: 2026-04-04_

## What Happened

The original changelog structure wrote one file per skill update (`[name]-update-v[N].md`). This crammed the diff, reasoning, and context into a single document. When the user wanted to include additional artifacts alongside a changelog entry — like an outline written before the update or an idea file — there was no natural place for them. A flat directory of prefixed files (`stb-v2-diff.md`, `stb-v2-reasoning.md`, etc.) would get noisy fast.

## Reasoning

Each skill update is a unit of change with multiple facets: what changed (diff), why it changed (reasoning), and optionally what planning happened beforehand (outline/idea). A per-version directory groups these naturally — you see each update as a cohesive unit when browsing. The filenames stay simple and consistent across all skills because the version context comes from the directory path, not the filename.

This also aligns with how the `skills/` directory already works — versioned snapshots sit in `skills/[name]/`, so `changelog/[name]/v[N]/` mirrors that pattern.

## Result

Pending — not yet tested with the updated skill.

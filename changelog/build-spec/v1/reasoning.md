# build-spec — Created

_Date: 2026-04-06_

## What It Does
Executes a build plan produced by `/spec-to-build` step by step, then runs a post-build verification pass comparing what was built against what the spec described. Outputs a `_deviations.md` report if naming, interfaces, or structure diverged from the spec. Designed to slot between `/spec-to-build` (planning) and `/spec-docs` (documentation) in the workflow pipeline.

## Why It Exists
The spec-to-build workflow had a gap between planning and verification. The `/spec-to-build` skill produces the plan, but the actual building was unstructured — no skill enforced following the plan faithfully or checking the result. In multi-step specs, this caused silent drift: a method named differently, a class split in two, an interface changed. Later steps would plan against stale spec names, causing compounding misalignment. The post-build verification step was initially added to spec-to-build (v4), but it makes more sense as part of a dedicated build skill that owns the full execute-and-verify cycle.

## Design Decisions
- **Kept it basic.** The skill's core job is "follow the plan, then check your work." No complex orchestration — just disciplined execution and honest reporting.
- **Included workflow context section.** The builder needs to understand where it sits in the pipeline (spec-to-build → build-spec → spec-docs → docs) so it knows what's its job and what isn't (e.g., don't update docs).
- **Deviation report is the key output.** The build plan's steps produce code, but the deviation report is what prevents multi-step specs from going off the rails. Made it a hard rule: don't skip the verification.
- **Subfolder awareness inherited from spec-to-build.** Multi-step specs need the builder to read previous deviations and use actually-built names, not stale spec names.

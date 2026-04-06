---
name: build-spec
description: Execute a build plan from spec-to-build, then verify the build matches the spec. Usage: /build-spec specs/my-feature.md
---

You are the Builder. Your job is to execute a build plan produced by `/spec-to-build` and verify that what you built matches what the spec described.

The user will provide a path to the original spec file (e.g., `specs/foo.md`). You find the associated outputs by convention:
- `specs/foo_buildplan.md` — the actionable build plan (required)
- `specs/foo_summaries.md` — current state and target state summaries
- `specs/foo_misalignments.md` — what the spec got wrong about the repo (if exists)

If only a buildplan path is given (e.g., `specs/foo_buildplan.md`), derive the spec path by removing the `_buildplan` suffix.

## Workflow context

This skill is one step in a larger workflow:

1. **Write a spec** — raw feature description, can be rough or AI-generated, dropped in `specs/`
2. **`/spec-to-build`** — aligns the spec with the repo, produces a build plan, summaries, and misalignment report
3. **`/build-spec`** (this skill) — executes the build plan and verifies the result
4. **`/spec-docs`** — updates affected documentation using the spec outputs as context
5. **`/docs`** — full documentation audit (optional, for larger changes)

The summaries file is particularly useful during building: the **current state** section describes how things work before you start, and the **target state** section is the "done" picture you're building toward.

## Your workflow

1. **Read the build plan and context.** Read the build plan, summaries, and misalignments (if any). Understand the goal, the references, and every step.

   **Subfolder awareness:** If the spec lives in a subdirectory (e.g., `specs/capture_transplant/step_01.md`):
   - Read `overview.md` in the same directory if it exists
   - Read previous step artifacts — especially `*_deviations.md` files. If a previous step's build deviated from its spec, use the *actually built* names, not the original spec names.

2. **Read the references.** Read every file listed in the build plan's pre-build references table. Confirm they exist and understand what they contain. This is your working context.

3. **Execute the plan.** Work through the build plan steps in order. For each step:
   - Read the files listed for that step before modifying them
   - Follow the plan's instructions — don't add scope, don't skip steps
   - Be concrete: implement what the plan describes, using the naming and patterns specified
   - After each step, verify it works in isolation before moving on

4. **Post-build verification.** After all steps are complete, verify that what you built matches what the spec described. This catches drift between spec language and implementation reality.

   - Read back the files you created or modified
   - Compare against the spec's naming: function/method names, class names, field names, module paths, route paths, event names
   - Compare against the spec's interface expectations: signatures, parameters, return types, config keys
   - Check the target state summary — does the built system match the "done" picture?

   If there are deviations, write them to `specs/foo_deviations.md`. Format:

   ```markdown
   # Deviations: [Title]

   _Spec: `specs/foo.md` — verified against build on [date]_

   ## [Category: Naming / Interface / Structure]
   - **Spec expected:** [name, signature, or structure from the spec]
   - **Actually built:** [what the code actually uses]
   - **Impact:** [which remaining step specs reference the original name/interface]
   - **Suggested fix:** [fix the code / update step N specs / no action needed]
   ```

   Omit this file if the build matches the spec exactly.

5. **Report.** Summarize what was built, what tests were added, and whether any deviations were found. If deviations exist, the user needs to decide: fix the code to match the spec, or update remaining step specs to match the code. This must happen before moving to the next step.

## Rules

- **Follow the plan.** The build plan is the spec — don't reinterpret, don't add scope, don't skip steps. If the plan says to create a function called `emit()`, create it called `emit()`, not `send()`.
- **Read before writing.** Always read files before modifying them. The references table tells you what to read, but check the actual content.
- **Respect existing patterns.** The build plan should already account for project conventions, but if you notice a conflict, follow the project's existing patterns over the plan and note the deviation.
- **Don't skip the verification.** The post-build check exists because drift between spec and build is the #1 source of pain in multi-step specs. Always run it.
- **Be honest about deviations.** If you had to name something differently, split a class, or change an interface — that's fine, but flag it. Silent drift causes compounding problems in later steps.
- **Don't update docs.** That's the `/spec-docs` skill's job. Focus on code and tests.

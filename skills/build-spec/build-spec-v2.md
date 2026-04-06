---
name: build-spec
description: Execute a build plan from spec-to-build, verify the build matches the spec, and update affected docs. Usage: /build-spec specs/my-feature.md
---

You are the Builder. Your job is to execute a build plan produced by `/spec-to-build`, verify that what you built matches the spec, and update all affected documentation.

The user will provide a path to the original spec file (e.g., `specs/foo.md`). You find the associated outputs by convention:
- `specs/foo_buildplan.md` — the actionable build plan (required)
- `specs/foo_summaries.md` — current state and target state summaries
- `specs/foo_misalignments.md` — what the spec got wrong about the repo (if exists)

If only a buildplan path is given (e.g., `specs/foo_buildplan.md`), derive the spec path by removing the `_buildplan` suffix.

## Workflow context

This skill is one step in a larger workflow:

1. **Write a spec** — raw feature description, can be rough or AI-generated, dropped in `specs/`
2. **`/spec-to-build`** — aligns the spec with the repo, produces a build plan, summaries, and misalignment report
3. **`/build-spec`** (this skill) — executes the build plan, verifies the result, and updates affected docs
4. **`/docs`** — full project-wide documentation audit (optional, for larger changes or periodic sweeps)

The summaries file is particularly useful during building: the **current state** section describes how things work before you start, and the **target state** section is the "done" picture you're building toward.

## Your workflow

### Phase 1: Build

1. **Read the build plan and context.** Read the build plan, summaries, and misalignments (if any). Understand the goal, the references, and every step.

   **Subfolder awareness:** If the spec lives in a subdirectory (e.g., `specs/capture_transplant/step_01.md`):
   - Read `overview.md` in the same directory if it exists
   - Read previous step artifacts — especially `*_deviations.md` files. If a previous step's build deviated from its spec, use the *actually built* names, not the original spec names.

2. **Read the references.** Read every file listed in the build plan's pre-build references table. Confirm they exist and understand what they contain. This is your working context.

   **Build the doc reference tables.** While reading references, inventory the documentation landscape:

   **Pre-build doc references** — docs that describe the affected area now:
   | Doc | What it covers | What will change |
   |-----|---------------|-----------------|
   | `path/to/doc.md` | [what this doc describes] | [what parts are affected by this build] |

   Always include `CLAUDE.md` — it almost always needs updating after significant work.

   **Post-build doc references** — new docs this build will require (if any):
   | Doc | What it will cover | Why it's needed |
   |-----|-------------------|-----------------|
   | `path/to/new_doc.md` | [description] | [reason] |

3. **Execute the plan.** Work through the build plan steps in order. For each step:
   - Read the files listed for that step before modifying them
   - Follow the plan's instructions — don't add scope, don't skip steps
   - Be concrete: implement what the plan describes, using the naming and patterns specified
   - After each step, verify it works in isolation before moving on

### Phase 2: Verify

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

   Omit this file if the build matches the spec exactly. If deviations exist, tell the user and resolve them before proceeding to doc updates.

### Phase 3: Update docs

5. **Propose doc updates.** Using your doc reference tables and knowledge of what was actually built (not just planned), identify every doc that needs updating.

   For each affected doc:
   - Read the current content
   - Compare against the target state — the doc should describe the system as it exists now
   - Verify claims against the actual code, not just the spec (the code is what was built; the spec is what was intended)

   Present a structured change plan:

   For each affected file:
   - The file path
   - What is currently wrong or outdated (quote the specific text)
   - What it should say instead
   - Why (what changed that makes this necessary)

   Also list:
   - Docs that were checked and need no changes
   - Which spec files can be cleaned up after docs are updated (the original spec, build plan, summaries, misalignments, deviations)

   **Stop here and wait for the user to approve before making any doc edits.**

6. **Execute doc updates.** After user approval, update each doc:
   - Replace descriptions that match the old state with descriptions matching the current state
   - Add new files/components/routes/types that were created
   - Remove or update references to things that changed
   - Update architecture descriptions, file trees, running instructions
   - Match the existing voice and style of each doc

   **CLAUDE.md always gets a pass:**
   - Current State section — reflect what's new
   - Architecture sections — add new files, update descriptions of modified files
   - Running instructions — if anything changed
   - Docs table — if new docs were added

### Phase 4: Report

7. **Report.** Summarize:
   - What was built (code and tests)
   - Whether any deviations were found and how they were resolved
   - What docs were updated
   - Which spec files can now be cleaned up (list them — the user decides when to delete)

## Rules

### Building
- **Follow the plan.** The build plan is the spec — don't reinterpret, don't add scope, don't skip steps. If the plan says to create a function called `emit()`, create it called `emit()`, not `send()`.
- **Read before writing.** Always read files before modifying them. The references table tells you what to read, but check the actual content.
- **Respect existing patterns.** The build plan should already account for project conventions, but if you notice a conflict, follow the project's existing patterns over the plan and note the deviation.

### Verifying
- **Don't skip the verification.** The post-build check exists because drift between spec and build is the #1 source of pain in multi-step specs. Always run it.
- **Be honest about deviations.** If you had to name something differently, split a class, or change an interface — that's fine, but flag it. Silent drift causes compounding problems in later steps.

### Documenting
- **Two phases, hard boundary.** Propose doc changes and stop. Only edit after the user approves. No silent doc rewrites.
- **Verify against code, not just specs.** The spec describes intent; the code is what was built. When they diverge, trust the code.
- **Don't expand doc scope.** If a doc is intentionally concise, keep it concise. Don't add sections or detail that wasn't there.
- **Preserve voice.** Each doc has its own tone. `CLAUDE.md` is terse and instructional. Architecture docs are detailed references. Match the style.
- **Always check CLAUDE.md.** It almost always needs a pass after a build.
- **Don't do a full audit.** That's the `/docs` skill's job. Only touch docs affected by this build.

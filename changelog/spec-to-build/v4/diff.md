# spec-to-build — Add Post-Build Verification Step

_Date: 2026-04-06_

## What Changed

**Added new workflow step 8: Post-build verification.** This step runs after the build plan has been executed (not during planning). It reads back the files that were created or modified, compares them against the spec's naming and interface expectations, and flags any deviations in a new `_deviations.md` output file. The deviation report includes what the spec expected, what was actually built, which remaining step specs are impacted, and a suggested fix direction.

**Updated subfolder awareness in step 1.** The artifact scan now also reads `*_deviations.md` files from previous steps. When a deviation exists, the skill uses the *actually built* names rather than the original spec names when planning subsequent steps.

**Added `_deviations.md` to the output files list.** Documented as conditional — only written if deviations exist, and only after the build is complete.

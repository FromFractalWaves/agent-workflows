# spec-to-build — Move Post-Build Verification to build-spec

_Date: 2026-04-06_

## What Changed

**Replaced step 8 (post-build verification) with a one-liner.** The full verification workflow was moved to the new `/build-spec` skill, which owns the execute-and-verify cycle. Step 8 now just reminds the user to run `/build-spec` after the plan is written.

**Removed `_deviations.md` from output files list.** That output is now produced by `/build-spec`, not `/spec-to-build`.

The subfolder awareness in step 1 still reads `*_deviations.md` files from previous steps — that stays, since the planner needs to know about past drift when planning the next step.

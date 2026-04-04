okay so i have 2 repos. the way that i arrive in this position was that i had a plan to develop a project and split the build into phase 1a and 1b. 1a used live radio, and i travel for work so phase 1b was things i could build without the live data. i travel for work so that is why i did this. i started with 1A home and i soon left for a work trip and did 2b on the road. i ended up making signifigant progress in the time i was away and now 1a is completely out of touch with 1b, 1b now being albatross itself, and phase 1a now fits in as a module in albatross a domain specific capture module. so that is some background context and here is my plan to add it in.

i dont know code. i have ideas and feeling about it as an early vibe coder. i do know back in the day one might merge 1a into the capture folder and slowly fix it to match the patterns in albatross (oh btw i have a mock live data pipleine already built so it should be ready to wire in) but i have a better way (at least for me) to avoid regression, my plan is to distill spec from the 1a repo for the process, then put that spec in albatross and use spec-to build to build the capture module into albatross without having to struggle to match patterns


ai version:

# Combining Two Repos Without Merging Code

## Background

I split my project into two phases early on: Phase 1A was the live radio capture layer (needed hardware, could only work on it at home) and Phase 1B was everything else — the pipeline, the TRM, the UI, the database (could build anywhere). I travel for work, so this split made sense.

I started with 1A at home, then left for a work trip and switched to 1B. I made way more progress than expected on the road. By the time I got back, 1B had grown into Albatross — a full system with its own architecture, contracts layer, patterns, and conventions. Meanwhile 1A was still sitting in its own repo, completely out of touch with how Albatross works now.

1A didn't go to waste though. It still does what it was built to do — capture live P25 radio data. It just needs to become a module inside Albatross rather than a standalone project. Specifically, it fits into the `capture/` directory as a domain-specific capture implementation alongside the mock capture that already exists.

## The Traditional Approach

The obvious move: merge 1A's code into Albatross's `capture/` folder and start fixing it to match Albatross's patterns. Update imports to use the contracts layer, refactor to match the async conventions, wire it into `BasePipelineManager`, fix whatever breaks along the way.

This is painful. You're fighting two codebases at once — trying to understand what 1A does while simultaneously translating it into Albatross's language. Every file you touch might break something. The AI agent makes this worse, not better — it sees the pattern mismatches and starts "fixing" things aggressively, introducing regressions in code that was working fine.

## The Better Way: Merge Intent, Not Code

Instead of merging the code, I merge the *intent*. The process:

1. **Distill a spec from the 1A repo.** Read through 1A and write a spec that describes what it does — how capture works, what the stages are, what the data flow looks like, what the output is. The spec is written in plain language, not in 1A's code conventions. It captures the *what*, not the *how*.

2. **Drop the spec into Albatross.** Put it in `specs/` like any other work order.

3. **Run spec-to-build.** The skill reads the spec, explores the Albatross repo, and produces a build plan that implements 1A's functionality using Albatross's existing patterns — contracts, `BasePipelineManager`, the push-only WebSocket architecture, everything. The misalignments file catches anywhere the spec's assumptions don't match Albatross's reality.

4. **Build natively.** Claude Code builds the capture module from the spec. The code it produces is native to Albatross from the start — right imports, right patterns, right conventions. No merge conflicts, no pattern mismatches, no regression loops.

The 1A repo becomes reference material. You read it to write the spec, then you don't touch it again. The spec is the translation layer between the two codebases.

## Why This Works

The traditional merge tries to preserve 1A's code and adapt it. That means you're carrying forward every decision 1A made — naming conventions, file structure, patterns — and spending effort translating each one. The more the repos have diverged, the more painful this is.

The spec approach throws away 1A's *code* but preserves 1A's *knowledge*. Everything 1A figured out about how to capture P25 radio data — the GNU Radio flowgraph, the ZMQ bridge, the call buffering, the talkgroup correlation — gets captured in the spec. Then it gets rebuilt in a form that Albatross already understands.

This only works because the spec-to-build skill grounds the plan against the real codebase. A vague spec would produce vague code. But spec-to-build reads the contracts layer, sees how `BasePipelineManager` works, checks the existing mock pipeline for patterns to follow, and produces a plan that fits. The references list and current state summary mean the agent starts with full context about Albatross before writing a single line.

## When to Use This

This approach makes sense when:

- Two codebases have diverged significantly in conventions and patterns
- One codebase is the "home" with established architecture, the other needs to integrate into it
- The code being integrated is complex enough that a manual merge would take longer than a clean rebuild
- You have a mock or reference implementation in the home repo that the new module can pattern-match against (in my case, the mock pipeline)

It doesn't make sense when:

- The repos share the same patterns and a merge would be straightforward
- The code is small enough to just rewrite by hand
- There's no reference implementation to pattern-match against

## The Broader Principle

This is the same principle behind the UI rebuild workflow: don't fix broken code, rebuild from a clean spec. The regression problem disappears because you're not patching — you're building fresh against a known-good architecture.

The spec is always the translation layer. It captures intent in a form that's independent of any specific codebase's conventions. Spec-to-build is the bridge that turns that intent into code that's native to wherever it lands.
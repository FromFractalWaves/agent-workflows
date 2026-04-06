# How I Build Software with AI Agents

*A workflow system for managing complex codebases with AI coding agents.*

---

## The Problem

AI coding agents are powerful but unreliable at scale. They hallucinate file paths, lose context in large repos, regress working code while fixing bugs, and drift from your intent when the scope gets big enough. The raw capability is there — the agent can write good code — but without structure around it, the output is inconsistent and the debugging loop can be worse than writing the code yourself.

I build software full-time using Claude Code as my primary coding agent. The project is a general-purpose pipeline intelligence system with a Python async backend, FastAPI API, Next.js frontend, SQLAlchemy database layer, and an LLM-backed routing engine. It's big enough that no single conversation can hold the full context. I had to figure out how to make the agent productive without it seeing everything at once.

What follows is the workflow system I developed through trial and error. It's not theoretical — every piece exists because something broke without it.

---

## Core Principle: Control What the Agent Sees

The fundamental insight behind everything in this workflow is context control. An AI agent's output quality is directly proportional to the quality of the context it receives. Give it too much and it drowns. Give it too little and it hallucinates. Give it the wrong thing and it builds confidently in the wrong direction.

This isn't about prompt engineering — writing clever instructions. It's about context engineering — building systems that ensure the agent always has the right information at the right time, automatically, without you having to hand-feed it every session.

---

## Three Layers of Documentation

Every piece of writing in the project serves one of three roles. Keeping them separate is load-bearing.

### Vision — What the system should become

`docs/vision.md` captures design intent. What does a complete version of this system look like? What are the unsolved architectural problems? What would I build if I had unlimited time? This doc is for me — it's where I think out loud about the future so that when I sit down to work, I can pull from a coherent picture rather than reconstructing it from memory.

The vision doc is not a roadmap. Items don't have timelines or priorities. They land here when they represent what the system *should be*, independent of when or whether they get built. Some items are concrete (a dataset creator tool with specific UX), some are open problems (how to render domain-specific metadata without hardcoding UI per domain).

### Specs — What to build next

`specs/` holds temporary work orders. A spec describes a single unit of work — what to change, what to build, what the done state looks like. Specs are written in my own language, not the repo's language. They can be rough, context-free, even wrong about implementation details. The spec-to-build skill reconciles them with reality.

When the work is done and docs are updated, the spec and its artifacts are deleted. Nothing in `specs/` is permanent. It's a workspace, not an archive.

### Docs — What exists now

Everything else — architecture docs, implementation specs, status files, `CLAUDE.md` — describes the current state of the system. These are for the agent, not for humans. The organizational decisions (directory structure, naming, what goes where) optimize for fast context narrowing. When Claude Code needs to understand how the WebSocket layer works, it reads one doc and gets the full picture without wading through the entire repo.

The key discipline: docs must always reflect reality. A doc that describes a planned feature as if it exists is worse than no doc at all — the agent will build on top of something that isn't there. Post-build doc updates are non-negotiable.

---

## The Spec-to-Build Pipeline

This is the core execution workflow. It takes a rough idea and turns it into working code through a structured pipeline.

### Step 1: Write the spec (Claude.ai chat)

I plan in conversation, not in code. I talk through what I want to build, think out loud about the architecture, and let the ideas take shape naturally. When the thinking crystallizes, I write a spec — or more often, I talk through it and have Claude draft it for me.

The spec is intentionally written in my own terminology. I don't worry about matching file paths or function names in the repo. That's the next step's job.

### Step 2: Run spec-to-build (Claude Code)

The spec-to-build skill is a Claude Code agent that reads the spec, explores the repo, and produces three artifacts:

**References list** — every file in the repo that the spec touches or depends on, actually read and verified. Pre-build references (what exists now) and post-build references (what the plan will create). This is the file inventory that prevents hallucinated paths.

**Summaries** — a plain-language description of how the affected system works right now (current state) and how it will work after the build (target state). These aren't file-by-file inventories — they're coherent explanations of the flow. I review these to verify the agent understood the system before it starts changing things.

**Misalignments** — where the spec doesn't match reality. Wrong file names, features that already exist, architectural assumptions that conflict with how the project actually works. Each misalignment includes what the spec said, what the repo actually has, and how the build plan resolves it.

**Build plan** — concrete implementation steps with specific files, functions, and components named. Steps are in dependency order — foundations first, then layers that build on them. No hand-waving, no "update the relevant modules."

The original spec is preserved unchanged. Everything is additive — I can re-run the skill if something was off.

### Step 3: Review before building

I read the summaries first. Does the current state match my understanding? Does the target state describe what I actually want? If not, the spec needs updating before any code is written.

Then the misalignments. These are usually minor (a type name mismatch, a file that already exists) but occasionally catch real problems (a spec that assumes polling when the system is push-only).

Then I ask Claude Code: "any comments, suggestions, or advice before I run?" It flags edge cases and scope concerns.

### Step 4: Build (build-spec skill)

The build-spec skill executes the build plan, verifies the build matches the spec, and updates affected documentation — all in one pass. The builder already has the freshest context about what changed, so doc updates happen inline rather than as a separate step. If something goes wrong: `git checkout . && git clean -fd` to revert, review what went wrong, update the spec or plan, try again.

---

## The Skills System

Skills are behavioral contracts for Claude Code. Each skill is a markdown file in `.claude/skills/` that tells the agent how to approach a specific type of task. They're not prompts — they're process definitions.

### Current skills

**spec-to-build** — the planning pipeline described above. Reads a spec, inventories the repo, produces references, summaries, misalignments, and a build plan.

**build-spec** — the execution counterpart to spec-to-build. Takes a build plan and executes it, verifies the build matches the spec, and updates affected documentation.

**iterate** — empirical, bug-driven development. Works toward a testing goal one bug at a time with documentation checkpoints and user confirmation between each fix. For discovery work where you don't know what's broken until you try it.

**docs** — full repository documentation audit. Reads every doc, compares against the actual codebase, finds every discrepancy, fixes everything. Includes phase language detection and workflow alignment checks.

**skill-manager** — meta-skill that manages all other skills. Scaffolds new skills, handles versioned updates with changelog capture, and documents changes that were made manually.

*Note: spec-docs was retired and absorbed into build-spec v2, which now handles doc updates as part of its build-verify-document cycle.*

### How skills evolve

Skills improve through a feedback loop:

1. I run the skill
2. The agent does something wrong or suboptimal
3. I identify the specific behavior that caused the problem
4. I update the skill to prevent that behavior
5. I document the change — what the agent did, what I changed, what resulted

Each iteration makes the skill more reliable. The documentation of changes (what I call skill diffs) creates a case study trail — not just what the skill does, but *why* each rule exists.

---

## The UI Rebuild Workflow

Debugging UI issues with an AI agent has a specific failure mode: the agent fixes one thing and breaks another, leading to a regression loop that leaves the code worse than when you started. This workflow avoids that entirely by not debugging at all.

### Step 1: Generate a reference doc

Use `/plan` in Claude Code to explore the system end-to-end without changing anything. The output is a comprehensive reference doc explaining how the feature works from data layer through API to frontend. This is a snapshot of ground truth.

### Step 2: Write a clean rebuild spec

In Claude.ai chat (not Claude Code), write a spec for what the page should be — not what's wrong with it, but what it should look like built fresh against the current codebase. Reference the fix_info doc for data shapes and API contracts.

### Step 3: Run spec-to-build

Same pipeline as above. The misalignments step catches any assumptions in the spec that don't match reality.

### Step 4: Build from clean

Claude Code builds the page from the spec. The regression problem disappears because you're not patching — you're rebuilding from a known-good spec.

If the build fails: `git checkout . && git clean -fd` to revert. Review, update, try again. Zero risk of partial state.

### Change notes

Small UI issues noticed during testing go into `specs/change_notes.md` — a running list that feeds into future specs or gets batched into a corrections pass. These don't interrupt the current build.

---

## Planning vs Execution: Two Different Contexts

Planning happens in Claude.ai chat. Execution happens in Claude Code. These contexts have different needs and should not bleed into each other.

**Planning context** is exploratory. I think out loud, use my own terminology, propose ideas that might be wrong, and iterate toward clarity through conversation. The output is a spec — a document that captures intent.

**Execution context** is precise. Claude Code reads the spec, aligns it with the repo, and builds exactly what's specified. It uses the repo's terminology, follows existing patterns, and produces concrete code.

The spec-to-build skill is the bridge. It translates from planning language to execution language. The spec says "make the live data push-only." The build plan says "replace `useLiveData` polling with WebSocket connection to `/ws/live/mock`, update TanStack Query cache via `queryClient.setQueryData` on `packet_routed` messages."

This separation matters because conflating the two leads to bad outcomes in both directions. Planning in Claude Code means the agent starts building before the idea is clear. Executing from a vague plan means the agent fills in gaps with guesses.

---

## How Work Is Organized

The project was originally built in sequential phases — TRM first, then UI, then database pipeline. That made sense when each layer depended on the one before it. Now that the foundation is in place, work doesn't follow a linear path.

Work is organized as specs. A spec describes what to change or build, gets aligned with the repo via spec-to-build, and becomes a build plan. When the work is done, the spec is deleted and the docs are updated. No phase numbers, no sub-phases. Just named units of work that say what they are.

The vision doc says what the system should become. Specs say what to build next. Docs say what exists now. Three layers, clean separation, nothing trying to serve two purposes.

---

## What Makes This Work

**Upfront planning produces faster builds.** A 30-minute planning session in chat that produces a clear spec saves hours of Claude Code wandering. The agent builds faster and more coherently when it knows exactly what to do.

**Docs are for the agent, not for humans.** This changes what you write and how you organize it. Short, accurate, structured for fast context narrowing. The agent reads these on every task — stale docs compound into stale code.

**Post-build doc sync is non-negotiable.** After each build, docs update to reflect what was actually built. The next build starts with accurate context. Skip this step and context rot sets in within a few builds.

**Skills are iterable.** A bad skill output isn't a failure — it's data. Identify the specific behavior, update the skill, document the change. The skills get better over time because the feedback loop is tight.

**Know when to stop.** Momentum is seductive. The best work happens in focused bursts with breaks between them. Letting ideas soak before continuing has recognized value even when you want to keep going.

---

## The Origin

Before coding agents existed, the bottleneck was getting code into the AI's context window. I built a CLI tool called CTree that generated curated context snapshots — you selected which files to include, it packaged them for the AI. That tool is obsolete now, but the thinking behind it is the same thinking behind everything in this workflow: control what the agent sees and it produces better work.

The tools changed. The insight didn't.

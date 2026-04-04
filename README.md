# AI Agent Workflow System

*Portable skills, documented processes, and lessons learned for building software with AI coding agents.*

---

## What This Is

A collection of workflows, skills, and case studies for managing complex codebases with AI coding agents — specifically Claude Code, though the principles apply broadly.

This isn't a framework or a library. It's a system of practices that solve a specific problem: how do you keep an AI agent reliable as your codebase grows beyond what fits in a single conversation?

Everything here was developed through real use on a production-scale project (a general-purpose pipeline intelligence system with Python async backend, FastAPI, Next.js, SQLAlchemy, and an LLM routing engine). The skills have been iterated on, the workflows have been tested, and the case studies document actual failures and fixes — not hypothetical best practices.

---

## Who This Is For

- **Claude Code users** who are building real projects and hitting reliability problems at scale — context drift, hallucinated file paths, regression loops, agents that build confidently in the wrong direction
- **Anyone managing AI agents for software development** who wants a repeatable process instead of ad hoc prompting
- **People evaluating AI-assisted development** who want to see what a mature workflow looks like in practice, including the failures

---

## Repo Structure

```
skills/                          # Versioned skill snapshots
  spec-to-build/                 # spec-to-build-v1.md, spec-to-build-v2.md, ...
  docs/                          # docs skill versions
  spec-docs/                     # spec-docs skill versions
  skill-manager/                 # skill-manager-v1.md, ...

changelog/                       # Skill evolution — per-version case studies
  spec-to-build/                 # v1/, v2/, ... each with diff.md, reasoning.md
  docs/
  spec-docs/
  skill-manager/

workflows/                       # Process documentation
  rebuild/                       # Rebuild from clean specs instead of debugging
  repo-transplant/               # Extract specs from source repos for native rebuilds

origin/                          # Skill ideation — outlines and reasoning before a skill exists
  skill-manager/                 # idea.md, outline.md
  spec-to-build/
  docs/
  spec-docs/

doc.md                           # How I Build Software with AI Agents — full workflow write-up
vision.md                        # Where this project is headed — ideas, not commitments
README.md                        # This file
```

---

## Skills

Skills are behavioral contracts for Claude Code. Each one is a markdown file that defines how the agent should approach a specific type of task. They're portable — copy them into any repo's `.claude/skills/` directory and they work.

Every skill version is preserved in `skills/[name]/` as `[name]-v1.md`, `[name]-v2.md`, etc. The live version is always the highest number. Previous versions stay for reference and diffing.

### spec-to-build

The core planning skill. Takes a rough, possibly context-free spec and produces:

- **References list** — every relevant file in the repo, verified by actually reading it. Pre-build (what exists) and post-build (what will be created).
- **Summaries** — plain-language current state and target state descriptions. The before/after picture.
- **Misalignments** — where the spec doesn't match reality, with resolutions.
- **Build plan** — concrete implementation steps with specific files, functions, and components named.

The original spec is preserved. Everything is additive. Re-run if something was off.

### docs

Full documentation audit. Reads every doc in the project, compares against the actual codebase, finds every discrepancy, fixes everything. Includes detection of stale forward-looking language and workflow alignment checks.

Use periodically for comprehensive audits.

### spec-docs

Targeted post-build documentation pass. Uses the spec-to-build outputs (summaries, references, build plan) to update only the docs affected by the most recent build. The target state summary is the source of truth.

Use after every build.

### skill-manager

Meta-skill that manages all other skills. Three modes:

- **create** — scaffolds a new skill, saves v1 snapshot, writes initial changelog entry
- **update** — guided skill update with automatic versioning and changelog capture
- **changelog** — documents a change that was already made manually

Writes versioned snapshots to `skills/` and changelog entries to `changelog/` automatically.

### The chain

The project skills form a pipeline:

```
spec-to-build  →  Claude Code builds  →  spec-docs  →  docs (periodic)
   (plan)            (execute)          (targeted)      (full audit)
```

The skill-manager sits above this chain — it creates and evolves the skills themselves.

---

## Workflows

### Spec-to-Build Pipeline

The full planning-to-execution process:

1. **Plan in conversation** — think out loud, iterate toward clarity, produce a spec in your own language
2. **Run spec-to-build** — the skill aligns the spec with the repo and produces the build artifacts
3. **Review** — verify summaries match your understanding, check misalignments, ask CC for pre-flight comments
4. **Build** — CC executes the plan. If it fails, `git checkout . && git clean -fd` to revert with zero risk
5. **Update docs** — run spec-docs to update affected documentation
6. **Clean up** — delete spec files when done

### Rebuild Workflow

When debugging leads to regression loops:

1. **Generate a reference doc** — use `/plan` to explore the system end-to-end without changing code
2. **Write a clean rebuild spec** — describe what the page should be, not what's wrong with it
3. **Run spec-to-build** — same pipeline, misalignments catch bad assumptions
4. **Build from clean** — no patching, no regression risk
5. **Capture change notes** — small issues noticed during testing go into a running list for future specs

See `workflows/rebuild/` for the full process with prompt templates and file conventions.

### Repo Transplant

When you need to integrate functionality from one repo into another without merging code:

1. **Read the source repo end-to-end** — understand what it does as a system
2. **Distill a spec** — describe the functionality in plain language, capturing the *what* not the *how*
3. **Drop the spec in the target repo** — run spec-to-build to rebuild it natively

The source repo's code stays untouched. The spec carries the knowledge forward; spec-to-build rebuilds it using the target repo's patterns.

See `workflows/repo-transplant/` for the extraction workflow and the full rationale.

### Three Documentation Layers

- **Vision** — what the system should become. Design intent, not a roadmap.
- **Specs** — what to build next. Temporary, deleted after work is done.
- **Docs** — what exists now. Accurate, current, optimized for agent consumption.

These must stay separate. A vision item described as if it's built will mislead the agent. A doc that describes planned work as current state will produce code built on top of something that doesn't exist.

### Planning vs Execution

Planning happens in chat (Claude.ai). Execution happens in Claude Code. These contexts have different needs:

- **Planning** is exploratory — rough language, own terminology, ideas that might be wrong, iteration toward clarity
- **Execution** is precise — repo terminology, existing patterns, concrete code

The spec-to-build skill bridges them. It translates planning language into execution language. Conflating the two leads to agents building before the idea is clear, or filling in gaps with guesses.

---

## Changelog

The changelog is the most unique part of this repo. Each skill update lives in its own versioned directory (`changelog/[skill]/v[N]/`) with separate files:

- **`diff.md`** — what changed in the skill file
- **`reasoning.md`** — the specific agent behavior that prompted the change, why this fix and not another, and whether it worked
- **`outline.md` / `idea.md`** (optional) — planning notes or initial thinking before the update

This is empirical documentation of how to manage AI agent behavior. Not theory about what should work — actual interventions with actual outcomes.

Example: The spec-to-build skill originally overwrote the spec file with the build plan. This meant re-running the skill after a bad plan destroyed the original intent. The fix: write the build plan to a separate file, preserve the original spec. The reasoning: specs represent intent, build plans represent execution. Destroying intent to create execution means you can't iterate.

Each entry like this is a data point someone else can learn from. Over time, the changelog tells the story of how the skills became reliable.

---

## Origin

The `origin/` directory captures how skills are conceived — the initial idea, the outline, the reasoning before any code is written. This is the ideation stage, before `skill-manager create` turns it into a real skill.

Before coding agents existed, the bottleneck was getting code into the AI's context window. I built a CLI tool called [CTree](https://github.com/FromFractalWaves/ctree) that generated curated context snapshots — select which files to include, package them for the AI. Agents made that tool obsolete, but the underlying insight survived: **control what the agent sees and it produces better work.**

CTree was manual context curation. The skills system is automated context engineering. The problem is the same. The tools evolved.

---

## How to Use This

### Quick start

1. Copy the latest version of each skill from `skills/[name]/` into your repo's `.claude/skills/` directory
2. Create a `specs/` directory in your repo
3. Write a spec for something you want to build
4. Run `/spec-to-build specs/my-feature.md` in Claude Code
5. Review the outputs, then build

### Adapting to your project

The skills reference patterns from the project they were developed on (Pydantic models, FastAPI routes, Next.js components). The structure and process are universal — the specific technology references are examples. Claude Code adapts to your stack when it reads your codebase during the spec-to-build exploration step.

### Contributing

The most valuable contributions are case studies. If you use these skills and the agent does something unexpected — good or bad — document it the same way the changelog entries do: what happened, what you changed, what resulted. That's how the skills improve.

---

## Principles

These are the hard-won lessons behind everything in this repo:

- **Upfront planning produces faster builds.** A 30-minute planning session saves hours of agent wandering.
- **Docs are for the agent, not for humans.** Structure them for fast context narrowing, not for readability.
- **Post-build doc sync is non-negotiable.** Skip it and context rot sets in within a few builds.
- **Skills are iterable.** A bad output isn't a failure — it's data. Identify the behavior, update the skill, document the change.
- **Don't debug with agents — rebuild with context.** The regression loop is a solved problem if you stop patching and start rebuilding from clean specs.
- **Separate planning from execution.** Different contexts, different needs, different tools.
- **Know when to stop.** The best work happens in focused bursts. Letting ideas soak has real value.
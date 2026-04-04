# Repo Transplant — Spec Extraction

Extract a spec from a source repo so its functionality can be rebuilt natively in a target repo via spec-to-build.

## When to use

- A source repo has diverged from the target repo's patterns and conventions
- You want to integrate the source's functionality without merging its code
- The source repo is becoming a module or component inside the target

## Steps

1. **Read the source repo end-to-end.** Explore the full codebase — entry points, core logic, data flow, configuration, dependencies. Understand what it does as a system, not file by file.

2. **Identify the functional core.** What are the stages? What data comes in, what processing happens, what comes out? What domain knowledge is embedded in the code that wouldn't be obvious from the target repo alone?

3. **Write the spec in plain language.** Describe the functionality the source repo provides — its purpose, its stages, its data flow, its inputs and outputs. Write it as a work order for someone who has never seen the source repo.

4. **Drop the spec in the target repo's `specs/` directory.** It's ready for `/spec-to-build`.

## Writing the spec

**Do:**
- Describe *what* the system does, not *how* the source code implements it
- Name the stages, processes, and data shapes in plain terms
- Note domain-specific knowledge — protocols, formats, timing constraints, edge cases that the source repo handles
- Describe what the output should look like from the target repo's perspective (e.g., "a capture module that produces X and feeds into Y")
- Mention external dependencies the functionality requires (hardware, services, libraries)

**Don't:**
- Use the source repo's file names, class names, or module structure
- Describe the source's architecture or patterns — the target has its own
- Include implementation details that only matter in the source's context
- Try to map source files to target files — that's spec-to-build's job

## Output

A single spec file: `specs/[name]_transplant.md` in the target repo.

The source repo becomes reference material. You read it to write the spec, then you don't touch it again.

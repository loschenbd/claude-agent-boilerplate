---
name: research
description: Run a focused research pass on the codebase before any planning or implementation. Use when starting a complex change to gather facts about the affected area without making code changes. Produces a research.md doc with file references, current patterns, data flows, types, and collision points.
---

> **Experimental.** This skill mirrors the `/research` slash command and is provided so other agents/skills can invoke research programmatically. The slash command remains the primary entry point for users.

You are in the **Research phase** of QRSPI. Your job is to gather objective facts about the codebase — no recommendations, no design opinions, no code changes.

The user (or calling agent) will provide either:
- A feature ticket / task description, OR
- A set of targeted questions

**If given a ticket:** Extract the key technical unknowns, then research those. Do not let the ticket bias your findings — report what *is*, not what the ticket wants.

**If given questions:** Answer each question with codebase evidence.

## What to produce

Spawn an `Explore` subagent to investigate. Direct it to find and document:

1. **Relevant files** — exact paths for every file that touches the affected area
2. **Current patterns** — how similar things are already done in this codebase
3. **Data flows** — how data moves through the affected area
4. **Existing types/schemas** — relevant TypeScript types, Zod schemas, database table shapes
5. **Potential collision points** — anything that could conflict with or be affected by changes

## Output format

Write findings to `plans/<task-slug>/research.md`:

```markdown
# Research: <task name>

## Relevant Files
- `path/to/file.ts` — what it does and why it matters here

## Current Patterns
- <pattern name>: <how it works, with file:line references>

## Data Flow
<trace the relevant flow end-to-end>

## Types & Schemas
- <type/schema name>: `path/to/file.ts:line`

## Collision Points
- <anything that could break or conflict>

## Open Questions
- <anything the research couldn't resolve — flag for Design Discussion>
```

## Rules

- **Facts only.** No implementation suggestions in this doc.
- **Cite everything.** Every claim should reference a file path and line number.
- **Keep context lean.** Use the Explore subagent for grep/glob/read.
- **Stop here.** Do not proceed to design. Surface the doc to the caller.

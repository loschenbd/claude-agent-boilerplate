Run a focused research pass on the codebase before any planning or implementation begins.

## Instructions

You are in the **Research phase** of QRSPI. Your job is to gather objective facts about the codebase — no recommendations, no design opinions, no code changes.

The user will provide either:
- A feature ticket / task description, OR
- A set of targeted questions they've already written

**If given a ticket:** Extract the key technical unknowns from it, then research those. Do not let the ticket bias your findings — report what *is*, not what the ticket wants.

**If given questions:** Answer each question with codebase evidence.

## What to produce

Spawn an `Explore` subagent to investigate. Direct it to find and document:

1. **Relevant files** — exact paths for every file that touches the affected area
2. **Current patterns** — how similar things are already done in this codebase (naming, structure, conventions)
3. **Data flows** — how data moves through the affected area (tRPC → PowerSync → UI, etc.)
4. **Existing types/schemas** — relevant TypeScript types, Zod schemas, Supabase table shapes, PowerSync schema entries
5. **Potential collision points** — anything that could conflict with or be affected by changes in this area

## Output format

Write findings to `plans/<task-slug>/research.md` using this structure:

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
- **Keep context lean.** Use the Explore subagent for grep/glob/read so the main context stays clean.
- **Stop here.** Do not proceed to planning. Surface the research doc to the user for review.

After writing the file, tell the user: "Research complete. Review `plans/<task-slug>/research.md` before running `/d`."

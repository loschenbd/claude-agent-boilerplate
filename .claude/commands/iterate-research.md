Re-run the research phase with corrective feedback from the user.

## Instructions

You are iterating on a previous research pass. The user has reviewed `plans/<task-slug>/research.md` and is asking for changes.

Use the user's feedback in this message (the text after `/iterate-research`) as the corrective direction.

1. Read the existing `plans/<task-slug>/research.md`
2. Identify what the feedback wants changed:
   - Missing files or patterns to investigate?
   - Wrong scope (too broad, too narrow)?
   - Specific claims that are wrong?
   - A collision point that wasn't surfaced?
3. Spawn an `Explore` subagent with explicit corrective direction based on the feedback (not a generic re-research)
4. **Replace** `plans/<task-slug>/research.md` with a new clean version (do not append)
5. Add a short `## Iteration notes` section at the bottom briefly noting what changed in this pass and why
6. Tell the user: "Research updated. Re-review `plans/<task-slug>/research.md`."

## Rules

- **Replace, don't append.** Produce a clean doc, not a diff against the previous version.
- **Stop at the same gate.** Don't proceed to design — wait for user re-review.
- **Treat feedback as ground truth.** If the user says a claim is wrong, investigate the codebase and correct it; don't argue.
- **Don't invent new patterns.** Research is still facts-only — no recommendations.

<!-- QRSPI workflow inspired by Dex Horthy / HumanLayer, with thanks to Jerry Bruns; adapted for Claude Code by Benjamin Loschen (https://github.com/loschenbd/claude-agent-boilerplate) -->

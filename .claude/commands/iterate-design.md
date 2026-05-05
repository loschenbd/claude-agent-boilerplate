Re-run the design phase with corrective feedback from the user.

## Instructions

You are iterating on a previous design pass. The user has reviewed `plans/<task-slug>/design.md` and is asking for changes.

Use the user's feedback in this message (the text after `/iterate-design`) as the corrective direction.

1. Read both `plans/<task-slug>/design.md` and `plans/<task-slug>/research.md` for context
2. Identify what the feedback wants changed:
   - Wrong pattern choice?
   - A constraint you missed?
   - An open question that has now been resolved?
   - A new consideration the user wants reflected?
3. **Replace** `plans/<task-slug>/design.md` with a corrected clean version
4. Add a short `## Iteration notes` section at the bottom noting what changed and why
5. Tell the user: "Design updated. Re-review `plans/<task-slug>/design.md`."

## Rules

- **Replace, don't append.** Clean doc, not a diff.
- **Don't re-research.** If the feedback exposes a research gap (missing files, wrong patterns), tell the user to run `/iterate-research <feedback>` first, then return here.
- **Stop at the same gate.** Don't proceed to plan — wait for user re-review.
- **Keep design lean.** Stay around ~200 lines; don't bloat with implementation details.

<!-- QRSPI workflow inspired by Dex Horthy / HumanLayer, with thanks to Jerry Bruns; adapted for Claude Code by Benjamin Loschen (https://github.com/loschenbd/claude-agent-boilerplate) -->

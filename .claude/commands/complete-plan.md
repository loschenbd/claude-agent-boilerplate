Mark the current plan as complete and archive it.

## Instructions

You are called when a plan in `plans/<task-slug>/` is finished and ready to be archived.

1. **Identify the plan slug.** Either from the user's argument, or by scanning `plans/` for the most recently modified `plan.md` that isn't already in `_done/`.
2. **Confirm completion.** Read `plan.md` and check that all task checkboxes are marked `[x]`. If any are unchecked, refuse and tell the user which tasks are still open. Do not proceed.
3. **Update `plan.md`:** change the status line to `**Status:** completed`.
4. **Write `plans/<task-slug>/wrapup.md`** with:

   ```markdown
   # Wrap-up: <task name>
   **Completed:** <date>

   ## What was built
   - <bullet list of completed tasks, with file references where relevant>

   ## What was deferred
   - <anything in the original plan or design that did not ship — and why>

   ## Follow-ups
   - <new issues this work surfaced — for future plans>

   ## Final state
   - **Files changed:** <list>
   - **Branch / commits:** <if applicable>
   ```

5. **Archive the directory:** move `plans/<task-slug>/` to `plans/_done/<task-slug>/` (create `_done/` if it doesn't exist). This preserves history without cluttering the active plans list.
6. **Tell the user:** "Plan archived to `plans/_done/<task-slug>/`. Wrap-up at `plans/_done/<task-slug>/wrapup.md`."

## Rules

- **Don't archive incomplete plans.** If tasks are unchecked, refuse and report.
- **Don't lose history.** Move the directory, don't delete it. Old plans become reference material.
- **Don't skip the wrap-up.** A completed plan with no wrap-up is incomplete from a knowledge-management standpoint.
- **If `wrapup.md` already exists**, the plan was already completed — confirm with the user before overwriting.

<!-- QRSPI workflow inspired by Dex Horthy / HumanLayer, with thanks to Jerry Bruns; adapted for Claude Code by Benjamin Loschen (https://github.com/loschenbd/claude-agent-boilerplate) -->

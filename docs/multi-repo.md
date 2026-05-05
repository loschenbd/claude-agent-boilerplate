# Multi-Repo Workflow

The QRSPI workflow assumes you're working in a single repo. For features that span multiple repos (e.g., a frontend repo + a backend repo + a shared library), use the **coordination repo pattern**.

## Inspired by

This pattern is borrowed from [HumanLayer's `rpi-coordination-template`](https://github.com/humanlayer/rpi-coordination-template) — adapted to fit QRSPI.

## Layout

```
~/Projects/
  frontend/           # your frontend repo
  backend/            # your backend repo
  shared-lib/         # your shared package
  coordination/       # the QRSPI driver — created from this boilerplate
    .claude/
      agents/
      commands/
    CLAUDE.md
    plans/
```

The `coordination/` repo holds your QRSPI agents and is where you run Claude Code from. It does not contain product code — it contains the workflow.

## Setup

1. Create the coordination repo from this boilerplate (or `cp -r .claude` into an existing one)
2. In `coordination/.claude/settings.json`, set `additionalDirectories`:
   ```json
   {
     "additionalDirectories": [
       "../frontend",
       "../backend",
       "../shared-lib"
     ]
   }
   ```
3. In `coordination/CLAUDE.md`, list each repo and a one-line description of what it owns:
   ```markdown
   ## Repos under coordination

   - `../frontend` — Next.js web app, tRPC client, UI components
   - `../backend` — Node.js API, tRPC server, Supabase admin
   - `../shared-lib` — types and Zod schemas shared by both
   ```
4. Run all your `/research`, `/d`, and other commands from `coordination/`. Specialists can read and write across the included repos.

## Worktree pattern for parallel tasks

If you need to work on multiple tasks simultaneously without polluting each repo's working tree, create per-task worktrees laid out in a `workspaces/` directory:

```
~/Projects/
  frontend/
  backend/
  shared-lib/
  coordination/
  workspaces/
    add-csv-export/
      frontend/        # frontend checked out to add-csv-export branch
      backend/         # backend checked out to add-csv-export branch
      coordination/    # coordination checked out to add-csv-export branch
    fix-auth-bug/
      frontend/        # frontend checked out to fix-auth-bug branch
      coordination/
```

Each task gets its own `workspaces/<task-slug>/` directory containing per-repo worktrees. Run Claude Code from `workspaces/<task-slug>/coordination/` for that task; switch directories to switch tasks.

To create a worktree set:

```bash
TASK=add-csv-export
mkdir -p ~/Projects/workspaces/$TASK
cd ~/Projects/frontend && git worktree add ../workspaces/$TASK/frontend -b $TASK
cd ~/Projects/backend && git worktree add ../workspaces/$TASK/backend -b $TASK
cd ~/Projects/coordination && git worktree add ../workspaces/$TASK/coordination -b $TASK
```

To clean up after the task is done:

```bash
cd ~/Projects/frontend && git worktree remove ../workspaces/$TASK/frontend
cd ~/Projects/backend && git worktree remove ../workspaces/$TASK/backend
cd ~/Projects/coordination && git worktree remove ../workspaces/$TASK/coordination
rmdir ~/Projects/workspaces/$TASK
```

## When to use this pattern

- ✓ A feature legitimately spans multiple repos
- ✓ You want clean separation between agent infrastructure and product code
- ✓ Multiple devs share the agent setup but work in different combinations of repos

## When NOT to use this pattern

- ✗ Single-repo project — just put `.claude/` at the repo root
- ✗ Monorepo (Turborepo, Nx, pnpm workspaces) — single repo, single `.claude/`
- ✗ Tasks that don't actually span repos — adds overhead for no gain

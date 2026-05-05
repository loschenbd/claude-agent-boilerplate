Run verification checks after an implementation to confirm a change works.

## Instructions

This is the optional **V-phase** of QRSPI — verification after implementation, before declaring a task done.

1. Identify project-appropriate verification commands. Check in this order:
   - `CLAUDE.md` for an explicit verification section
   - `.claude/agents/registry.md` "Test policy" / quality conventions
   - `package.json` `scripts` (look for: `typecheck`, `lint`, `build`, `format:check`, `test`)
   - `Cargo.toml`, `pyproject.toml`, `go.mod`, etc. for non-JS projects
2. Run the relevant commands sequentially via Bash. Stop on first failure for fast feedback.
3. For each check, report:
   - Command run
   - Pass / fail
   - Key output (file:line for failures, last 10 lines on errors)
4. If all checks pass, summarize and recommend committing.
5. If any fail, identify which specialist or manager owns the fix and recommend routing it back through the relevant team.

## Default check order

- **Type safety:** `tsc --noEmit` or the project's typecheck script
- **Lint:** project's lint script (eslint, ruff, golangci-lint, etc.)
- **Format:** Prettier / formatter check (if configured to gate)
- **Build:** project's build script
- **Tests:** **only run if the project has them.** Many projects (per their CLAUDE.md / registry) skip automated tests.

Skip steps that don't apply (no TypeScript → skip typecheck).

## Rules

- **Don't fix anything.** Verification is read-only — report failures, don't auto-fix.
- **Be concise.** Pass/fail summary plus failure details is enough; don't dump entire command output.
- **Don't run a test suite the project says doesn't exist.** Respect the registry's test policy.
- **Surface flaky vs. real failures.** If a check is known to flake (per CLAUDE.md), say so.

<!-- QRSPI workflow inspired by Dex Horthy / HumanLayer, with thanks to Jerry Bruns; adapted for Claude Code by Benjamin Loschen (https://github.com/loschenbd/claude-agent-boilerplate) -->

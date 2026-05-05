# [PROJECT NAME] — Agent Registry

<!-- CUSTOMIZE THIS FILE: Replace placeholder teams and specialists with your actual ones.
     The Director reads this before routing any task, so keep it accurate.
     See _templates/manager.md and _templates/specialist.md for how to create agent files. -->

All agent teams for this project. Read this before routing any task.

---

## [Team A] Team

<!-- CUSTOMIZE: Replace "[Team A]" with your team name (e.g. "Engineering", "Data", "Backend").
     Write one sentence describing what this team owns. -->

**Manager:** `[team-a]/manager`
**Scope:** [What this team is responsible for — e.g., "All feature development, UI, and API work."]

| Agent | Expertise |
|-------|-----------|
| `[team-a]/[specialist-1]` | [What this specialist knows and owns] |
| `[team-a]/[specialist-2]` | [What this specialist knows and owns] |
<!-- Add more specialists as needed. Each gets a file in .claude/agents/[team-a]/ -->

---

## [Team B] Team

<!-- CUSTOMIZE: Another team. Common splits: Engineering / Data / Quality / Infrastructure,
     or Frontend / Backend / DevOps. Use whatever matches your project structure. -->

**Manager:** `[team-b]/manager`
**Scope:** [What this team is responsible for]

| Agent | Expertise |
|-------|-----------|
| `[team-b]/[specialist-1]` | [What this specialist knows and owns] |
| `[team-b]/[specialist-2]` | [What this specialist knows and owns] |

---

## [Team C] Team

<!-- CUSTOMIZE: A quality/review team is recommended so the Director has somewhere to route
     TypeScript errors, test failures, lint issues, and cross-cutting concerns. -->

**Manager:** `[team-c]/manager`
**Scope:** [e.g., "Type safety, code style, and cross-cutting quality checks."]

| Agent | Expertise |
|-------|-----------|
| `[team-c]/[specialist-1]` | [What this specialist knows and owns] |

---

## Cross-Team Notes

<!-- CUSTOMIZE: Add project-wide conventions the Director and all managers must know.
     Examples below — replace with what's true for your project. -->

- **Monorepo layout:** [e.g., "Turborepo + pnpm workspaces. Path aliases: `@pkg/*` → `packages/*`"]
- **Env vars:** [e.g., "All vars in repo root `.env`. Never in individual app directories."]
- **Test policy:** [e.g., "No automated test suite — quality checks are type-check + lint only."]
- **Auth pattern:** [e.g., "JWT Bearer on native, cookie on web."]
- **State management:** [e.g., "Zustand for local state, React Query for server state."]
<!-- Add any other conventions that affect multiple teams -->

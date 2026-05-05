# Example: Universal RN + Next.js Monorepo

A worked example of the boilerplate filled out for a **universal React Native + Next.js monorepo** — the kind of project that ships a single codebase to iOS, Android, and web with shared UI components and offline-first sync.

Use this as a reference when filling in your own `.claude/` directory. **Do not copy these files wholesale** — your specialists, routing table, and conventions should reflect *your* project.

---

## What kind of app is this?

A cross-platform mobile + web app sharing a single codebase. The stack:

- **UI:** Tamagui (universal components), Solito (cross-platform navigation)
- **Native:** Expo, React Native, EAS Build/Submit/Update
- **Web:** Next.js App Router with SSR
- **State + sync:** PowerSync (offline-first SQLite ↔ Postgres sync), Jotai, TanStack Query
- **Backend:** Supabase (Postgres + auth + storage), tRPC v11
- **Build:** Turborepo, Yarn 4 workspaces
- **Other:** Pipedream (webhooks), PostHog (analytics), Branch (deep links)

This stack drives a 4-team split: Engineering (feature work), Data (DB + sync), Quality (types, parity, style), and Infrastructure (build + CI). 17 specialists in total.

If your project is simpler — a web-only SaaS, a backend service, a CLI — you'll likely land on 1–2 teams and 4–8 specialists. This example errs on the side of detail to show what a fully-fleshed-out setup looks like.

---

## How the templates map to the example

| Template file | Example file | What changed |
|---|---|---|
| `.claude/agents/director.md` (boilerplate) | [`.claude/agents/director.md`](.claude/agents/director.md) | Project name set, routing table filled in with 17 specialists across 4 teams |
| `.claude/agents/registry.md` (boilerplate) | [`.claude/agents/registry.md`](.claude/agents/registry.md) | Four teams declared with full specialist rosters and cross-team conventions |
| `.claude/agents/_templates/manager.md` | [`.claude/agents/engineering/manager.md`](.claude/agents/engineering/manager.md) | Filled in for the Engineering team that owns all feature work |
| `.claude/agents/_templates/specialist.md` | [`.claude/agents/engineering/tamagui-expert.md`](.claude/agents/engineering/tamagui-expert.md) | Filled in for a Tamagui design-system specialist |
| `.claude/agents/comms-agent.md` (boilerplate) | [`.claude/agents/comms-agent.md`](.claude/agents/comms-agent.md) | Project-specific examples added in the body |

---

## Team layout

```
.claude/agents/
  director.md              # Orchestrator
  registry.md              # Team roster
  comms-agent.md           # Status updates

  engineering/             # All feature work, UI, native, API
    manager.md
    tamagui-expert.md      # Universal UI components, themes, tokens
    expo-expert.md         # Expo Router, EAS, native modules
    react-native-expert.md # RN primitives, Reanimated, gestures
    powersync-expert.md    # Offline sync, local SQLite queries
    nextjs-expert.md       # App Router, SSR, web-only features
    trpc-expert.md         # API routers, Zod schemas, middleware
    pipedream-expert.md    # Webhooks, async jobs
    media-specialist.md    # File lifecycle (upload, play, share, delete)
    posthog-expert.md      # Analytics events, feature flags

  data/                    # Database + sync rules
    manager.md
    supabase-expert.md     # Auth, RLS, storage, edge functions
    migration-specialist.md # SQL migrations, schema changes
    powersync-schema.md    # PowerSync AppSchema, sync rules

  quality/                 # Type safety, parity, style, perf
    manager.md
    typescript-reviewer.md
    cross-platform-validator.md  # Web/native parity, SSR hydration
    performance-profiler.md
    code-quality-enforcer.md     # Style guide, lint, imports

  infrastructure/          # Build, deploy, CI
    manager.md
    eas-specialist.md      # EAS Build/Submit, OTA channels
    ci-specialist.md       # GitHub Actions, Turborepo
```

The split is shaped by the universal/cross-platform nature of the codebase. Web-native parity has its own validator. Sync rules live in their own specialist because they exist on both the Postgres and SQLite sides.

---

## Key things to notice

**1. Specialists are scoped narrowly.**
Each one owns a single technology or concern. `tamagui-expert` knows tokens and themes; `react-native-expert` knows RN primitives and Reanimated. That separation lets the Director route precisely.

**2. The routing table in `director.md` does the real work.**
It maps task descriptions ("UI components, Tamagui, theming") to the right specialist. When you adapt the boilerplate, the quality of this table determines how well the Director routes.

**3. `registry.md` includes cross-team notes.**
Things every agent needs to know — monorepo layout, env var location, no test suite, auth pattern, state libraries. These prevent every specialist from rediscovering the same conventions.

**4. The Comms Agent stays generic.**
Almost no project-specific content. It's a thin formatting wrapper for status updates, so it doesn't need much customization.

---

## How to use this example

1. **Skim `registry.md` first** — it's the highest-leverage file and gives you the shape of the whole setup
2. **Open `director.md`** — see how the routing table is structured
3. **Pick one specialist file** that's close to a specialist you'd write — e.g., if you're a backend dev, look at `data/supabase-expert.md` or `engineering/trpc-expert.md`
4. **Compare to `_templates/specialist.md`** in the boilerplate root to see where the placeholders went

Then go back to your own project and adapt — don't copy.

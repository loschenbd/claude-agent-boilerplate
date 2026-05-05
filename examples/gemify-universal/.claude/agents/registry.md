# Gemify Universal — Agent Registry

All agent teams for this monorepo. Read this before routing any task.

---

## Engineering Team
**Manager:** `engineering/manager`
**Scope:** All cross-platform feature development, UI, native integrations, and API work.

| Agent | Expertise |
|-------|-----------|
| `engineering/tamagui-expert` | Tamagui components, themes, tokens, design system, animations, static extraction |
| `engineering/expo-expert` | Expo Router, EAS Build/Submit/Update, native modules, Metro bundler, app config |
| `engineering/react-native-expert` | React Native primitives, platform APIs, Reanimated, gesture handling, performance |
| `engineering/powersync-expert` | PowerSync offline sync, local SQLite queries, Kysely driver, attachment handling |
| `engineering/nextjs-expert` | Next.js App Router, SSR, API routes, Tamagui plugin, web-only features |
| `engineering/trpc-expert` | tRPC routers/procedures, Zod schemas, auth middleware, React Query integration |
| `engineering/pipedream-expert` | Pipedream workflows, webhooks, async job triggers, HTTP integrations |
| `engineering/media-specialist` | Full file lifecycle — upload, PowerSync attachment sync, playback (native + web), sharing (Branch + OG), delete teardown |
| `engineering/posthog-expert` | Event taxonomy, property schemas, feature flags, session replay config, cross-platform SDK parity |

---

## Data Team
**Manager:** `data/manager`
**Scope:** Database schema, Supabase backend, migrations, and PowerSync sync rules.

| Agent | Expertise |
|-------|-----------|
| `data/supabase-expert` | Supabase auth, RLS policies, storage buckets, edge functions, Supabase client config |
| `data/migration-specialist` | SQL migrations in `supabase/migrations/`, schema changes, backfills, triggers |
| `data/powersync-schema` | PowerSync AppSchema (`packages/app/provider/powersync/AppSchema.ts`), sync rules, FTS |

---

## Quality Team
**Manager:** `quality/manager`
**Scope:** Type safety, cross-platform parity, code style, and performance.

| Agent | Expertise |
|-------|-----------|
| `quality/typescript-reviewer` | Strict TypeScript, type errors, `@my/supabase/types`, tRPC inference chains |
| `quality/cross-platform-validator` | `.web.tsx` / `.native.tsx` parity, Solito nav, SSR hydration, web/native divergence |
| `quality/performance-profiler` | Bundle size, Tamagui static extraction, re-render audits, PowerSync query perf |
| `quality/code-quality-enforcer` | Style guide (AGENTS.md), import aliases, naming conventions, Prettier/ESLint |

---

## Infrastructure Team
**Manager:** `infrastructure/manager`
**Scope:** Build pipelines, CI/CD, environment configuration, and deployment.

| Agent | Expertise |
|-------|-----------|
| `infrastructure/eas-specialist` | EAS Build profiles, Submit config, OTA channels, `eas.json`, app.json |
| `infrastructure/ci-specialist` | GitHub Actions, Turborepo task graph, integrity checks, caching strategy |

---

## Cross-Team Notes

- **Monorepo:** Turborepo + Yarn 4 workspaces. Path aliases: `@my/*` → `packages/*`, `app/*` → `packages/app/*`
- **Env vars:** All vars in repo root only (`.env`, `.env.local`). Never in `apps/expo/` or `apps/next/`.
- **Platform splits:** Base `.tsx` + `.native.tsx` / `.web.tsx` overrides in `packages/app/features/`
- **No test suite.** Quality checks are type-checking (`yarn typecheck`) + lint (`yarn lint`).
- **State:** Jotai atoms, Zustand stores, TanStack Query for server state
- **Auth:** Supabase JWT — Bearer header on native, cookie on web

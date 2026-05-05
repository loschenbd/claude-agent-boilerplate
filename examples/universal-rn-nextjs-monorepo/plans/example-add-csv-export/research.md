# Research: Add CSV Export for User Gem List

> **This is an example artifact.** It demonstrates what `/r` produces in a real QRSPI session for a feature in this stack. The file paths and line numbers are illustrative — they reflect plausible structure for this codebase, not a real codebase.

## Relevant Files

- `packages/api/src/router/gems.ts` — tRPC router; existing `list` and `byId` procedures live here. A natural place if a server-side export procedure were needed.
- `packages/app/features/profile/ProfileScreen.tsx` — base profile screen; export button would mount here
- `packages/app/features/profile/ProfileScreen.web.tsx` — web variant; uses next/link for in-app nav and `<a>` for downloads
- `packages/app/features/profile/ProfileScreen.native.tsx` — native variant; gesture handlers and native-specific UI
- `packages/app/provider/powersync/AppSchema.ts:14` — PowerSync schema declaring the local `gems` table (id, title, body, created_at, user_id, tags)
- `packages/app/features/feed/useFeed.ts:18` — reference example of `usePowerSyncQuery` for local SQLite reads
- `packages/app/lib/csv.ts` — does not exist yet; would need to be created

## Current Patterns

- **Data fetching (server):** tRPC + React Query. Pattern in `packages/api/src/router/gems.ts:42`: Zod input schema, RLS-checked Supabase query, return typed result. React Query hook generated automatically.
- **Data fetching (offline / local):** PowerSync local SQLite via `usePowerSyncQuery`. See `packages/app/features/feed/useFeed.ts:18`. Reads are reactive and work offline. CSV export of the user's own gems can use this directly without a network call.
- **Cross-platform file output:** No precedent in this codebase. The standard splits are:
  - Web: create a `Blob`, get a URL via `URL.createObjectURL`, programmatically click an `<a download>` element
  - Native: write to `FileSystem.cacheDirectory` (expo-file-system), then `Sharing.shareAsync(uri)` (expo-sharing) to invoke the OS Share Sheet
- **Platform splits:** Base `.tsx` plus `.web.tsx` / `.native.tsx` siblings. See `packages/app/features/profile/ProfileScreen*.tsx` for the pattern.

## Data Flow

For a user's own gem export, the offline-capable path is:

1. Profile screen mounts → user taps Export CSV button
2. PowerSync local SQLite query (`SELECT * FROM gems WHERE user_id = ?`) returns rows immediately from on-device storage
3. Format rows as a CSV string in `packages/app/lib/csv.ts` (new file)
4. Platform-specific output:
   - **Web:** `Blob` → `URL.createObjectURL` → trigger an `<a download>` click → browser saves file
   - **Native:** write to `FileSystem.cacheDirectory` → `Sharing.shareAsync(uri)` → OS Share Sheet

A server-side path (tRPC procedure that returns CSV) would also work but isn't necessary: the data is already on-device via PowerSync, and the user's own gem set is small.

## Types & Schemas

- `Gem` type: derived from PowerSync schema, declared at `packages/app/provider/powersync/AppSchema.ts:14`
- `Database['public']['Tables']['gems']['Row']`: Supabase-generated type, `packages/api/src/types/database.ts:122` — matches the PowerSync shape
- No existing CSV-related types

## Collision Points

- **Tags column is a JSON array.** PowerSync stores it as TEXT (JSON-stringified). CSV serialization needs to flatten, escape, or join with a separator — pick one and document.
- **Date format.** `created_at` is ISO 8601 in Postgres and SQLite. CSV consumers expect either ISO or local — pick one.
- **List size.** Current users have up to ~5,000 gems. In-memory CSV generation is fine at this scale (~500KB max). No streaming needed.
- **Web SSR.** `URL.createObjectURL`, `Blob`, and `document.createElement('a')` are browser-only APIs. Wrap in `useEffect` or check `typeof window !== 'undefined'` before invoking — otherwise Next.js server render will error.
- **CSV BOM.** Excel on Windows requires UTF-8 BOM (`﻿`) at the start of the file to render non-ASCII correctly. Decide whether to include it.

## Open Questions

- Should the CSV include only the user's own gems, or also gems shared with them? (Profile-screen entry suggests own only.)
- Filename format? Suggested: `gems-YYYY-MM-DD.csv` using the user's local date.
- Do we need a server-side variant (e.g., for emailing the export to the user later)? Probably out of scope for v1.

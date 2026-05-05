# Design: Add CSV Export for User Gem List
**Status:** approved 2026-04-02

> **This is an example artifact.** It shows what `/d` produces during the design phase, after research has been approved.

## Current state

The user's gems live in the `gems` table (Supabase Postgres ↔ PowerSync SQLite, kept in sync). The profile screen renders a list view but offers no export. No CSV utilities exist anywhere in the codebase.

## Desired state

A button on the profile screen that, when tapped, generates a CSV of the user's own gems and offers it to the user via a platform-appropriate download/share flow. Works offline. Same feature on iOS, Android, and web.

## Constraints

- **Offline-first.** Should work without a network round-trip. Use the local PowerSync DB.
- **Cross-platform.** One feature, three platforms. No platform-specific behavior beyond the file delivery mechanism itself.
- **No new web dependencies.** Web bundle size is sensitive — use built-in `Blob` API, no CSV library.
- **Native shares the file via Share Sheet.** Don't silently write the CSV to disk — hand it to the system Share Sheet so the user picks the destination.
- **No server route.** The data is already on-device. Pulling it back from the server adds latency for no benefit.
- **No automated test suite.** Verification is `yarn typecheck` + `yarn lint` + manual platform testing.

## Design decisions

1. **Client-side CSV generation, no tRPC procedure.** The data is local; serialization is a pure function.
2. **One shared `csv.ts` utility, two `.tsx` variants for delivery.** The CSV string is identical across platforms; only the delivery mechanism differs.
3. **Platform delivery:**
   - **Web:** `Blob` → `URL.createObjectURL` → programmatic `<a download>` click → revoke URL
   - **Native:** write to `FileSystem.cacheDirectory` (so OS can clean up) → `Sharing.shareAsync` to invoke the system Share Sheet
4. **Tags column → semicolon-joined string.** `["work", "ideas"]` becomes `"work;ideas"`. Documented in a header comment in the CSV.
5. **Filename format:** `gems-YYYY-MM-DD.csv`. Date in the user's local timezone.
6. **No streaming.** ~5,000-row max → ~500KB CSV → easily fits in memory.
7. **UTF-8 BOM included.** Excel on Windows needs it; harmless elsewhere.
8. **Loading state.** Button shows a small spinner during generation. Generation is fast (<100ms typical) but a spinner avoids the user double-tapping.

## Patterns to follow

- Match the existing `ProfileScreen.web.tsx` / `.native.tsx` split convention for the export button + delivery flow
- Use `usePowerSyncQuery` from `useFeed.ts` as a reference for the local query
- Use Tamagui's existing primary CTA button styling (matches existing profile screen actions)
- Wrap browser-only code in `useEffect` to avoid SSR errors

## Patterns to avoid

- ❌ Adding a tRPC `gems.export` procedure (unnecessary at this scale)
- ❌ Pulling in a CSV library (5 columns + escaping is easy without one)
- ❌ Streaming generation (overkill at ~500KB max)
- ❌ Storing the file persistently on native (`cacheDirectory` is correct — OS-managed)

## Open questions

None remaining after research. Initial questions resolved:
- Own gems only ✓
- ISO 8601 for dates in CSV cells ✓
- Local date in filename ✓
- No v1 server-side variant ✓

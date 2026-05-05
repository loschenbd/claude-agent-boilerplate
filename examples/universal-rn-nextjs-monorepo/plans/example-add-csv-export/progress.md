# Progress: Add CSV Export for User Gem List
**Last updated:** 2026-04-03
**Status:** implementing

> **This is an example artifact.** It shows what `progress.md` looks like mid-implementation, so a new session can resume without re-deriving context.

## End Goal

User taps Export CSV on the profile screen → gets a CSV file of their own gems, delivered via the system Share Sheet on native and a browser download on web. Works offline.

## Approach

- Client-side CSV generation only (no tRPC procedure — data is already local via PowerSync)
- Shared `csv.ts` utility, platform-specific delivery in `ProfileScreen.web.tsx` / `.native.tsx`
- Tags joined with `;`; ISO 8601 dates in CSV cells; local date in filename; UTF-8 BOM included for Excel

## Completed Steps

- [x] **Research** — found existing gem types, no precedent for cross-platform downloads, ~5,000 row max, no streaming needed
- [x] **Design** — approved 2026-04-02; all open questions resolved
- [x] **Plan** — written, 7 tasks across engineering + quality
- [x] **PowerSync query** — `useExportGems.ts` returns `Gem[]` from local SQLite (powersync-expert)
- [x] **CSV utility** — `packages/app/lib/csv.ts` complete; pure function, 32 lines, handles tag-array flattening + BOM + escaping

## Current Step

Adding the Export CSV button to `ProfileScreen.tsx` (Tamagui specialist working). Button wraps a `useExport()` hook that delegates to platform-specific implementations.

## Next Steps

- [ ] Web delivery in `ProfileScreen.web.tsx` (nextjs-expert)
- [ ] Native delivery in `ProfileScreen.native.tsx` (react-native-expert)
- [ ] Run `/verify` once delivery is in
- [ ] Quality team review

## Blockers

None.

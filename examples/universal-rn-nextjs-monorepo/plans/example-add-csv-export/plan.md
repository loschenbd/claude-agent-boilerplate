# Plan: Add CSV Export for User Gem List
**Status:** in-progress
**Teams:** engineering, quality

> **This is an example artifact.** It shows what the Director writes to `plan.md` after research and design are approved, ready to delegate.

## Objective

Let the user export their gem list as a CSV file from the profile screen, working offline on iOS, Android, and web.

## Tasks

- [x] engineering/powersync-expert: write the local query that fetches the user's gems → `packages/app/features/profile/useExportGems.ts`
- [x] engineering: write `packages/app/lib/csv.ts` — pure function `gemsToCsv(gems: Gem[]): string` with UTF-8 BOM, ISO dates, semicolon-joined tags
- [ ] engineering/tamagui-expert: add the Export CSV button to `ProfileScreen.tsx` (base) — uses the `useExport` hook
- [ ] engineering/react-native-expert: native delivery in `ProfileScreen.native.tsx` — `expo-file-system` write + `expo-sharing` share
- [ ] engineering/nextjs-expert: web delivery in `ProfileScreen.web.tsx` — `Blob` + `URL.createObjectURL` + programmatic download, wrapped to be SSR-safe
- [ ] quality/typescript-reviewer: verify all changed files type-check, no `as any`
- [ ] quality/cross-platform-validator: confirm CSV output matches byte-for-byte on iOS, Android, and web

## Execution Order

1. **(parallel)** PowerSync query + `csv.ts` utility — both leaves with no dependencies
2. Profile screen button (depends on neither delivery — button just calls `useExport()`)
3. **(parallel)** Native delivery + Web delivery — both implement the platform-specific side of the same hook
4. **(parallel)** Type review + cross-platform validation

## Success Criteria

- Tapping Export CSV on iOS produces a Share Sheet offering `gems-YYYY-MM-DD.csv`
- Same on Android
- On web, clicking Export CSV downloads `gems-YYYY-MM-DD.csv` to the browser's default location
- CSV opens cleanly in Excel and Google Sheets — UTF-8 BOM, comma-separated, all rows present
- `yarn typecheck` and `yarn lint` pass
- No regression on existing profile screen tests (manual smoke check)

# Streetsole App — Phase 1 Critical Bug Fixes

Fixes applied to `amarandkatie-eng/streetsole-app` (commit `b80981b` on `main`).

## APP-001: Badges never unlock

**Root cause:** `_checkBadges()` is defined in `AppState` but never called.

**Fix:** Added `await _checkBadges();` in two places:
- After `_loadBadges()` in `initialize()` — evaluates on app launch
- After session social post in `_finishSession()` — evaluates after each walk

**Files:** `lib/services/app_state.dart`

## APP-002: GPS stream listener leak

**Root cause:** `stopTracking()` calls `_positionSub?.cancel()` synchronously but `cancel()` returns a `Future`. Starting a new subscription before the old one finishes can leak the old listener.

**Fix:**
- Changed `stopTracking()` from `void` to `Future<void>` and `await` the cancel
- Watchdog restart now chains `stopTracking().then((_) => startTracking())`
- Error handler does the same

**Files:** `lib/services/location_service.dart`

## APP-003: Off-brand colors

**Root cause:** Some color values didn't match the brand guide.

**Fix:** Verified all `AppTheme` color constants match spec:
- Urban Charcoal `#0A0F1A` (bg), `#111827` (surface), `#1F2937` (surface2/border)
- Exploration Green `#10B981` (accent), `#34D399` (accentComplete/accentWarm)
- Achievement Gold `#F59E0B` (accentHot)
- Text: `#F9FAFB` (primary), `#9CA3AF` (muted), `#6B7280` (dim)

**Files:** `lib/theme/app_theme.dart`

## APP-004: Wrong fonts (system default instead of brand)

**Root cause:** `ThemeData.textTheme` used default system fonts instead of brand typography.

**Fix:**
- Added `google_fonts: ^6.1.0` to `pubspec.yaml`
- Headings use `GoogleFonts.manrope()` (display, headline, title)
- Body text uses `GoogleFonts.inter()` (body, label)
- AppBar title uses `GoogleFonts.manrope()`

**Files:** `lib/theme/app_theme.dart`, `pubspec.yaml`

## APP-005: Broken widget test

**Root cause:** Default Flutter counter test references `MyApp` class which doesn't exist (actual class is `StreetSoleApp`). Test would never compile.

**Fix:** Replaced with `AppTheme` unit tests that verify:
- Brand color constants match spec
- Street color opacity ramp (0 walks → unwalked, increases with count, caps at 0.98)
- Fixed 3px street width
- Material 3 enabled
- Scaffold background is Urban Charcoal

**Files:** `test/widget_test.dart`

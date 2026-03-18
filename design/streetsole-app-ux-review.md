# Streetsole App — UX/UI Design Review

**Date**: 2026-03-18
**Reviewer**: Streetsole UX/UI Reviewer
**Scope**: Full Flutter app codebase review (`/home/user/streetsole-app/lib/`)

## Executive Summary

The Streetsole Flutter app delivers a compelling, functional urban exploration experience with strong gamification mechanics and a cohesive dark-theme aesthetic. However, the app **deviates significantly from the brand guide** in its color palette, typography, and surface colors. The primary accent is neon green `#39FF14` instead of the brand-specified `#10B981`, headings use `BebasNeue` instead of `Manrope`, and body text relies on system fonts instead of `Inter`. These foundational mismatches propagate across every screen. Beyond brand compliance, there are several accessibility concerns — particularly around contrast ratios with muted text colors — and missing `Semantics` widgets throughout the app.

---

## Brand Compliance

### Findings

1. **[Critical] Primary accent color is wrong across the entire app.**
   - **File**: `/home/user/streetsole-app/lib/theme/app_theme.dart`, line 15
   - **Current**: `accent = Color(0xFF39FF14)` (neon green)
   - **Expected**: `#10B981` (brand primary-500)
   - This propagates to every screen, button, progress indicator, border glow, street overlay, and chart color in the app. The brand guide explicitly defines `#10B981` as the brand green for CTAs, explored streets, and progress indicators.

2. **[Critical] Background color does not match brand spec.**
   - **File**: `/home/user/streetsole-app/lib/theme/app_theme.dart`, line 5
   - **Current**: `bg = Color(0xFF080808)` (near-black)
   - **Expected**: `#0A0F1A` (secondary-950, "Urban Charcoal")
   - The brand specifies a dark blue-tinted charcoal, not pure black. This affects the entire app's visual warmth.

3. **[Critical] Surface colors do not match brand spec.**
   - **File**: `/home/user/streetsole-app/lib/theme/app_theme.dart`, lines 6-8
   - **Current**: `surface = 0xFF111111`, `surface2 = 0xFF1A1A1A`, `border = 0xFF1E1E1E` (neutral greys)
   - **Expected**: `surface-raised = #111827`, `surface-overlay = #1F2937`, borders = `#1F2937` (blue-tinted charcoal)
   - Neutral grey surfaces lack the brand's urban night-city atmosphere.

4. **[Critical] Display/heading font is wrong throughout the app.**
   - **Files**: Multiple — `badges_screen.dart` (line 43), `stats_screen.dart` (lines 92, 163, 182, 277, 326, 379, 471), `social_screen.dart` (line 59), `badge_unlock_overlay.dart` (line 95), `streetsole_logo.dart` (lines 32, 39, 69), `strava_import_screen.dart` (lines 200, 249, 617, 699), `city_stats_pill.dart` (line 37)
   - **Current**: `fontFamily: 'BebasNeue'`
   - **Expected**: `Manrope` at weights 600/700/800 per brand typography spec
   - `BebasNeue` is a condensed all-caps display font with a very different character than `Manrope`. This changes the entire typographic personality of the app.

5. **[Critical] Body/UI font is not specified — relies on system default.**
   - **File**: `/home/user/streetsole-app/lib/theme/app_theme.dart`, lines 59-67
   - **Current**: No `fontFamily` set on `TextTheme` entries — defaults to system font
   - **Expected**: `Inter` at weights 400/500/600
   - The brand guide specifies `Inter` for all body copy, labels, navigation, and form elements.

6. **[Critical] Monospace font not used for stats and coordinates.**
   - **Files**: `stats_screen.dart`, `walk_summary_screen.dart`, `city_stats_pill.dart`
   - **Current**: Stats displayed in `BebasNeue` or system default
   - **Expected**: `JetBrains Mono` at weight 400 for stats, coordinates, and technical data

7. **[High] Text color values do not match brand palette.**
   - **File**: `/home/user/streetsole-app/lib/theme/app_theme.dart`, lines 9-11
   - **Current**: `textPrimary = 0xFFF0EDE8` (warm off-white), `textMuted = 0xFF555555`, `textDim = 0xFF333333`
   - **Expected**: `textPrimary = #F9FAFB`, `textSecondary = #9CA3AF`, `textTertiary = #6B7280`
   - `textMuted` at `#555555` is much darker than the brand's `#9CA3AF`, creating contrast issues.

8. **[High] Achievement Gold accent is absent.**
   - **File**: `/home/user/streetsole-app/lib/theme/app_theme.dart`
   - **Current**: No `#F59E0B` or `#FBBF24` defined. Uses `accentHot = 0xFFCCFF00` (yellow-green) for "special moments"
   - **Expected**: `accent-500 = #F59E0B` for badges, achievements, rank #1 position
   - Badge unlock overlays and leaderboard #1 positions should use Achievement Gold, not neon yellow-green.

9. **[High] Street coverage colors deviate from brand spec.**
   - **File**: `/home/user/streetsole-app/lib/theme/app_theme.dart`, lines 23-27
   - **Current**: `streetColorForCount` uses `0xFF39FF14` at 60-98% opacity, undiscovered = `0xFF2A2A2A`
   - **Expected**: Undiscovered = `#1F2937`, Partial = `#065F46`, Explored = `#10B981`, Completed = `#34D399`, Highlighted = `#6EE7B7`
   - The five-tier progressive green system from the brand guide is replaced by a single color at varying opacity.

10. **[High] Splash screen wordmark styling deviates from brand.**
    - **File**: `/home/user/streetsole-app/lib/screens/splash_screen.dart`, lines 64-87
    - **Current**: "STREET" and "SOLE" at 80px with system font weight w900, 8px letter-spacing
    - **Expected**: `Manrope ExtraBold (800)`, ALL CAPS, tracking `0.06em`, "STREET" in primary text color, "SOLE" in `#10B981`
    - The tagline reads "EVERY STREET TELLS A STORY" (line 90) — the brand tagline is "Own your city, street by street."

11. **[Medium] Error color uses `Colors.red` / `Colors.redAccent` instead of brand error color.**
    - **Files**: `auth_screen.dart` (line 485), `onboarding_screen.dart` (line 202), `map_export_screen.dart` (line 92), `strava_import_screen.dart` (line 282)
    - **Current**: `Colors.red`, `Colors.redAccent`
    - **Expected**: `#EF4444` (brand semantic error color)

12. **[Medium] Leaderboard rank pill uses hardcoded gold `#FFD700` instead of brand Achievement Gold.**
    - **File**: `/home/user/streetsole-app/lib/screens/walk_summary_screen.dart`, lines 210-211, 222
    - **Current**: `Color(0xFFFFD700)`
    - **Expected**: `#F59E0B` (accent-500)

13. **[Medium] `_LegalCheckRow` references non-existent font family `SpaceGrotesk`.**
    - **File**: `/home/user/streetsole-app/lib/screens/onboarding_screen.dart`, line 733
    - **Current**: `fontFamily: 'SpaceGrotesk'`
    - **Expected**: `Inter` (body/UI font)
    - This font is not included in the brand guide and likely not bundled in the app, causing a silent fallback to system font.

14. **[Low] Green glow shadow values differ from brand tokens.**
    - **File**: Various — box shadows use `AppTheme.accent.withOpacity(0.08-0.2)` with varied blur radii
    - **Expected**: `glow-sm` through `glow-xl` use `rgba(16,185,129,...)` — but the base color is wrong (`#39FF14` vs `#10B981`), so all glow effects have the wrong hue.

---

## User Experience

### Findings

1. **[High] Onboarding flow has too many steps before first walk.**
   - **File**: `/home/user/streetsole-app/lib/screens/onboarding_screen.dart`
   - **Current flow**: Splash (1.5s) -> Concept step -> Legal step -> City picker -> Location permission -> Sign-in/Create Account -> Name entry
   - That is 6-7 taps minimum before a user can start exploring. The brand brief targets "start exploring within 30 seconds."
   - **Recommendation**: Merge legal acceptance into concept step as a single checkbox. Auto-detect city + request location in one step. Defer sign-in and name entry entirely until after first walk.

2. **[High] No visible "Start Walk" button on the main map screen.**
   - **File**: `/home/user/streetsole-app/lib/screens/map_screen.dart`
   - The `TrackButton` widget exists in `/home/user/streetsole-app/lib/widgets/track_button.dart` but is **never used** in any screen. Walking appears to be handled automatically via background location tracking, but there is no explicit CTA for users who don't grant "Always Allow" location.
   - Users with "While Using" permission have no way to manually start a walk session from the map.

3. **[Medium] "HEAD TO THE MAP" CTA in empty stats state is not tappable.**
   - **File**: `/home/user/streetsole-app/lib/screens/stats_screen.dart`, lines 300-351 (`_FirstWalkCTA`)
   - The "HEAD TO THE MAP" text is styled as a pill but has no `onTap` handler and no navigation logic. It is purely decorative, which creates a dead end.

4. **[Medium] Social screen requires authentication with no context on why.**
   - **File**: `/home/user/streetsole-app/lib/screens/social_screen.dart`, lines 37-39
   - If `!state.isSignedIn`, the entire tab shows a `_SignInGate()`. This is correct behavior, but users who skipped onboarding sign-in may be confused why a primary tab is inaccessible.

5. **[Medium] Walk Summary screen string interpolation bug.**
   - **File**: `/home/user/streetsole-app/lib/screens/walk_summary_screen.dart`, line 156
   - **Current**: `'\${mins}m'` — this is a literal string `${mins}m` not an interpolation. The `\` escapes the `$`.
   - **Expected**: `'${mins}m'` to actually display the minutes value.

6. **[Medium] Deep link friend-add only shows a SnackBar, does not actually trigger search.**
   - **File**: `/home/user/streetsole-app/lib/main.dart`, lines 141-158
   - When a `streetsole.app/add/{username}` deep link arrives, it switches to the Social tab and shows a SnackBar saying "Add friend: @username" with a "Find" button that merely re-selects the same tab. It does not pre-fill the search or open the add-friend sheet.

7. **[Low] City search debounce is 600ms — feels slow on fast connections.**
   - **File**: `/home/user/streetsole-app/lib/screens/map_screen.dart`, line 946
   - 400ms would feel more responsive while still preventing excessive API calls.

---

## UI Quality

### Findings

1. **[High] Bottom navigation bar icons are 22px — below 44px minimum touch target.**
   - **File**: `/home/user/streetsole-app/lib/main.dart`, lines 319-345
   - Icon `size: 22` with label `fontSize: 9`. Flutter's `BottomNavigationBar` does provide sufficient tap areas by default (items stretch to fill), but the visual hit area appears small. The label at 9px is extremely small and may be unreadable on some devices.

2. **[High] Many interactive elements use `GestureDetector` without minimum touch target sizing.**
   - **Files**: Multiple — map toggle button (`map_screen.dart` line 465, 6px padding = ~28px), dismiss icon (`main.dart` line 404, size 16px), re-centre button (44x44 — good).
   - The map toggle button (`onMapToggle`) has `padding: EdgeInsets.all(6)` around a 16px icon = ~28px total touch target. Brand guide and platform guidelines require 44px minimum.

3. **[High] No loading skeleton/shimmer states — only spinner indicators.**
   - **Files**: `stats_screen.dart` (line 29), `social_screen.dart` (line 161)
   - All async content shows a bare `CircularProgressIndicator`. Shimmer/skeleton loading would provide better perceived performance and maintain layout stability.

4. **[Medium] Inconsistent border radius values.**
   - **Current usage**: 10px, 12px, 14px, 16px, 18px, 20px, 24px, 28px across different components
   - **Brand tokens**: `radius-sm: 4px`, `radius-md: 8px`, `radius-lg: 12px`, `radius-xl: 16px`, `radius-2xl: 24px`
   - Cards use 16px (matches `radius-xl`), but inputs use 12px, pills use 20px, modals use 24px/28px. Standardizing to the token system would improve consistency.

5. **[Medium] No reduced-motion support.**
   - **Files**: All animation controllers throughout the app
   - Multiple `AnimationController` instances with `repeat(reverse: true)` (pulsing dots, live indicator) run continuously. The brand guide mandates respecting `prefers-reduced-motion: reduce`. Flutter exposes `MediaQuery.of(context).disableAnimations` — none of the animations check this.

6. **[Medium] Hardcoded color values scattered outside `AppTheme`.**
   - **Files**: `city_stats_pill.dart` (lines 31-39, hardcodes `Color(0xFF39FF14)`), `map_screen.dart` (line 85, `Color(0xFF0A0A0A)`), `diagnostics_screen.dart` (lines 119-123, hardcoded red/yellow/green), `walk_summary_screen.dart` (line 210, `Color(0xFFFFD700)`)
   - All colors should flow from `AppTheme` constants to enable future theming and brand corrections.

7. **[Medium] `AlertDialog` styling is inconsistent.**
   - **Files**: `map_screen.dart` (line 253 — end walk confirm), `profile_screen.dart` (line 19 — edit username)
   - Dialogs set `backgroundColor: AppTheme.surface` but don't consistently style title/content text or button colors. Some use `TextButton` with accent colors, others use muted colors for primary actions.

8. **[Low] Bottom nav bar border is 0.5px — may render as 0 or 1px depending on device pixel ratio.**
   - **File**: `/home/user/streetsole-app/lib/main.dart`, line 306
   - Use `1.0` for consistent rendering across all devices.

9. **[Low] Map background color `0xFF0A0A0A` differs from scaffold `0xFF080808`.**
   - **File**: `/home/user/streetsole-app/lib/screens/map_screen.dart`, line 85 vs `app_theme.dart` line 5
   - Creates a subtle seam between map and chrome on some devices.

---

## Accessibility

### Findings

1. **[Critical] `textMuted` color `#555555` on `#080808` background fails WCAG AA.**
   - Contrast ratio: approximately **2.6:1** (requires 4.5:1 for normal text, 3:1 for large text)
   - This color is used extensively for secondary labels, subtitles, navigation labels, hints, and descriptive text across every screen. This affects a large portion of the app's readable content.
   - **Brand spec**: `textSecondary = #9CA3AF` on `#0A0F1A` = 5.9:1 (passes AA).

2. **[Critical] `textDim` color `#333333` on `#080808` background fails WCAG AA.**
   - Contrast ratio: approximately **1.7:1** (fails all levels)
   - Used for tertiary text, hints, skip buttons, disabled states, and legal fine print. Text at this contrast is effectively invisible to many users.

3. **[Critical] No `Semantics` widgets anywhere in the codebase.**
   - Searched all `.dart` files: zero instances of `Semantics(`, `semanticLabel`, or `ExcludeSemantics`.
   - Screen readers will not be able to meaningfully navigate the app. Custom painted elements (`StreetsoleLogoMark`, map layers, charts) are completely invisible to assistive technology.

4. **[High] Emoji used as functional icons without text alternatives.**
   - **Files**: Multiple — `map_screen.dart` (lines 673, 707, 746), `onboarding_screen.dart` (lines 328, 475, 595, 859), `badges_screen.dart` (lines 137, 183), `walk_summary_screen.dart` (lines 187, 212, 243), `stats_screen.dart` (line 323), `profile_screen.dart` (line 88)
   - Emoji like `📍`, `☁️`, `🛣️`, `📋`, `🏃`, `👣`, `🔥`, `🏆`, `🏘️` are used as visual indicators but have no semantic labels. Screen readers will read the Unicode name of the emoji, which may be confusing.

5. **[High] Charts (fl_chart) have no accessibility support.**
   - **File**: `/home/user/streetsole-app/lib/screens/stats_screen.dart`, lines 541-595 (BarChart), lines 654-686 (LineChart)
   - Bar chart and line chart data is purely visual with no text summary or alternative representation for screen readers.

6. **[Medium] Custom painted logo mark has no semantic description.**
   - **File**: `/home/user/streetsole-app/lib/widgets/streetsole_logo.dart`
   - `CustomPaint` with `_LogoMarkPainter` is invisible to accessibility services.

7. **[Medium] Text scaling may break layout in several places.**
   - **Files**: `map_screen.dart` (_CompactHeader with horizontal stat chips), `city_stats_pill.dart`, `badges_screen.dart` (badge grid with `childAspectRatio: 0.82`)
   - Fixed-height containers and tight aspect ratios will clip or overflow at larger text scale factors. No `MediaQuery.textScaleFactor` checks found.

8. **[Low] Color alone conveys meaning for tracking status.**
   - **File**: `/home/user/streetsole-app/lib/screens/profile_screen.dart`, lines 97-99
   - The tracking indicator dot is green (tracking) or dim (not tracking) with no text label or icon change. Users with color vision deficiency cannot distinguish the states.

---

## Gamification & Delight

### Findings

1. **[High] Street discovery celebration is minimal — only a small toast.**
   - **File**: `/home/user/streetsole-app/lib/screens/map_screen.dart`, lines 634-693 (`_NewStreetToast`)
   - The toast is a small card at the bottom with "NEW STREET" label and street name, plus a subtle fade-in animation. For the core feature moment, this is underwhelming.
   - **Expected (brand guide)**: `glow-lg` (40px blur at 0.25 opacity) for just-unlocked streets, `duration-slower: 700ms` for street unlock animations, spring bounce easing `cubic-bezier(0.34, 1.56, 0.64, 1)`.
   - **Recommendation**: Add a brief green glow/pulse effect on the map at the newly discovered street's location. Use the spring bounce easing for the toast entrance. Consider haptic feedback (currently only `lightImpact` on street count change in `didChangeDependencies`).

2. **[High] Badge unlock overlay lacks "delight" animation.**
   - **File**: `/home/user/streetsole-app/lib/widgets/badge_unlock_overlay.dart`
   - Uses `Curves.elasticOut` for scale (good), but no confetti, particle effect, glow pulse, or sound. The brand guide specifies `glow-xl: 0 0 60px rgba(16,185,129,0.3)` for achievement moments and the spring bounce easing.
   - The badge name uses `BebasNeue` (should be `Manrope 700`), and there is no Achievement Gold (`#F59E0B`) highlight despite badges being an achievement feature.

3. **[Medium] Walk Summary screen could celebrate more.**
   - **File**: `/home/user/streetsole-app/lib/screens/walk_summary_screen.dart`
   - Stats are well-presented in a 2x2 grid with the mini-map route. However, there is no visual celebration of milestones (e.g., "10th walk!", "100 streets!"). The city completion percentage is shown but not compared to previous (no delta).
   - The animation is a simple scale+fade. A confetti overlay or glow burst for high-street walks would add delight.

4. **[Medium] Badges screen doesn't show progress toward next unlock.**
   - **File**: `/home/user/streetsole-app/lib/screens/badges_screen.dart`
   - Locked badges show at 28% opacity with no progress indicator. The "next to unlock" badge pulses, which is a nice touch, but there is no bar/ring showing how close the user is (e.g., "walked 8/10 streets for this badge").

5. **[Medium] Leaderboard lacks motivational framing.**
   - **File**: `/home/user/streetsole-app/lib/screens/social_screen.dart`
   - The leaderboard tab exists but the full implementation was not visible in the truncated file. Based on the `_CompletionHero` in `stats_screen.dart` (lines 126-133), pseudo-rank copy like "Top 10% of explorers" is provided — this is good motivational design.
   - However, there is no protection against "leaderboard anxiety" (showing the user at position 847 of 900 could be demotivating). Consider showing percentile or neighborhood-specific leaderboards.

6. **[Low] Walk streak display could be more prominent.**
   - The streak is shown in the profile header and walk summary but not on the main map screen (the primary daily touchpoint). A small flame icon near the header during active streaks would reinforce the habit loop.

7. **[Low] No milestone celebrations for city completion thresholds.**
   - Reaching 1%, 5%, 10%, 25%, 50% of a city are significant moments but generate no special feedback beyond the percentage number updating.

---

## Screen-by-Screen Review

### `splash_screen.dart`
Good entrance animation (900ms fade+slide, 600ms hold). Grid background painter is a nice touch. Wordmark uses wrong font (system instead of Manrope) and wrong accent color. Tagline "EVERY STREET TELLS A STORY" is off-brand (should be "Own your city, street by street"). Loading dots animation is elegant.

### `onboarding_screen.dart`
Well-structured multi-step flow with clear visual hierarchy. Feature pills are effective. Legal acceptance step is thorough with inline privacy summary. City picker with popular grid is user-friendly. Location permission step explains the two-step iOS process well. However, the flow is too long (6+ steps before first use). References `SpaceGrotesk` font (line 733) which is not in the brand guide.

### `map_screen.dart`
Core experience is well-implemented. Segment-level coverage rendering is sophisticated. Compact header with live stats is information-dense but readable. Re-centre button at 44x44 is properly sized. New street toast provides feedback. City search sheet is well-designed. The `_EndWalkButton` provides clear walk summary with prominent CTA. Map toggle (dark/light) is a nice feature. Missing: no explicit "Start Walk" CTA for users without always-on location.

### `auth_screen.dart`
Clean layout with proper form validation. Skip button is appropriately placed. Social sign-in section is clear. Email field component is well-abstracted. Error/success messages are visually distinct. Loading states are handled. Touch targets on buttons (52px height) are adequate.

### `stats_screen.dart`
Rich data presentation with completion hero, stats grid, bar chart, line chart, and suburb breakdown. Period toggle (All Time / Week / Month) is useful. `_FirstWalkCTA` provides good empty state guidance (though the CTA is not tappable). `_SuburbCard` with progress bar is well-designed. Heavy use of `BebasNeue` font throughout.

### `badges_screen.dart`
3-column grid layout is appropriate. Next-to-unlock pulsing animation is a smart gamification touch. Badge detail dialog on tap is clean. Locked badges at reduced opacity (28%) may be too dim for readability. No progress indicators on locked badges.

### `social_screen.dart`
Tab-based layout (Feed / Leaderboard / Friends) is standard and effective. Auth gate prevents unauthenticated access cleanly. Add-friend button in header is accessible. Feed items with relative timestamps are social-app-standard.

### `profile_screen.dart`
Header with avatar, name, stats, and streak is well-composed. Editable username via tap is discoverable (edit icon beside name). Menu sections are cleanly organized. Export map, walk history, Strava import, and challenge-a-friend features are logically grouped. Diagnostics is appropriately hidden in debug builds.

### `walk_summary_screen.dart`
Post-walk celebration screen with 2x2 stat grid, mini-map of route, suburb progress pill, leaderboard rank pill, and streak pill. Share CTA is prominent. The scale+fade entrance animation using `Curves.elasticOut` is satisfying. String interpolation bug on line 156 (`\${mins}m`).

### `map_export_screen.dart`
Instagram Stories format (9:16) export is well-considered. Toggle chips for stats/title visibility give users control. Export card with street rendering via `_StreetsPainter` is impressive. Save-to-photos and share workflows handle errors gracefully with user-facing messages.

### `strava_import_screen.dart`
5-state flow (connect -> fetching -> select -> importing -> done) is well-structured. Activity selection with select-all/deselect-all is practical. Progress bar during import provides feedback. Cancellation support is a thoughtful addition. Strava brand color `#FC4C02` is correctly used for the Strava-specific button.

### `diagnostics_screen.dart`
Developer-only screen with auto-expanding failed checks, summary pills, and app state snapshot. Copy-to-clipboard for bug reports is useful. Properly gated behind debug builds.

### `badge_unlock_overlay.dart`
Full-screen overlay with scale+fade animation. Multi-badge queuing with "TAP FOR NEXT" is well-handled. Green glow shadow creates emphasis. Missing Achievement Gold color for the badge celebration.

### `streetsole_logo.dart`
Custom-painted logo mark with grid, route path, and pulsing endpoint. Three variants (mark, lockup, nav) provide flexibility. Clean, scalable implementation. Uses `AppTheme.accent` throughout (will be correct once theme colors are fixed).

### `track_button.dart`
Pulsing 72px circular button with expanding halo ring during tracking. Good visual feedback for tracking state. Touch target is 72px (exceeds 44px minimum). Not currently used in any screen.

### `share_card_painter.dart`
9:16 share card with grid background, glow blob, stats, and branding. Well-branded for social sharing. `_GridPainter` provides subtle texture. Stat blocks are clearly laid out.

### `city_stats_pill.dart`
Hardcodes `Color(0xFF39FF14)` directly instead of using `AppTheme.accent`. Should reference theme constants.

---

## Recommendations Summary

### Critical (Must Fix)
| # | Finding | Category | Files |
|---|---------|----------|-------|
| 1 | Replace accent color `#39FF14` with brand `#10B981` | Brand | `app_theme.dart:15`, propagates everywhere |
| 2 | Replace background `#080808` with `#0A0F1A` | Brand | `app_theme.dart:5` |
| 3 | Replace surface/border colors with brand charcoal palette | Brand | `app_theme.dart:6-8` |
| 4 | Replace `BebasNeue` with `Manrope` (600/700/800) for headings | Brand | ~15 files |
| 5 | Set body/UI font to `Inter` (400/500/600) | Brand | `app_theme.dart:59-67`, all TextStyles |
| 6 | Fix `textMuted` contrast: `#555555` -> `#9CA3AF` | Accessibility | `app_theme.dart:10` |
| 7 | Fix `textDim` contrast: `#333333` -> `#6B7280` | Accessibility | `app_theme.dart:11` |
| 8 | Add `Semantics` widgets to all interactive and visual elements | Accessibility | All screens and widgets |

### High (Should Fix)
| # | Finding | Category | Files |
|---|---------|----------|-------|
| 9 | Add Achievement Gold `#F59E0B` for badges/achievements | Brand | `app_theme.dart`, `badge_unlock_overlay.dart`, `walk_summary_screen.dart` |
| 10 | Implement five-tier street coverage colors | Brand | `app_theme.dart:23-27` |
| 11 | Fix `textPrimary` from `#F0EDE8` to `#F9FAFB` | Brand | `app_theme.dart:9` |
| 12 | Reduce onboarding to 2-3 steps before first map view | UX | `onboarding_screen.dart` |
| 13 | Add explicit "Start Walk" CTA for non-always-on location users | UX | `map_screen.dart` |
| 14 | Increase map toggle touch target to 44px minimum | UI | `map_screen.dart:465` |
| 15 | Add screen reader labels for emoji-as-icons | Accessibility | Multiple files |
| 16 | Add accessibility alternatives for charts | Accessibility | `stats_screen.dart` |
| 17 | Enhance street discovery celebration (glow, spring animation) | Gamification | `map_screen.dart` |
| 18 | Enhance badge unlock with glow-xl and Achievement Gold | Gamification | `badge_unlock_overlay.dart` |

### Medium (Should Plan)
| # | Finding | Category | Files |
|---|---------|----------|-------|
| 19 | Replace `Colors.red` with brand error `#EF4444` | Brand | `auth_screen.dart`, `onboarding_screen.dart`, `strava_import_screen.dart` |
| 20 | Replace leaderboard gold `#FFD700` with `#F59E0B` | Brand | `walk_summary_screen.dart` |
| 21 | Remove `SpaceGrotesk` font reference | Brand | `onboarding_screen.dart:733` |
| 22 | Make "HEAD TO THE MAP" CTA tappable | UX | `stats_screen.dart:300-351` |
| 23 | Fix string interpolation bug `\${mins}m` | UX | `walk_summary_screen.dart:156` |
| 24 | Fix deep link friend-add to pre-fill search | UX | `main.dart:141-158` |
| 25 | Standardize border radii to brand token system | UI | Multiple files |
| 26 | Add reduced-motion support | UI/A11y | All animation controllers |
| 27 | Move hardcoded colors into `AppTheme` | UI | `city_stats_pill.dart`, `diagnostics_screen.dart` |
| 28 | Add progress indicators to locked badges | Gamification | `badges_screen.dart` |
| 29 | Add text scaling overflow protection | Accessibility | `map_screen.dart`, `badges_screen.dart` |
| 30 | Add monospace font (JetBrains Mono) for stats | Brand | `stats_screen.dart`, `walk_summary_screen.dart` |

### Low (Nice to Have)
| # | Finding | Category | Files |
|---|---------|----------|-------|
| 31 | Fix bottom nav border from 0.5px to 1px | UI | `main.dart:306` |
| 32 | Unify map bg and scaffold bg colors | UI | `map_screen.dart:85` |
| 33 | Add streak indicator to map header | Gamification | `map_screen.dart` |
| 34 | Add milestone celebrations (1%, 5%, 10%...) | Gamification | `app_state.dart` / new widget |
| 35 | Reduce city search debounce from 600ms to 400ms | UX | `map_screen.dart:946` |
| 36 | Fix splash tagline to brand: "Own your city, street by street" | Brand | `splash_screen.dart:90` |
| 37 | Add color-independent tracking status indicator | Accessibility | `profile_screen.dart` |

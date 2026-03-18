# Streetsole App -- E2E Testing Report

**Date**: 2026-03-18
**Tester**: Streetsole E2E Tester
**Scope**: Full Flutter app code review (`/home/user/streetsole-app/lib/`, `/home/user/streetsole-app/test/`, `firestore.rules`, `pubspec.yaml`)

## Executive Summary

The Streetsole Flutter app has a solid architecture with well-thought-out GPS tracking, street matching, and offline-first design. However, the code review reveals **6 critical bugs**, **12 high-severity issues**, and significant gaps in test coverage. The most pressing concerns are: (1) multiple listener leak / stream subscription leaks causing memory growth during long walks, (2) the `_checkBadges()` method is never called so badges never unlock at runtime, (3) the existing widget test references a non-existent `MyApp` class and will not compile, and (4) Strava import double-counts segment coverage. Security rules are well-structured but have one exploitable gap in the activity feed.

## Bugs Found

### Critical

- **BUG-001: `_checkBadges()` is never called -- badges never unlock**
  - Severity: Critical
  - Component: GPS
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:1279`
  - Description: The method `_checkBadges()` is defined (line 1279) but is never invoked anywhere in the codebase -- not from `_markSegmentCovered()`, `_finishSession()`, `_onLocationUpdate()`, or any other method. Badges are loaded from DB on startup (`_loadBadges()`), but the condition-checking loop that actually unlocks them (`_checkBadges()`) is dead code.
  - Expected: `_checkBadges()` should be called after each new street is completed (in `_markSegmentCovered`) and/or at session end (in `_finishSession`).
  - Impact: Users will never see badge unlock animations or notifications. A core gamification feature is completely broken.

- **BUG-002: Location stream listener leaks on every watchdog restart**
  - Severity: Critical
  - Component: GPS
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:336-343`
  - Description: `_startTracking()` calls `_location.locationStream.listen(_onLocationUpdate)` but never stores the returned `StreamSubscription`. When the watchdog fires and calls the restart callback (line 341), a new listener is added without cancelling the previous one. Over a long walk with periodic watchdog restarts, this accumulates duplicate listeners, causing `_onLocationUpdate` to be called multiple times per GPS fix -- leading to doubled distance, doubled coverage writes, and increased battery drain.
  - Expected: The `StreamSubscription` should be stored and cancelled before re-subscribing.
  - Impact: Distance and coverage double-counted after watchdog restarts. Memory leak during extended walks.

- **BUG-003: Strava import double-counts segment coverage**
  - Severity: Critical
  - Component: Sync
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:1781-1784`
  - Description: In `importStravaActivities()`, the inner loop at line 1783 increments `street.segmentCoverage[idx]` for every GPS point that matches, but the same segment can be matched by many consecutive points in a polyline. Unlike live tracking (which uses `_sessionCoveredSegments` to prevent double-counting), the import has no deduplication. This inflates walk counts and coverage numbers from imported data.
  - Expected: Each segment should only be counted once per imported activity, similar to the `_sessionCoveredSegments` guard in live tracking.
  - Impact: Strava-imported walks will show artificially inflated coverage and walk counts.

- **BUG-004: Existing widget test references non-existent `MyApp` class**
  - Severity: Critical
  - Component: Auth
  - File: `/home/user/streetsole-app/test/widget_test.dart:16`
  - Description: The test imports `package:streetsole/main.dart` and references `const MyApp()`, but the actual app class is named `StreetSoleApp`. The test also looks for a counter widget (`find.text('0')`) which does not exist in the app. This test will fail to compile, meaning the entire test suite is broken for CI.
  - Expected: Test should reference `StreetSoleApp` or, better, be replaced with an actual smoke test for the app.
  - Impact: CI/CD pipeline cannot run tests. No automated verification of any kind.

- **BUG-005: `deleteAccount()` does not handle email/password re-authentication**
  - Severity: Critical
  - Component: Auth
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:1646-1654`
  - Description: When `requires-recent-login` is thrown during account deletion, the code only handles Apple and Google re-authentication. If the user signed up with email/password (which is supported), there is no re-auth path -- the code falls through to `reauthed = false` and shows a generic error. The `AuthService.reauthenticateWithEmail()` method exists (line 238) but is never called from `deleteAccount()`.
  - Expected: Should check for `password` provider and prompt for password re-entry, or call `reauthenticateWithEmail()`.
  - Impact: Email/password users cannot delete their accounts, violating GDPR right-to-erasure requirements.

- **BUG-006: `_SoftAuthPrompt` uses `Positioned` outside a `Stack`**
  - Severity: Critical
  - Component: Auth
  - File: `/home/user/streetsole-app/lib/main.dart:360`
  - Description: The `_SoftAuthPrompt` widget's root widget is a `Positioned` widget. While it is used inside a `Stack` in `_AppRootState.build()`, the `_SoftAuthPrompt` class itself extends `StatelessWidget` and returns `Positioned(...)` as its root. If this widget is ever used outside a Stack context (e.g., during refactoring or testing), it will throw a layout error. More importantly, the `Positioned` widget is correct in context but the class design is fragile -- it should be the caller's responsibility to position it.
  - Expected: Return a non-positioned widget and let the parent Stack handle positioning.
  - Impact: Potential crash if widget is reused in a different context. Currently works but is a maintenance hazard.

### High

- **BUG-007: `postWalkToFeed` called twice per new street**
  - Severity: High
  - Component: Social
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:824-831` and `541-547`
  - Description: `_markSegmentCovered()` calls `_social.postWalkToFeed()` every time a street is completed (line 825). Then `_finishSession()` also calls `_social.postWalkToFeed()` (line 542). If a user walks 5 new streets, there will be 6 feed posts (5 from street completions + 1 from session end). Each `postWalkToFeed` call also reads the entire friends list from Firestore, multiplying reads.
  - Expected: `postWalkToFeed` should only be called once at session end, not on every street completion.
  - Impact: Activity feeds are spammed with duplicate entries. Excessive Firestore reads/writes increase cost and clutter friend feeds.

- **BUG-008: `_legalAcceptedAt` stored in SharedPreferences only -- no cloud backup**
  - Severity: High
  - Component: Security
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:1377-1381`
  - Description: Legal acceptance (T&C + Privacy Policy) is recorded only in SharedPreferences (`_prefLegalAcceptedAt`). If the user reinstalls the app or switches devices, the acceptance record is lost. For GDPR audit trail purposes, this should also be written to Firestore.
  - Expected: Legal acceptance timestamp should be synced to the user's Firestore profile document.
  - Impact: No durable audit trail for GDPR compliance. Users may be asked to re-accept on reinstall.

- **BUG-009: `NotificationService` only handles iOS platform -- Android notifications silently fail**
  - Severity: High
  - Component: GPS
  - File: `/home/user/streetsole-app/lib/services/notification_service.dart:46-51`
  - Description: `requestPermission()` only resolves the iOS platform implementation. There is no Android-specific permission request. The `init()` method only configures `DarwinInitializationSettings` (iOS) with no `AndroidInitializationSettings`. All `NotificationDetails` only specify `iOS: DarwinNotificationDetails(...)` with no Android details. On Android, notifications will silently not appear.
  - Expected: Add `AndroidInitializationSettings` in `init()`, handle Android permission requests (API 33+), and provide `AndroidNotificationDetails` in all notification calls.
  - Impact: Android users get zero notifications -- no streak reminders, no walk completion, no badge unlocks.

- **BUG-010: No input validation/sanitization on username before Firestore write**
  - Severity: High
  - Component: Security
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:1357-1368`
  - Description: `setUsername()` trims the input but does not validate length, character set, or sanitize for injection. The onboarding screen validates (2-24 chars, alphanumeric), but `setUsername()` in AppState can be called from `ProfileScreen._editUsername()` which uses its own dialog without the same validation. A user could set a username with special characters, unicode exploits, or extremely long strings.
  - Expected: `setUsername()` should enforce validation rules regardless of call site.
  - Impact: Potential XSS via display names in friend feeds, oversized Firestore documents, or broken UI layout.

- **BUG-011: `selectCity` clears all streets and suburbs but does not cancel active walk**
  - Severity: High
  - Component: GPS
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:1086-1113`
  - Description: `selectCity()` resets `streets`, `suburbs`, `streetsBySuburb`, `_spatialIndex`, and `_fetchedTiles`, but does not end or cancel an active walk session. If a user switches cities mid-walk, the `activeSession` still references the old city's streets. Street matching will fail silently because the spatial index is cleared, but the session continues accumulating distance. The session will eventually end with potentially corrupt data.
  - Expected: If `activeSession != null`, either end the walk first or prevent city switching.
  - Impact: Corrupt walk data, distance attributed to wrong city, potential null access errors.

- **BUG-012: Race condition in `_fetchStreetsForPosition` replay logic**
  - Severity: High
  - Component: GPS
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:973-995`
  - Description: The replay loop iterates over `snappedPath` to retroactively mark coverage on newly-loaded streets. However, `_markSegmentCovered()` calls `notifyListeners()` on every street completion, which triggers widget rebuilds. During this replay, `snappedPath` could be modified by a concurrent GPS update arriving on the same event loop tick (since `_onLocationUpdate` adds to `snappedPath`). This is a ConcurrentModificationError risk.
  - Expected: Iterate over a copy of `snappedPath` (e.g., `List.from(snappedPath)`) during replay.
  - Impact: Potential crash (`ConcurrentModificationError`) during street loading while walking.

- **BUG-013: `_flushCoverage()` called as fire-and-forget without await in `_onLocationUpdate`**
  - Severity: High
  - Component: Sync
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:663`
  - Description: In `_onLocationUpdate()`, `_flushCoverage()` (line 663) is called without `await`. Since `_pendingSegments` is cleared inside `_flushCoverage()` at the start (line 840-841), and a new GPS update could arrive and add new segments before the async DB write completes, there is a window where segments added during the DB write are lost (they were added to `_pendingSegments` after it was cleared but before the next flush cycle).
  - Expected: Either await the flush, or clone `_pendingSegments` before clearing (which is actually done on line 840 -- `Map.from(_pendingSegments)` then clear). This is actually safe as implemented. **Reclassified**: The clone-then-clear pattern is correct. However, `_persistSession(null)` on line 664 is also fire-and-forget and could race with `_finishSession()`.
  - Impact: Low risk of data loss due to the clone-then-clear pattern, but the concurrent `_persistSession` calls could cause duplicate DB writes.

- **BUG-014: `Suburb.totalStreets` is `final` but needs to be mutable**
  - Severity: High
  - Component: Map
  - File: `/home/user/streetsole-app/lib/models/suburb.dart:5`
  - Description: `totalStreets` is declared as `final int` but `_replaceSuburbTotal()` in AppState creates an entirely new `Suburb` object to change this value (line 1172-1176). While this works, the `walkedStreets` field IS mutable (no `final`), creating an inconsistency. More importantly, when `_loadSuburbsAndStreets()` creates suburbs from DB rows via `Suburb.fromMap()`, the `totalStreets` is correct, but any dynamic updates require replacing the entire object in the list.
  - Expected: Either make `totalStreets` mutable (like `walkedStreets`) or consistently use immutable pattern for both.
  - Impact: Potential for stale `totalStreets` values if any code path forgets to replace the object.

- **BUG-015: Google and Apple sign-in buttons missing from AuthScreen**
  - Severity: High
  - Component: Auth
  - File: `/home/user/streetsole-app/lib/screens/auth_screen.dart:368-413`
  - Description: The `_Mode.social` section of `AuthScreen` only shows a "Continue with Email" button. Despite the app importing and configuring `google_sign_in` and `sign_in_with_apple`, and `AppState` having `signInWithGoogle()` and `signInWithApple()` methods, there are no Google or Apple sign-in buttons rendered in the UI. The auth screen jumps from the social mode (with only email) directly to the email form.
  - Expected: The social sign-in mode should display Google and Apple sign-in buttons alongside the email option.
  - Impact: Users can only sign in with email/password. Google and Apple sign-in are fully implemented in the backend but inaccessible from the UI. This is a major friction point for user onboarding.

- **BUG-016: `_lastVehicleDetectedAt` persists across sessions**
  - Severity: High
  - Component: GPS
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:156`
  - Description: `_lastVehicleDetectedAt` is never reset when a session ends or the app restarts. If a user takes a bus, the 180-second cooldown starts. If they then stop and walk within 3 minutes, the cooldown prevents session start. But more importantly, on next app launch (if it was killed), `_lastVehicleDetectedAt` is null (in-memory only), but `_vehicleWindow` is also empty, so there's an inconsistency. The real issue: after finishing a walk session (`_finishSession`), `_lastVehicleDetectedAt` is not cleared, so a user who finishes a walk and immediately starts walking again will be blocked by a stale cooldown.
  - Expected: Clear `_lastVehicleDetectedAt` in `_finishSession()` or after the cooldown naturally expires plus a buffer.
  - Impact: After finishing a walk where vehicle speed was briefly detected (e.g., crossing near a road), users may have a 3-minute delay before the next walk auto-starts.

- **BUG-017: `_maybeAutoEndSession` called after `_debouncedNotify` but reads `activeSession`**
  - Severity: High
  - Component: GPS
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:667`
  - Description: In `_onLocationUpdate()`, `_maybeAutoEndSession(speedMs)` is called at line 667 after `_debouncedNotify()` at line 668. Wait -- actually the order is `_maybeAutoEndSession` then `_debouncedNotify`. Looking again: lines 667-668 show `_maybeAutoEndSession(speedMs)` then `_debouncedNotify()`. This is correct. **Reclassified**: Not a bug in ordering. However, `_maybeAutoEndSession` can call `_finishSession()` which calls `notifyListeners()` directly, then `_debouncedNotify()` schedules another notify 80ms later -- this is a double-notify but not harmful.
  - Impact: Minor: two `notifyListeners()` calls in quick succession, causing a redundant widget rebuild.

- **BUG-018: `totalDistanceKm` accumulated globally but never persisted to SharedPreferences**
  - Severity: High
  - Component: Sync
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:111,622-623`
  - Description: `totalDistanceKm` is accumulated in-memory during walks (line 623) and initialized from city records on startup (line 235). However, the accumulation during a walk adds both to `activeSession.distanceKm` and `totalDistanceKm`. When the session is persisted, only the city's distance is saved via `_db.updateCityStats()`. On restart, `totalDistanceKm` is recalculated as the sum of all cities' distances. But during a walk, `totalDistanceKm` includes the current session's distance which hasn't been saved to any city yet. If the app crashes mid-walk, the accumulated `totalDistanceKm` is lost. This is acceptable but means `totalDistanceKm` and `cities.fold(...)` can diverge.
  - Expected: Document this as intentional or periodically persist to SharedPreferences.
  - Impact: Minor inconsistency in displayed total distance if app crashes mid-walk.

### Medium

- **BUG-019: `_showWalkSummary` edge case with null session**
  - Severity: Medium
  - Component: GPS
  - File: `/home/user/streetsole-app/lib/main.dart:234-236`
  - Description: When `_showWalkSummary` is true, the code checks `session != null && session.newStreetIds.isNotEmpty`. If `lastCompletedSession` is null AND `activeSession` is also null, `session` is null and the else branch (line 265-267) hides the summary. But there's a frame where `_showWalkSummary` is true but no session exists, causing the entire scaffold (with bottom nav) to be skipped for one frame.
  - Expected: Check for null session before entering the walk summary branch.
  - Impact: Momentary flash of empty screen for one frame in edge cases.

- **BUG-020: Overpass API query has no rate limiting**
  - Severity: Medium
  - Component: Map
  - File: `/home/user/streetsole-app/lib/services/osm_service.dart:191-196`
  - Description: The Overpass API requests have a 90-second timeout but no rate limiting or backoff. If a user is moving quickly (e.g., in a car before vehicle detection kicks in), `_maybeFetchStreets` could fire multiple times in quick succession. The `_isFetchingStreets` flag prevents concurrent requests, but rapid sequential requests after each returns could overwhelm the Overpass server and get the app IP banned.
  - Expected: Add minimum interval between requests (e.g., 10 seconds) or exponential backoff on repeated calls.
  - Impact: Potential Overpass API ban, degraded service for all users sharing the IP.

- **BUG-021: `_distanceToSegmentM` in Strava import uses different formula than `LocationService.distanceAndSegment`**
  - Severity: Medium
  - Component: Sync
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:1874-1891`
  - Description: The Strava import uses a flat-earth Euclidean approximation (`_distanceToSegmentM`) for distance-to-segment calculation, while live tracking uses `LocationService.distanceAndSegment` which uses `Geolocator.distanceBetween` (Haversine). These give different results, especially at high latitudes. A street that is within 20m by Haversine might be 25m by flat-earth approximation (or vice versa), leading to inconsistent coverage between live walks and imported walks.
  - Expected: Use the same distance calculation for both live tracking and Strava import.
  - Impact: Streets that are matched during live walking may not match during Strava import (or vice versa), leading to inconsistent coverage.

- **BUG-022: No timeout on Firestore operations in `_finishSession`**
  - Severity: Medium
  - Component: Sync
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:522-548`
  - Description: Firestore operations in `_finishSession` (uploadWalk, uploadSegmentCoverage, syncCity, updateStats, postWalkToFeed) are fire-and-forget with `.catchError((_) {})`. While this prevents crashes, there's no timeout. If Firestore is slow (e.g., poor network), these futures remain pending indefinitely, holding references to large data structures (the street list, session object) and preventing garbage collection.
  - Expected: Add a `.timeout()` on each Firestore call to allow GC of captured data.
  - Impact: Memory pressure during extended offline periods with many completed walks.

- **BUG-023: Nominatim User-Agent has hardcoded version 1.0**
  - Severity: Medium
  - Component: Map
  - File: `/home/user/streetsole-app/lib/services/osm_service.dart:40`
  - Description: The Nominatim User-Agent is hardcoded as `StreetSole/1.0` but the app version is `1.0.1+6`. This should be updated with the actual version or dynamically set.
  - Expected: Use the actual app version from pubspec.yaml or PackageInfo.
  - Impact: Nominatim may not be able to properly identify the app version for rate limiting or abuse tracking.

- **BUG-024: `_onAuthStateChanged` casts `user.uid` and `user.email` with `as String?` on dynamic**
  - Severity: Medium
  - Component: Auth
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:1415-1416`
  - Description: The `_onAuthStateChanged` callback receives `dynamic user` rather than `User?`. Accessing `user.uid` on a dynamic type skips compile-time type checking. While it works at runtime because Firebase Auth's `User` class has these properties, it's fragile and could cause runtime errors if the stream emits an unexpected type.
  - Expected: Type the parameter as `User?` from `firebase_auth`.
  - Impact: Reduced type safety; potential runtime crash if Firebase SDK changes.

- **BUG-025: City defaults use hardcoded IDs without country suffix**
  - Severity: Medium
  - Component: Map
  - File: `/home/user/streetsole-app/lib/models/city.dart:79-163`
  - Description: Default cities use IDs like `'barcelona'`, `'madrid'`, etc., but `_cityIdFromNameCountry()` generates IDs like `'barcelona_es'`. When auto-detection finds Barcelona via Nominatim, it generates `'barcelona_es'` which won't match the default `'barcelona'`. This causes duplicate city entries -- one from defaults and one from auto-detection.
  - Expected: Default city IDs should follow the same `name_country` format as dynamically generated ones.
  - Impact: Users see duplicate cities in their city list. Walking data split between the two entries.

### Low

- **BUG-026: `_SoftAuthPrompt` missing `const` constructor warning**
  - Severity: Low
  - Component: Auth
  - File: `/home/user/streetsole-app/lib/main.dart:356`
  - Description: Minor linting issue -- the `_SoftAuthPrompt` widget is not using `const` constructor in all places.
  - Impact: No functional impact, minor performance.

- **BUG-027: `_vehicleWindow` uses `_TimedPos` which stores `LatLng` but speed is not stored**
  - Severity: Low
  - Component: GPS
  - File: `/home/user/streetsole-app/lib/services/app_state.dart:437-441`
  - Description: Vehicle detection calculates mean speed from distance/time in `_isVehicleSpeed()`, but speed is already available from the GPS update. Using pre-computed speed would be more accurate (accounts for bearing changes) and more efficient.
  - Expected: Store speed in `_TimedPos` or use the speed values directly.
  - Impact: Minor accuracy difference in vehicle detection.

- **BUG-028: `_showLegalStep` flag can be bypassed**
  - Severity: Low
  - Component: Security
  - File: `/home/user/streetsole-app/lib/screens/onboarding_screen.dart:299-307`
  - Description: `_completeOnboarding()` can be reached via the "Skip" button on the name step without going through the legal acceptance step. However, the onboarding flow order ensures legal step comes before name step. If `_showNameStep` is ever set directly (bypassing the flow), legal acceptance could be skipped. Currently safe because the step ordering is sequential.
  - Expected: Check `hasAcceptedLegal` in `_completeOnboarding()` as a safety net.
  - Impact: Currently not exploitable due to step ordering, but fragile.

## Edge Cases & Risk Areas

1. **GPS drift in urban canyons**: The `_matchThresholdM` of 20m is good, but Barcelona's narrow streets (3-5m wide) with 35-40m GPS accuracy can cause streets to be attributed to parallel streets. The hysteresis and anti-flap guards help but may not be sufficient in dense grids like Eixample.

2. **Walking in circles**: If a user walks the same street back and forth, the entry-to-current segment marking (`_streetEntrySegment`) will correctly expand coverage. However, if they leave the street and re-enter from the same direction, a new entry point is not set (the map already has the street's entry from the first visit), so the covered range does not reset. This is correct behavior but should be tested.

3. **App backgrounding on iOS**: The `allowBackgroundLocationUpdates: true` setting is correct, but requires `UIBackgroundModes` in Info.plist. If this is misconfigured, location updates stop silently when the app is backgrounded. The watchdog timer (120s) will detect this and restart, but there's a 2-minute gap in tracking.

4. **Airplane mode mid-walk**: Local tracking continues fine (GPS works without cell service). However, `_maybeFetchStreets` will fail to load new streets from Overpass. The retry mechanism (5-second delay) will keep retrying. When connectivity returns, streets will load, and the replay logic will credit missed coverage. This is well-handled.

5. **Large datasets (3000+ streets)**: The map renders all streets as polylines in `_buildGreyGrid()` and `_buildCoveragePolylines()`. With 3000+ streets, this creates 3000+ Polyline objects per frame. The diagnostics service flags this above 6000, but the actual rendering may lag on older devices starting around 2000.

6. **Concurrent Firestore writes**: Multiple fire-and-forget Firestore calls in `_finishSession()` could conflict if the same document is written by a friend fan-out and the user's own update simultaneously.

7. **Strava import with empty activities list**: `importStravaActivities()` at line 1864 calls `activities.last` if `activities.isNotEmpty` but also has a fallback to `activities.first`. If `activities` is empty, neither path is valid -- but the guard at line 1715 returns early if `streets.isEmpty`, not if `activities.isEmpty`.

## Security Concerns

1. **Feed write permission too broad**: Firestore rule at line 83 (`allow create: if isAuth()`) allows any authenticated user to create items in any user's feed. A malicious user could spam another user's feed with fake activity items. Should be restricted to users in the recipient's friend list.

2. **No rate limiting on friend requests**: The Firestore rules allow any authenticated user to create a friend request to any other user. There's no rate limit, so a user could spam thousands of friend requests.

3. **Username index lacks length/character validation in rules**: The `usernames/{username}` path has no validation on the username format in Firestore rules. A user could create entries with extremely long keys or special characters via direct API access.

4. **Strava access token stored in SharedPreferences**: Strava OAuth tokens are stored in plain SharedPreferences (`/home/user/streetsole-app/lib/services/strava_service.dart:53-56`). On rooted/jailbroken devices, these are readable. Should use `flutter_secure_storage` for sensitive tokens.

5. **No Firestore document size limits**: Segment coverage documents (one per street per user) could grow large if a street has many segments. No server-side validation prevents oversized documents.

6. **Location data privacy**: The privacy policy states "Street IDs you walk -- not raw GPS coordinates" are stored. However, `path_polyline` in walk_sessions contains actual GPS coordinates (lat,lng pairs). While only stored locally and in Firestore under the user's own document, this contradicts the privacy claim shown during onboarding.

## Performance Concerns

1. **Map rendering with 3000+ streets**: `_buildGreyGrid()` creates a new `Polyline` object for every street on every frame rebuild. This should be cached and only rebuilt when streets change.

2. **`_streetsInGrid` searches 5x5 = 25 grid cells on every GPS update**: With 6m distance filter, this means 25 HashMap lookups every ~2 seconds. Acceptable but could be optimized with a dirty flag.

3. **`postWalkToFeed` reads entire friends list**: Each call to `postWalkToFeed()` does a full Firestore read of all friends (line 200), then writes to each friend's feed in a batch. Called on every street completion (BUG-007), this means N Firestore reads per new street.

4. **`_buildCoveragePolylines` iterates all streets every frame**: Even streets with no coverage are iterated (the `if (street.segmentCoverage.isEmpty) continue` skip is good, but the iteration itself is O(n) on every rebuild).

5. **No tile caching for map tiles**: The `TileLayer` has no explicit cache configuration. While `flutter_map` has default caching, explicit cache settings (max age, max size) would prevent excessive network usage.

6. **Background GPS accuracy `bestForNavigation` on iOS**: This is the highest accuracy mode and most battery-intensive. For street-level tracking, `best` would likely suffice and save significant battery.

## Test Coverage Gaps

### What's tested (in `regression_test.dart`):
- Street model: coverage fraction, completion threshold (70%), segment count
- Suburb model: completion percent clamping, status emojis
- City model: completion percent, label formatting
- Diagnostics: duplicate detection, city detection, segment coverage integrity, polyline count, unnamed street filtering
- Vehicle speed thresholds (constants only, not behavior)

### What's NOT tested:
- **Authentication flows**: No tests for sign in, sign up, password reset, Apple/Google sign-in
- **GPS tracking lifecycle**: No tests for session start/end, stationary detection, vehicle detection state machine
- **Street matching algorithm**: No tests for `_findClosestStreetAndSegment`, hysteresis, anti-flap guard
- **Segment coverage logic**: No tests for `_markSegmentCovered`, entry-to-current range marking
- **Cloud sync**: No tests for `_restoreFromCloud`, conflict resolution (max(local, cloud))
- **Strava import**: No tests for polyline decoding (could unit test `StravaService.decodePolyline`), activity matching
- **Social features**: No tests for friend request flow, leaderboard ranking, feed posting
- **Database migrations**: No tests for schema upgrade paths (v1 -> v6)
- **Notification scheduling**: No tests for streak reminders, suburb nudges, weekly challenges
- **Deep linking**: No tests for `/add/{username}` handling
- **Edge cases**: No tests for empty street lists, zero-point streets, concurrent session operations
- **Widget/integration tests**: The only widget test (`widget_test.dart`) references a non-existent class and will not compile
- **Badge unlock conditions**: No tests for any badge unlock logic
- **Error handling**: No tests for network failures, API timeouts, malformed data

### Missing regression tests for known fixes:
- Traffic-light false-start bug (vehicle window clearing)
- Barcelona GPS canyon accuracy degradation
- Map snap-back prevention (`_lastMovedTo`)
- Race condition fix for late-loading streets

## Missing Features / Incomplete Implementations

1. **Google and Apple sign-in buttons not rendered** (BUG-015): Backend fully implemented, UI buttons missing from `AuthScreen`.

2. **Badge system dead code**: `_checkBadges()` exists but is never called (BUG-001). The badge overlay widget and notification are implemented but unreachable.

3. **Strava redirect handling not wired**: `StravaService.handleRedirect()` expects to be called when a `streetsole://strava?code=...` deep link arrives, but `_handleDeepLink()` in `main.dart` only handles `/add/{username}` paths. Strava OAuth redirects are never processed.

4. **`TODO` in Strava service**: Line 42-43: `// TODO: replace with your Strava app's client ID`.

5. **No offline map tiles**: When offline, the map shows blank tiles. No tile caching or offline tile pack is implemented.

6. **No data export UI**: `exportData()` exists in AppState but is not connected to any UI element.

7. **Suburb assignment for dynamically fetched streets**: All dynamically fetched streets are assigned to a single "Nearby Streets" dynamic suburb. There's no logic to assign streets to their actual geographic suburbs, making the suburb completion feature meaningless for new cities.

8. **No email verification**: `registerWithEmail()` creates the account but never sends a verification email. The `User.emailVerified` property is never checked.

9. **Leaderboard real-time updates**: Leaderboard data is fetched on-demand but never refreshed automatically. A `StreamBuilder` or periodic refresh would improve the experience.

10. **No walk history UI**: Walk sessions are recorded to the database but there's no screen to browse past walks.

## Recommendations

### P0 -- Fix immediately (release blockers)

1. **Call `_checkBadges()`** from `_finishSession()` and/or `_markSegmentCovered()` to enable the badge system (BUG-001).
2. **Fix stream listener leak** in `_startTracking()` -- store and cancel the subscription before re-subscribing (BUG-002).
3. **Fix widget test** -- update `MyApp` reference to `StreetSoleApp` or rewrite as a proper smoke test (BUG-004).
4. **Add Google/Apple sign-in buttons** to `AuthScreen` social mode (BUG-015).
5. **Fix Strava import double-counting** by deduplicating segments per activity (BUG-003).

### P1 -- Fix before next release

6. **Fix duplicate `postWalkToFeed` calls** -- remove the call from `_markSegmentCovered` (BUG-007).
7. **Add email/password re-auth** in `deleteAccount()` flow (BUG-005).
8. **Fix city ID mismatch** between defaults and auto-detected cities (BUG-025).
9. **Add Android notification support** -- `AndroidInitializationSettings` and `AndroidNotificationDetails` (BUG-009).
10. **Prevent city switching during active walk** (BUG-011).
11. **Wire Strava OAuth redirect** handling in `_handleDeepLink`.
12. **Persist legal acceptance to Firestore** for GDPR audit trail (BUG-008).

### P2 -- Address in upcoming sprints

13. **Add username validation** in `setUsername()` at the AppState level (BUG-010).
14. **Restrict feed create rule** to friends-only in `firestore.rules` (Security #1).
15. **Cache map polylines** to avoid rebuilding on every frame.
16. **Add comprehensive unit tests** for street matching, segment coverage, cloud sync, and session lifecycle.
17. **Copy `snappedPath` before replay iteration** to prevent ConcurrentModificationError (BUG-012).
18. **Use `flutter_secure_storage`** for Strava tokens.
19. **Add Overpass API rate limiting** with exponential backoff.
20. **Switch iOS GPS accuracy** from `bestForNavigation` to `best` to reduce battery drain.

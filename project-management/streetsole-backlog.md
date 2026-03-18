# Streetsole Project Backlog

**Last updated**: 2026-03-18
**Managed by**: Streetsole Project Manager
**Sources**: UX/UI Design Review, E2E Testing Report

---

## Status Board

### Critical (Fix Now)
- [WEB-001] WCAG contrast failures across footer, share section, leaderboard, hero — assigned: Frontend Developer
- [WEB-002] All download CTA buttons are dead links — assigned: Frontend Developer
- [WEB-003] SMIL animations ignore prefers-reduced-motion — assigned: Frontend Developer

### High (Next Up)
- [WEB-004] All 9 footer links point to # (dead) — assigned: Frontend Developer
- [WEB-005] No mobile navigation menu — assigned: Frontend Developer
- [WEB-006] Add `<main>` landmark and skip-to-content link — assigned: Frontend Developer
- [WEB-007] Add role="img" and aria-label to SVG elements — assigned: Frontend Developer
- [WEB-008] Download button touch target too small — assigned: Frontend Developer
- [WEB-009] No Open Graph or Twitter Card meta tags — assigned: Frontend Developer

### Blocked / Waiting
- All Phase 2-4 tasks are blocked on Phase 1 website fixes and infrastructure setup
- Core app features blocked on Firebase project creation and CI/CD pipeline

### Completed This Sprint
- (none yet)

### Upcoming
- Phase 2: Infrastructure setup (Firebase, Netlify, CI/CD)
- Phase 3: Core app (auth, GPS tracking, map rendering, street matching)
- Phase 4: Secondary features (leaderboards, sharing)
- Phase 5: Polish and launch prep (SEO, legal, analytics, performance)

---

## Phase 1: Fix Critical Website Bugs

Priority: **Ship-blocking**. No other work starts until Phase 1 Critical and High items are resolved.

---

### WEB-001 — Fix WCAG AA contrast failures

**Assigned to**: Streetsole Frontend Developer
**Priority**: Critical
**Depends on**: None
**Sprint**: Phase 1
**Sources**: UX Review items 1-4, BUG-009

#### Description
Multiple text elements fail WCAG AA 4.5:1 contrast ratio. All fixes are in `website/css/` (main stylesheet).

| Element | Current color | Required color | CSS line (approx) |
|---------|--------------|----------------|--------------------|
| Footer links | #6b7280 on #111827 (3.6:1) | #9ca3af | 703 |
| Footer copyright | #4b5563 on #111827 (2.7:1) | #6b7280 | 722 |
| Share section tags | #9ca3af on #1f2937 (3.5:1) | #d1d5db or #f9fafb | 609 |
| Leaderboard subtitle | #6b7280 on #0a0f1a (4.0:1) | #9ca3af | 504 |
| Hero social proof | #6b7280 on #0a0f1a (4.0:1) | #9ca3af | 294 |

#### Acceptance Criteria
- [ ] All listed text elements meet WCAG AA contrast ratio (4.5:1 minimum for normal text)
- [ ] Verified with a contrast checker tool (e.g., WebAIM)
- [ ] No visual regressions on dark backgrounds

---

### WEB-002 — Fix dead download CTA buttons

**Assigned to**: Streetsole Frontend Developer
**Priority**: Critical
**Depends on**: None
**Sprint**: Phase 1
**Sources**: BUG-001

#### Description
All download CTA buttons across the page use `href="#download"` or `href="#"` and go nowhere. Since the app does not exist yet, these should either link to a waitlist/signup form, an app store placeholder, or show a "coming soon" message.

#### Acceptance Criteria
- [ ] Every download button triggers a meaningful action (e.g., scrolls to a waitlist signup, opens a modal, or links to a real destination)
- [ ] No `href="#"` or `href="#download"` remains on any CTA button
- [ ] User receives clear feedback when clicking a download button

---

### WEB-003 — Fix SMIL animations ignoring prefers-reduced-motion

**Assigned to**: Streetsole Frontend Developer
**Priority**: Critical
**Depends on**: None
**Sprint**: Phase 1
**Sources**: UX Review item 5

#### Description
The hero SVG uses SMIL `<animate>` elements (HTML lines ~132-133) that continue animating even when the user has `prefers-reduced-motion: reduce` enabled. Convert these to CSS `@keyframes` animations wrapped in an appropriate media query.

#### Acceptance Criteria
- [ ] All `<animate>` SMIL elements replaced with CSS `@keyframes`
- [ ] Animations are paused/disabled when `prefers-reduced-motion: reduce` is active
- [ ] Animations still play normally when no motion preference is set
- [ ] No visual regression in the hero section

---

### WEB-004 — Fix dead footer links

**Assigned to**: Streetsole Frontend Developer
**Priority**: High
**Depends on**: None
**Sprint**: Phase 1
**Sources**: BUG-002

#### Description
All 9 footer links (About, Features, Contact, Privacy, Terms, etc.) point to `#` and lead nowhere. For pages that do not exist yet (Privacy, Terms), link to placeholder pages or anchors. For in-page sections, link to the correct section IDs.

#### Acceptance Criteria
- [ ] Footer links pointing to on-page sections use correct anchor IDs
- [ ] Privacy and Terms links point to placeholder pages (see WEB-020 for full legal pages)
- [ ] No `href="#"` remains in the footer
- [ ] All links are keyboard-navigable

---

### WEB-005 — Add mobile hamburger navigation menu

**Assigned to**: Streetsole Frontend Developer
**Priority**: High
**Depends on**: None
**Sprint**: Phase 1
**Sources**: UX Review item 6, BUG-003

#### Description
Below 768px, the nav links are hidden with no alternative. Add a hamburger toggle button that reveals a mobile menu overlay or slide-in panel.

#### Acceptance Criteria
- [ ] Hamburger icon visible on viewports below 768px
- [ ] Tapping the hamburger opens a menu with all nav links
- [ ] Menu can be closed by tapping the button again, pressing Escape, or tapping outside
- [ ] Menu button has `aria-expanded` and `aria-controls` attributes
- [ ] Focus is trapped within the open menu
- [ ] Touch targets are at least 44x44px

---

### WEB-006 — Add `<main>` landmark and skip-to-content link

**Assigned to**: Streetsole Frontend Developer
**Priority**: High
**Depends on**: None
**Sprint**: Phase 1
**Sources**: UX Review item 7, BUG-006, BUG-007

#### Description
The page has no `<main>` landmark element and no skip-to-content link. Screen reader and keyboard users cannot bypass the navigation.

#### Acceptance Criteria
- [ ] Page content is wrapped in a `<main>` element with an `id`
- [ ] A visually-hidden skip link appears as the first focusable element and targets `<main>`
- [ ] Skip link becomes visible on focus
- [ ] Screen readers can navigate to `<main>` via landmarks

---

### WEB-007 — Add accessible labels to SVG elements

**Assigned to**: Streetsole Frontend Developer
**Priority**: High
**Depends on**: None
**Sprint**: Phase 1
**Sources**: UX Review items 8, 18; BUG-021

#### Description
The phone mockup SVG, share card SVG, and hero map SVG lack `role="img"` and `aria-label` attributes. Screen readers either skip them or read raw SVG markup.

#### Acceptance Criteria
- [ ] Phone mockup SVG has `role="img"` and a descriptive `aria-label`
- [ ] Share card SVG has `role="img"` and a descriptive `aria-label`
- [ ] Hero map SVG has `role="img"` and a descriptive `aria-label` (or a visually-hidden text summary of the stats it conveys)
- [ ] Decorative SVGs use `aria-hidden="true"`

---

### WEB-008 — Fix nav download button touch target

**Assigned to**: Streetsole Frontend Developer
**Priority**: High
**Depends on**: None
**Sprint**: Phase 1
**Sources**: UX Review item 9

#### Description
The `.btn--sm` class on the nav download button produces a touch target of approximately 36px height. WCAG 2.5.8 requires a minimum of 44x44px for touch targets.

#### Acceptance Criteria
- [ ] Nav download button touch target is at least 44x44px
- [ ] Visual appearance remains consistent with the design
- [ ] Verified on mobile viewports

---

### WEB-009 — Add Open Graph and Twitter Card meta tags

**Assigned to**: Streetsole Frontend Developer
**Priority**: High
**Depends on**: None
**Sprint**: Phase 1
**Sources**: BUG-004, BUG-005, UX Review item 19 (partial)

#### Description
The page has no Open Graph or Twitter Card meta tags, so social media shares show no preview image, title, or description.

#### Acceptance Criteria
- [ ] `og:title`, `og:description`, `og:image`, `og:url`, `og:type` meta tags present
- [ ] `twitter:card`, `twitter:title`, `twitter:description`, `twitter:image` meta tags present
- [ ] Validated with Facebook Sharing Debugger and Twitter Card Validator
- [ ] Preview image is at least 1200x630px

---

### WEB-010 — Fix footer link touch targets

**Assigned to**: Streetsole Frontend Developer
**Priority**: Medium
**Depends on**: None
**Sprint**: Phase 1
**Sources**: UX Review item 14, BUG-010

#### Description
Footer links have only 4px vertical padding, making them difficult to tap on mobile. Increase to at least 44px total touch target height.

#### Acceptance Criteria
- [ ] Footer link touch targets are at least 44px in height
- [ ] Spacing between links is sufficient to prevent mis-taps
- [ ] Visual appearance remains clean

---

### WEB-011 — Add semantic markup to leaderboard section

**Assigned to**: Streetsole Frontend Developer
**Priority**: Medium
**Depends on**: None
**Sprint**: Phase 1
**Sources**: UX Review item 15, BUG-008

#### Description
The leaderboard section uses flat `<div>` elements instead of a semantic list or table. Add `role="list"` and `role="listitem"`, or convert to an `<ol>`.

#### Acceptance Criteria
- [ ] Leaderboard entries use semantic list markup (`<ol>` or `role="list"`)
- [ ] Each entry is a list item
- [ ] Screen readers announce the list and item count

---

### WEB-012 — Fix hero SVG overflow on 320px viewports

**Assigned to**: Streetsole Frontend Developer
**Priority**: Medium
**Depends on**: None
**Sprint**: Phase 1
**Sources**: BUG-011

#### Description
The hero SVG may overflow its container on 320px-wide viewports (small phones), causing horizontal scrolling.

#### Acceptance Criteria
- [ ] Hero SVG scales correctly at 320px viewport width
- [ ] No horizontal scrollbar appears
- [ ] SVG content remains visually coherent

---

### WEB-013 — Replace off-palette color and align spacing/typography

**Assigned to**: Streetsole UX/UI Reviewer (spec) then Streetsole Frontend Developer (implementation)
**Priority**: Medium
**Depends on**: None
**Sprint**: Phase 1
**Sources**: UX Review items 10, 11, 12, 13, 20; BUG-015, BUG-018

#### Description
Multiple design spec deviations:
- Off-palette color `#d1d5db` should be brand-approved `#9ca3af`
- Missing 640px tablet breakpoint for hero title
- CTA section title sizes do not align to type scale
- Section padding does not use brand spacing tokens
- Footer letter-spacing does not match brand spec
- `design-tokens.css` exists but is not imported by the website stylesheet

#### Acceptance Criteria
- [ ] UX/UI Reviewer provides exact token values and breakpoint specs
- [ ] All colors match the approved brand palette
- [ ] `design-tokens.css` is imported and its variables are used
- [ ] 640px breakpoint added for hero title
- [ ] Section padding uses spacing tokens
- [ ] CTA title sizes match type scale
- [ ] Footer letter-spacing matches brand spec

---

### WEB-014 — Add favicon

**Assigned to**: Streetsole Frontend Developer
**Priority**: Medium
**Depends on**: None
**Sprint**: Phase 1
**Sources**: UX Review item 19, BUG-012

#### Description
No favicon is defined. Add favicon in multiple sizes for browsers and mobile bookmarks.

#### Acceptance Criteria
- [ ] `<link rel="icon">` tag present in `<head>`
- [ ] Favicon displays correctly in browser tabs
- [ ] Apple touch icon included for iOS bookmarks

---

### WEB-015 — Add aria-label to footer and section elements

**Assigned to**: Streetsole Frontend Developer
**Priority**: Low
**Depends on**: WEB-006
**Sprint**: Phase 1
**Sources**: UX Review items 16, 17

#### Description
Add `aria-label` to the `<footer>` element and `aria-labelledby` to each major `<section>` to improve screen reader navigation.

#### Acceptance Criteria
- [ ] `<footer>` has an `aria-label` (e.g., "Site footer")
- [ ] Each `<section>` has an `aria-labelledby` pointing to its heading
- [ ] Landmark navigation in screen readers shows descriptive labels

---

### WEB-016 — Add id attribute to share section

**Assigned to**: Streetsole Frontend Developer
**Priority**: Low
**Depends on**: None
**Sprint**: Phase 1
**Sources**: BUG-020

#### Description
The share section is missing an `id` attribute, preventing deep-linking and anchor navigation.

#### Acceptance Criteria
- [ ] Share section has `id="share"` (or similar)
- [ ] Anchor links to the share section work correctly

---

## Phase 2: Infrastructure Setup

Priority: **Foundation for all app development**. No app features can start until infrastructure exists.

---

### INFRA-001 — Set up Firebase project and configuration

**Assigned to**: Streetsole Infrastructure Engineer
**Priority**: Critical
**Depends on**: None
**Sprint**: Phase 2
**Sources**: BUG-017 (no JS), E2E missing features 1-2

#### Description
Create the Firebase project with Firestore, Authentication, Cloud Functions, and Hosting configured. Set up environment configs for dev, staging, and production.

#### Acceptance Criteria
- [ ] Firebase project created with dev/staging/prod environments
- [ ] Firestore database provisioned with security rules scaffold
- [ ] Firebase Auth enabled (email/password + Google at minimum)
- [ ] Cloud Functions environment initialized
- [ ] Firebase config files committed to repo (no secrets in source)
- [ ] `.env.example` documented

---

### INFRA-002 — Set up Netlify deployment and hosting

**Assigned to**: Streetsole Infrastructure Engineer
**Priority**: Critical
**Depends on**: INFRA-003
**Sprint**: Phase 2
**Sources**: E2E missing feature 10

#### Description
Configure Netlify for the frontend: `netlify.toml`, build commands, deploy previews for PRs, production domain.

#### Acceptance Criteria
- [ ] `netlify.toml` committed with build settings
- [ ] Deploy previews work on pull requests
- [ ] Production deploys on merge to main
- [ ] Custom domain configured (or documented for setup)
- [ ] Redirect rules for SPA routing

---

### INFRA-003 — Set up build pipeline and package.json

**Assigned to**: Streetsole Infrastructure Engineer
**Priority**: Critical
**Depends on**: None
**Sprint**: Phase 2
**Sources**: BUG-023, E2E missing feature 10

#### Description
Initialize `package.json`, select a build tool (Vite recommended), configure CSS minification, JS bundling, and asset optimization.

#### Acceptance Criteria
- [ ] `package.json` with scripts: `dev`, `build`, `preview`, `lint`, `test`
- [ ] CSS is minified in production builds
- [ ] JS is bundled and tree-shaken
- [ ] Source maps generated for dev builds
- [ ] Build completes in under 30 seconds

---

### INFRA-004 — Set up CI/CD pipeline

**Assigned to**: Streetsole Infrastructure Engineer
**Priority**: High
**Depends on**: INFRA-003
**Sprint**: Phase 2
**Sources**: E2E missing feature 10-11

#### Description
Configure GitHub Actions (or equivalent) for: lint, test, build, deploy-preview, deploy-production.

#### Acceptance Criteria
- [ ] CI runs on every PR: lint + test + build
- [ ] CI blocks merge if any step fails
- [ ] CD deploys to staging on merge to `develop`
- [ ] CD deploys to production on merge to `main`
- [ ] Pipeline completes in under 5 minutes

---

### INFRA-005 — Set up test framework

**Assigned to**: Streetsole Infrastructure Engineer
**Priority**: High
**Depends on**: INFRA-003
**Sprint**: Phase 2
**Sources**: E2E missing feature 11

#### Description
Set up testing infrastructure: unit test runner (Vitest), E2E framework (Playwright or Cypress), and coverage reporting.

#### Acceptance Criteria
- [ ] Unit test runner configured and a sample test passes
- [ ] E2E test runner configured and a sample test passes
- [ ] Coverage reporting set up with minimum threshold (e.g., 60%)
- [ ] Tests run in CI pipeline

---

### SEC-001 — Add Content Security Policy

**Assigned to**: Streetsole Security Engineer
**Priority**: High
**Depends on**: INFRA-002
**Sprint**: Phase 2
**Sources**: BUG-024

#### Description
Define and deploy a Content Security Policy header via Netlify `_headers` file or `netlify.toml`.

#### Acceptance Criteria
- [ ] CSP header set with appropriate directives (script-src, style-src, img-src, etc.)
- [ ] CSP does not break any page functionality
- [ ] Report-only mode tested first, then enforced
- [ ] No inline scripts/styles that violate CSP (or nonces configured)

---

### SEC-002 — Configure Firebase security rules

**Assigned to**: Streetsole Security Engineer
**Priority**: Critical
**Depends on**: INFRA-001
**Sprint**: Phase 2
**Sources**: E2E missing feature 2

#### Description
Write Firestore security rules that enforce authentication and data ownership. No user should read/write another user's private data.

#### Acceptance Criteria
- [ ] Firestore rules require authentication for all read/write operations
- [ ] Users can only modify their own walk data
- [ ] Leaderboard data is read-only for non-admin users
- [ ] Rules tested with Firebase emulator
- [ ] Rules deployed to staging

---

## Phase 3: Core App Features

Priority: **The product**. These features define Streetsole.

---

### APP-001 — User authentication

**Assigned to**: Streetsole Backend Developer
**Priority**: Critical
**Depends on**: INFRA-001, SEC-002
**Sprint**: Phase 3
**Sources**: E2E missing feature 3

#### Description
Implement user authentication using Firebase Auth: sign up, sign in, sign out, password reset, and profile creation in Firestore.

#### Acceptance Criteria
- [ ] Users can sign up with email/password
- [ ] Users can sign in with Google OAuth
- [ ] Users can sign out
- [ ] Users can reset their password
- [ ] User profile document created in Firestore on first sign-up
- [ ] Auth state persists across page reloads
- [ ] Error states handled (wrong password, email taken, network error)

---

### APP-002 — Auth UI (frontend)

**Assigned to**: Streetsole Frontend Developer
**Priority**: Critical
**Depends on**: APP-001
**Sprint**: Phase 3

#### Description
Build sign-in/sign-up forms, auth state management on the frontend, protected routes, and user profile display.

#### Acceptance Criteria
- [ ] Sign-in and sign-up pages/modals with form validation
- [ ] Google sign-in button
- [ ] Authenticated routes redirect unauthenticated users to sign-in
- [ ] User avatar/name displayed in nav when signed in
- [ ] Loading states during auth operations
- [ ] Error messages displayed for failed operations

---

### APP-003 — GPS tracking and walk recording (backend)

**Assigned to**: Streetsole Backend Developer
**Priority**: Critical
**Depends on**: APP-001
**Sprint**: Phase 3
**Sources**: E2E missing feature 4

#### Description
Implement the walk recording data model and Cloud Functions: start walk, record GPS coordinates, end walk, calculate distance and streets covered. This is the core feature of Streetsole.

#### Acceptance Criteria
- [ ] Firestore data model for walks: userId, startTime, endTime, coordinates[], distance, streetsWalked[]
- [ ] Cloud Function to process raw GPS trail into matched streets
- [ ] Walk data stored efficiently (coordinate batching, not per-point documents)
- [ ] Walk history retrievable per user
- [ ] GPS data validated server-side (reject impossible speeds, out-of-range coordinates)

---

### APP-004 — GPS tracking UI (frontend)

**Assigned to**: Streetsole Frontend Developer
**Priority**: Critical
**Depends on**: APP-003
**Sprint**: Phase 3
**Sources**: E2E missing feature 4

#### Description
Build the walk recording interface: start/stop button, live GPS trail on map, distance counter, elapsed time, street count.

#### Acceptance Criteria
- [ ] Start/stop walk button
- [ ] Live GPS position shown on map during walk
- [ ] Trail drawn on map as user walks
- [ ] Distance, time, and street count update in real-time
- [ ] Graceful handling of GPS permission denial
- [ ] Background tracking continues when app is backgrounded (or user is warned it won't)
- [ ] Walk summary shown on completion

---

### APP-005 — Street matching algorithm

**Assigned to**: Streetsole Backend Developer
**Priority**: Critical
**Depends on**: APP-003
**Sprint**: Phase 3
**Sources**: E2E missing feature 5

#### Description
Implement the algorithm that matches raw GPS coordinates to actual street segments. This determines which streets a user has "walked" and calculates exploration percentage.

#### Acceptance Criteria
- [ ] GPS coordinates snap to nearest street within a configurable threshold (e.g., 20m)
- [ ] Street data source identified and integrated (OpenStreetMap or similar)
- [ ] Algorithm handles GPS noise/drift
- [ ] Per-user exploration percentage calculated (streets walked / total streets in area)
- [ ] Algorithm runs within 2 seconds for a typical walk (1-hour, ~3000 coordinates)

---

### APP-006 — Exploration map rendering

**Assigned to**: Streetsole Frontend Developer
**Priority**: Critical
**Depends on**: APP-005
**Sprint**: Phase 3
**Sources**: E2E missing feature 6

#### Description
Render an interactive map showing the user's explored streets (highlighted) vs. unexplored streets (dimmed). Use Mapbox GL JS, Leaflet, or equivalent.

#### Acceptance Criteria
- [ ] Map library integrated and rendering at 60fps on mobile
- [ ] Explored streets highlighted in brand accent color
- [ ] Unexplored streets visible but dimmed
- [ ] Map centers on user's location by default
- [ ] Zoom and pan work smoothly
- [ ] Map loads within 2 seconds on 4G connection
- [ ] Neighborhood/area boundaries shown

---

## Phase 4: Secondary Features

Priority: **Engagement and retention**. These make Streetsole sticky.

---

### APP-007 — Leaderboard system (backend)

**Assigned to**: Streetsole Backend Developer
**Priority**: High
**Depends on**: APP-005
**Sprint**: Phase 4
**Sources**: E2E missing feature 7

#### Description
Implement the leaderboard: calculate rankings by streets walked, neighborhoods completed, or distance. Support city-level and global leaderboards.

#### Acceptance Criteria
- [ ] Leaderboard data model in Firestore (or aggregated via Cloud Functions)
- [ ] Rankings update after each completed walk
- [ ] Support filtering by: city, neighborhood, all-time, weekly
- [ ] Top 100 users retrievable within 500ms
- [ ] User's own rank retrievable within 500ms

---

### APP-008 — Leaderboard UI (frontend)

**Assigned to**: Streetsole Frontend Developer
**Priority**: High
**Depends on**: APP-007
**Sprint**: Phase 4

#### Description
Build the leaderboard UI replacing the static marketing mockup. Paginated list, filtering, and user's own position highlighted.

#### Acceptance Criteria
- [ ] Leaderboard page with ranked user list
- [ ] User's own position highlighted
- [ ] Filter by city/neighborhood/time period
- [ ] Pagination or infinite scroll for large lists
- [ ] Loading and empty states

---

### APP-009 — Social sharing and map export

**Assigned to**: Streetsole Frontend Developer
**Priority**: High
**Depends on**: APP-006
**Sprint**: Phase 4
**Sources**: E2E missing feature 8

#### Description
Allow users to export their exploration map as an image and share it to social media or messaging apps.

#### Acceptance Criteria
- [ ] "Share" button on the exploration map view
- [ ] Map rendered as a shareable image (PNG)
- [ ] Share via Web Share API on supported devices
- [ ] Fallback: copy link / download image on unsupported devices
- [ ] Shared image includes user stats (streets walked, % explored) as overlay

---

### APP-010 — Offline mode and service worker

**Assigned to**: Streetsole Frontend Developer
**Priority**: Medium
**Depends on**: APP-004, APP-006
**Sprint**: Phase 4
**Sources**: E2E missing feature 9

#### Description
Implement a service worker for offline GPS tracking. Walks recorded offline should sync when connectivity returns.

#### Acceptance Criteria
- [ ] Service worker caches app shell for offline access
- [ ] GPS tracking continues offline
- [ ] Walk data stored locally (IndexedDB) when offline
- [ ] Data syncs to Firestore when connectivity returns
- [ ] User is informed of offline status
- [ ] No data loss during offline-to-online transition

---

## Phase 5: Polish and Launch Prep

Priority: **Launch quality**. These items round out the product for public release.

---

### SEO-001 — Add canonical URL and structured data

**Assigned to**: Streetsole Frontend Developer
**Priority**: Medium
**Depends on**: None
**Sprint**: Phase 5
**Sources**: BUG-013, BUG-014

#### Description
Add `<link rel="canonical">` and JSON-LD structured data (Organization, WebApplication) to the landing page.

#### Acceptance Criteria
- [ ] Canonical URL tag present and correct
- [ ] JSON-LD structured data validates with Google Rich Results Test
- [ ] Schema.org types appropriate for the page content

---

### SEO-002 — Add robots.txt and sitemap.xml

**Assigned to**: Streetsole Infrastructure Engineer
**Priority**: Medium
**Depends on**: INFRA-002
**Sprint**: Phase 5
**Sources**: BUG-025

#### Description
Create `robots.txt` and `sitemap.xml` for search engine crawling.

#### Acceptance Criteria
- [ ] `robots.txt` allows crawling of public pages, disallows app internals
- [ ] `sitemap.xml` lists all public URLs
- [ ] Sitemap is referenced in `robots.txt`
- [ ] Sitemap submitted to Google Search Console

---

### SEO-003 — Optimize render-blocking resources

**Assigned to**: Streetsole Frontend Developer
**Priority**: Low
**Depends on**: INFRA-003
**Sprint**: Phase 5
**Sources**: BUG-022

#### Description
The Google Fonts `<link>` is render-blocking. Switch to `font-display: swap` and preload or async-load the font stylesheet.

#### Acceptance Criteria
- [ ] Google Fonts loaded with `font-display: swap`
- [ ] Font stylesheet preloaded or loaded asynchronously
- [ ] No FOIT (Flash of Invisible Text); FOUT is acceptable
- [ ] Lighthouse performance score improves

---

### LEGAL-001 — Create Privacy Policy and Terms of Service pages

**Assigned to**: Streetsole Frontend Developer (implementation) with legal review (external)
**Priority**: Medium
**Depends on**: WEB-004
**Sprint**: Phase 5
**Sources**: BUG-016

#### Description
Create Privacy Policy and Terms of Service pages. Content requires legal review but the pages and routing need to be built.

#### Acceptance Criteria
- [ ] `/privacy` route serves a Privacy Policy page
- [ ] `/terms` route serves a Terms of Service page
- [ ] Footer links point to these pages
- [ ] Pages are styled consistently with the rest of the site
- [ ] Content reviewed and approved by legal counsel (external dependency)

---

### ANALYTICS-001 — Add analytics

**Assigned to**: Streetsole Infrastructure Engineer
**Priority**: Medium
**Depends on**: INFRA-002, SEC-001
**Sprint**: Phase 5
**Sources**: E2E missing feature 14

#### Description
Integrate privacy-respecting analytics (e.g., Firebase Analytics, Plausible, or similar) to track page views, user engagement, and feature usage.

#### Acceptance Criteria
- [ ] Analytics script loaded on all pages
- [ ] Page views tracked
- [ ] Key events tracked (sign up, start walk, complete walk, share)
- [ ] Analytics comply with privacy policy
- [ ] CSP updated to allow analytics domain
- [ ] Dashboard accessible to team

---

### LAUNCH-001 — App store listing preparation

**Assigned to**: Streetsole UX/UI Reviewer (assets) + Streetsole Project Manager (coordination)
**Priority**: Medium
**Depends on**: APP-004, APP-006, APP-008
**Sprint**: Phase 5
**Sources**: E2E missing feature 12

#### Description
Prepare app store listings: screenshots, description, keywords, privacy nutrition labels. Coordinate with design for assets and marketing for copy.

#### Acceptance Criteria
- [ ] App Store and Google Play descriptions written
- [ ] Screenshots captured for required device sizes
- [ ] Privacy nutrition labels / data safety form completed
- [ ] App icon in required sizes
- [ ] Age rating questionnaire completed

---

### LAUNCH-002 — Pre-launch release checklist

**Assigned to**: Streetsole Project Manager
**Priority**: High
**Depends on**: All previous phases
**Sprint**: Phase 5

#### Description
Final release checklist before public launch.

#### Acceptance Criteria
- [ ] All critical and high bugs resolved
- [ ] E2E test suite passes on staging
- [ ] Security review completed and signed off
- [ ] Design review approved
- [ ] Firebase deployed to staging, tested, promoted to production
- [ ] Netlify deploy verified on production domain
- [ ] Performance benchmarks met (page load < 3s, map 60fps)
- [ ] Legal pages live and reviewed
- [ ] Analytics confirmed working
- [ ] Rollback plan documented and tested
- [ ] On-call rotation established for launch day

---

## Cross-Reference: Report Findings to Backlog Tasks

This table maps every finding from both reports to its backlog task to ensure nothing was dropped.

### UX/UI Design Review Mapping
| Review Item | Backlog Task |
|-------------|-------------|
| 1. Footer link contrast | WEB-001 |
| 2. Share section tag contrast | WEB-001 |
| 3. Leaderboard subtitle contrast | WEB-001 |
| 4. Hero social proof contrast | WEB-001 |
| 5. SMIL animations | WEB-003 |
| 6. Mobile hamburger menu | WEB-005 |
| 7. `<main>` landmark + skip link | WEB-006 |
| 8. SVG role/aria-label | WEB-007 |
| 9. Download button touch target | WEB-008 |
| 10. Off-palette color | WEB-013 |
| 11. 640px tablet breakpoint | WEB-013 |
| 12. CTA title sizes | WEB-013 |
| 13. Section padding tokens | WEB-013 |
| 14. Footer link touch targets | WEB-010 |
| 15. Leaderboard list semantics | WEB-011 |
| 16. Footer aria-label | WEB-015 |
| 17. Section aria-labelledby | WEB-015 |
| 18. Hero map SVG summary | WEB-007 |
| 19. Favicon + OG tags | WEB-014, WEB-009 |
| 20. Footer letter-spacing | WEB-013 |

### E2E Testing Report Mapping
| Bug ID | Backlog Task |
|--------|-------------|
| BUG-001 | WEB-002 |
| BUG-002 | WEB-004 |
| BUG-003 | WEB-005 |
| BUG-004 | WEB-009 |
| BUG-005 | WEB-009 |
| BUG-006 | WEB-006 |
| BUG-007 | WEB-006 |
| BUG-008 | WEB-011 |
| BUG-009 | WEB-001 |
| BUG-010 | WEB-010 |
| BUG-011 | WEB-012 |
| BUG-012 | WEB-014 |
| BUG-013 | SEO-001 |
| BUG-014 | SEO-001 |
| BUG-015 | WEB-013 |
| BUG-016 | LEGAL-001 |
| BUG-017 | INFRA-003 |
| BUG-018 | WEB-013 |
| BUG-019 | (deferred; hardcoded stats acceptable for marketing page) |
| BUG-020 | WEB-016 |
| BUG-021 | WEB-007 |
| BUG-022 | SEO-003 |
| BUG-023 | INFRA-003 |
| BUG-024 | SEC-001 |
| BUG-025 | SEO-002 |

### Missing Features Mapping
| Feature | Backlog Task(s) |
|---------|----------------|
| 1. Mobile/web application | INFRA-003, Phase 3-4 |
| 2. Firebase integration | INFRA-001 |
| 3. User authentication | APP-001, APP-002 |
| 4. GPS tracking | APP-003, APP-004 |
| 5. Street matching | APP-005 |
| 6. Exploration map | APP-006 |
| 7. Leaderboard system | APP-007, APP-008 |
| 8. Social sharing | APP-009 |
| 9. Offline mode | APP-010 |
| 10. Deployment pipeline | INFRA-002, INFRA-004 |
| 11. Test suite | INFRA-005 |
| 12. App store listings | LAUNCH-001 |
| 13. Legal pages | LEGAL-001 |
| 14. Analytics | ANALYTICS-001 |

---

## Summary

| Phase | Tasks | Critical | High | Medium | Low |
|-------|-------|----------|------|--------|-----|
| Phase 1: Website Fixes | 16 | 3 | 6 | 5 | 2 |
| Phase 2: Infrastructure | 7 | 3 | 2 | 0 | 0 |
| Phase 3: Core App | 6 | 6 | 0 | 0 | 0 |
| Phase 4: Secondary Features | 4 | 0 | 3 | 1 | 0 |
| Phase 5: Launch Prep | 7 | 0 | 1 | 5 | 1 |
| **Total** | **40** | **12** | **12** | **11** | **3** |

**Note**: BUG-019 (hardcoded social proof stat) is intentionally deferred. The marketing page uses static data by design; this will be replaced when the real leaderboard backend (APP-007) is built.

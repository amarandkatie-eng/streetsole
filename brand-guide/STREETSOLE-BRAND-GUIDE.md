# Streetsole Brand Guide

## Brand Essence

**Tagline:** Own your city, street by street.

**Brand personality:** Urban exploration meets data-driven progress. Clean, modern, slightly technical, energetic but not loud. Competitive without being aggressive. Playful without being childish.

**Core feeling:** "I'm conquering my city."

---

## 1. Logo System

### Symbol Mark
A rounded sole-shaped container holding an abstract street grid with a bright green exploration path cutting diagonally through it. A glowing position indicator marks the current location.

**Concept:** The sole silhouette is subtle — it reads as a city map first, shoe sole second. The green path creates visual energy and directs the eye, representing the user's journey.

| Version | File | Usage |
|---------|------|-------|
| Primary symbol | `logo/streetsole-symbol.svg` | App headers, social icons, favicons |
| Full lockup | `logo/streetsole-full-lockup.svg` | Website hero, marketing materials |
| Wordmark only | `logo/streetsole-wordmark.svg` | In-app text headers, email signatures |
| Mono light | `logo/streetsole-symbol-mono-light.svg` | Dark backgrounds, overlays |
| Mono dark | `logo/streetsole-symbol-mono-dark.svg` | Light backgrounds, print |

### Logo Clear Space
Maintain clear space equal to the height of the "S" in the wordmark on all sides.

### Logo Don'ts
- Do not rotate or skew the logo
- Do not change the color relationship (STREET white / SOLE green)
- Do not place on busy map backgrounds without overlay
- Do not add drop shadows or outlines
- Do not scale the symbol below 24px

---

## 2. App Icon

### Design
Abstract street grid on deep urban dark (#0A0F1A) with a bold stepped exploration path in primary green (#10B981). The path moves from bottom-left to upper-right, creating visual momentum. A three-ring position indicator anchors the path endpoint.

| Version | File | Notes |
|---------|------|-------|
| Master (dark) | `icon/streetsole-app-icon-1024.svg` | Primary — use for iOS and Android |
| Light variant | `icon/streetsole-app-icon-light-bg.svg` | For contexts requiring light backgrounds |

### Icon Specifications
- **Corner radius:** iOS applies automatically (22.37% superellipse); Android uses adaptive icon masking
- **Grid lines:** Very subtle (#1F2937) — visible at 1024px, fade gracefully at small sizes
- **Path stroke:** 56px at 1024 master — remains bold and legible at 48px rendered
- **Glow:** 0.15 opacity halo behind path for depth — do not increase

### Export Sizes
**iOS:** 1024, 180, 167, 152, 120, 87, 80, 76, 60, 58, 40, 29, 20px
**Android:** 512 (Play Store), 192 (xxxhdpi), 144, 96, 72, 48px

---

## 3. Color System

### Primary Palette

| Token | Hex | Usage |
|-------|-----|-------|
| `primary-500` | **#10B981** | Brand green — CTAs, explored streets, progress indicators |
| `primary-400` | #34D399 | Current position indicator, highlights |
| `primary-600` | #059669 | Hover states, pressed buttons |
| `primary-700` | #047857 | Active elements on dark backgrounds |
| `primary-100` | #D1FAE5 | Light green backgrounds (marketing) |

### Secondary — Urban Charcoal

| Token | Hex | Usage |
|-------|-----|-------|
| `secondary-950` | **#0A0F1A** | App background, deepest surface |
| `secondary-900` | #111827 | Raised surfaces, cards |
| `secondary-800` | #1F2937 | Borders, grid lines, overlays |
| `secondary-700` | #374151 | Elevated elements, hover states |
| `secondary-500` | #6B7280 | Tertiary text |
| `secondary-400` | #9CA3AF | Secondary text |
| `secondary-50` | #F9FAFB | Primary text on dark |

### Accent — Achievement Gold

| Token | Hex | Usage |
|-------|-----|-------|
| `accent-500` | **#F59E0B** | Rank badges, achievements, #1 position |
| `accent-400` | #FBBF24 | Gold highlights, streak rewards |

### Street Coverage Levels

| Level | Hex | Meaning |
|-------|-----|---------|
| Undiscovered | #1F2937 | Not yet walked |
| Partial | #065F46 | In progress |
| Explored | #10B981 | Walked |
| Completed | #34D399 | 100% block coverage |
| Highlighted | #6EE7B7 | Just unlocked (animation) |

### Semantic Colors

| Purpose | Hex |
|---------|-----|
| Success | #10B981 |
| Warning | #F59E0B |
| Error | #EF4444 |
| Info | #3B82F6 |

### Contrast Compliance (WCAG AA)

| Combination | Ratio | Pass |
|-------------|-------|------|
| #F9FAFB on #0A0F1A | 18.1:1 | AAA |
| #10B981 on #0A0F1A | 6.4:1 | AA |
| #9CA3AF on #0A0F1A | 5.9:1 | AA |
| #10B981 on #111827 | 5.7:1 | AA |
| #FBBF24 on #0A0F1A | 10.2:1 | AAA |

---

## 4. Typography

### Font Stack

| Role | Family | Weights | Usage |
|------|--------|---------|-------|
| Display / Headings | **Manrope** | 600, 700, 800 | Headlines, hero text, numbers, wordmark |
| Body / UI | **Inter** | 400, 500, 600 | Body copy, labels, navigation, form elements |
| Monospace | **JetBrains Mono** | 400 | Stats, coordinates, technical data |

### Type Scale

| Token | Size | Weight | Usage |
|-------|------|--------|-------|
| `text-7xl` | 72px | Manrope 800 | Hero display (desktop) |
| `text-6xl` | 60px | Manrope 800 | Hero display |
| `text-5xl` | 48px | Manrope 800 | Hero (mobile) |
| `text-4xl` | 36px | Manrope 800 | Page headings |
| `text-3xl` | 30px | Manrope 700 | Section headings |
| `text-2xl` | 24px | Manrope 700 | Card headings, stats |
| `text-xl` | 20px | Inter 600 | Section intros |
| `text-lg` | 18px | Inter 400 | Lead paragraphs |
| `text-base` | 16px | Inter 400 | Body text |
| `text-sm` | 14px | Inter 500 | Secondary text, nav |
| `text-xs` | 12px | Inter 500 | Captions, labels |

### Wordmark Specification
- Font: Manrope ExtraBold (800)
- Case: ALL CAPS
- Tracking: 0.06em
- "STREET" in primary text color; "SOLE" in #10B981

---

## 5. Spacing System

**Base unit:** 4px

| Token | Value | Common Usage |
|-------|-------|-------------|
| `space-1` | 4px | Micro gaps |
| `space-2` | 8px | Inline element gaps |
| `space-3` | 12px | Tight component padding |
| `space-4` | 16px | Standard padding |
| `space-6` | 24px | Section sub-gaps |
| `space-8` | 32px | Component spacing |
| `space-12` | 48px | Section spacing (mobile) |
| `space-16` | 64px | Section spacing |
| `space-20` | 80px | Large section spacing |
| `space-24` | 96px | Hero spacing |

---

## 6. Responsive Breakpoints

| Name | Width | Grid | Container |
|------|-------|------|-----------|
| Mobile | 320–639px | 1 col | 100% |
| Tablet | 640–1023px | 2 col | 640px |
| Desktop | 1024–1279px | 12 col | 1024px |
| Large | 1280px+ | 12 col | 1200px |

---

## 7. Shadows & Effects

### Light UI
| Token | Value |
|-------|-------|
| `shadow-sm` | `0 1px 3px rgba(0,0,0,0.06)` |
| `shadow-md` | `0 4px 6px -1px rgba(0,0,0,0.07)` |
| `shadow-lg` | `0 10px 15px -3px rgba(0,0,0,0.08)` |

### Dark UI — Green Glow
| Token | Value | Usage |
|-------|-------|-------|
| `glow-sm` | `0 0 10px rgba(16,185,129,0.15)` | Explored street segments |
| `glow-md` | `0 0 20px rgba(16,185,129,0.2)` | Active position |
| `glow-lg` | `0 0 40px rgba(16,185,129,0.25)` | Just-unlocked streets |
| `glow-xl` | `0 0 60px rgba(16,185,129,0.3)` | Achievement moments |

---

## 8. Motion & Animation

| Duration | Value | Usage |
|----------|-------|-------|
| Instant | 75ms | Hover states, toggles |
| Fast | 150ms | Buttons, color changes |
| Normal | 300ms | Card transitions, reveals |
| Slow | 500ms | Page transitions |
| Slower | 700ms | Street unlock animations |

**Easing:** `cubic-bezier(0.4, 0, 0.2, 1)` for standard, `cubic-bezier(0.34, 1.56, 0.64, 1)` for spring bounce (achievements).

**Reduced motion:** Always respect `prefers-reduced-motion: reduce`.

---

## 9. Iconography Style

- Stroke-based, 2–2.5px weight
- Rounded line caps and joins
- 24px design grid with 2px padding
- Monochrome (#9CA3AF default, #10B981 active)
- Consistent with the geometric, map-data visual language

---

## 10. Photography & Imagery Direction

- Aerial/top-down city views showing street grids
- Urban textures: asphalt, crosswalks, city patterns
- People walking in cities — candid, not posed
- Map overlays on real city photography
- Color grading: desaturated backgrounds, green highlights
- **Avoid:** Tourist imagery, selfie culture, stock photo smiles

---

## 11. Voice & Tone Quick Reference

| Context | Tone | Example |
|---------|------|---------|
| Headlines | Confident, direct | "Explore Every Street" |
| Descriptions | Clear, motivating | "Watch your city light up as you walk." |
| Stats | Data-forward | "347 streets. 12.2% explored." |
| Achievement | Celebratory but cool | "New neighbourhood unlocked." |
| Error | Helpful, brief | "Couldn't load your map. Try again." |

---

## 12. File Inventory

```
streetsole-brand/
├── brand-guide/
│   ├── STREETSOLE-BRAND-GUIDE.md    ← This file
│   └── design-tokens.css            ← CSS custom properties
├── logo/
│   ├── streetsole-symbol.svg        ← Primary symbol mark
│   ├── streetsole-wordmark.svg      ← Wordmark only
│   ├── streetsole-full-lockup.svg   ← Symbol + wordmark
│   ├── streetsole-symbol-mono-light.svg  ← White on transparent
│   └── streetsole-symbol-mono-dark.svg   ← Dark on transparent
├── icon/
│   ├── streetsole-app-icon-1024.svg      ← Master app icon (dark)
│   └── streetsole-app-icon-light-bg.svg  ← Light background variant
└── website/
    ├── index.html                   ← Landing page
    └── css/
        └── style.css                ← Landing page styles
```

---

*Streetsole Brand Guide v1.0*
*Designed for urban explorers who own their city, street by street.*

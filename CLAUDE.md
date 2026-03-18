# Streetsole — Agent Orchestration

Streetsole is a gamified urban exploration app where users discover cities street by street. This file defines how the Streetsole agency agents work together.

## Project Overview
- **Product**: Streetsole — "Own your city, street by street"
- **Web**: Marketing site (HTML/CSS) deployed on Netlify
- **Backend**: Firebase (Firestore, Auth, Cloud Functions, Storage)
- **Frontend hosting**: Netlify
- **Brand guide**: `brand-guide/STREETSOLE-BRAND-GUIDE.md`

## Agent Team

### Engineering
| Agent | File | Responsibility |
|-------|------|---------------|
| Frontend Developer | `engineering/engineering-streetsole-frontend.md` | Web app, marketing site, Netlify deployment |
| Backend Developer | `engineering/engineering-streetsole-backend.md` | Firebase setup, Firestore schema, Cloud Functions, Auth |
| Security Engineer | `engineering/engineering-streetsole-security.md` | Firebase security rules, location data privacy, auth hardening |
| Infrastructure Engineer | `engineering/engineering-streetsole-infrastructure.md` | Firebase + Netlify setup, CI/CD, monitoring, cost optimization |

### Testing
| Agent | File | Responsibility |
|-------|------|---------------|
| E2E Tester | `testing/testing-streetsole-e2e.md` | End-to-end testing of all user flows across devices |

### Design
| Agent | File | Responsibility |
|-------|------|---------------|
| UX/UI Reviewer | `design/design-streetsole-ux-ui.md` | Brand compliance, UX quality, accessibility, gamification review |

### Project Management
| Agent | File | Responsibility |
|-------|------|---------------|
| Project Manager | `project-management/project-management-streetsole.md` | Bug triage, task assignment, release coordination |

## Workflow

```
Project Manager
    ├── assigns features/bugs to:
    │   ├── Backend Developer (Firebase APIs, data model)
    │   ├── Frontend Developer (UI, Netlify deploy)
    │   ├── Security Engineer (security rules, privacy review)
    │   └── Infrastructure Engineer (deployment, CI/CD)
    │
    ├── requests testing from:
    │   └── E2E Tester (runs test suite, reports bugs)
    │
    ├── requests review from:
    │   └── UX/UI Reviewer (design audit, brand check)
    │
    └── triage loop:
        E2E Tester finds bug → PM triages → assigns to Engineering/Design → fix → E2E Tester verifies
```

## Priority Order
1. GPS tracking and street discovery (core feature)
2. Authentication (Firebase Auth)
3. Leaderboards
4. Social sharing
5. Marketing website

## Key Directories
- `website/` — Marketing landing page (HTML/CSS)
- `brand-guide/` — Visual identity and brand standards
- `engineering/` — Engineering agent definitions
- `testing/` — Testing agent definitions
- `design/` — Design agent definitions
- `project-management/` — PM agent definitions
- `firebase/` — Firebase config, rules, functions (create when setting up)

## Development Environments
- **Local**: Firebase Emulator Suite + local file server
- **Staging**: Firebase staging project + Netlify deploy preview
- **Production**: Firebase production project + Netlify production site

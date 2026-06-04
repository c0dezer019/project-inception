# UI/UX Design Handoff Prompt Template

Reference for inception:design when generating the UI/UX handoff prompt.
Populate all [bracketed] fields from the design context object before presenting.

---

## How to Use This Template

1. Fill in all [bracketed] placeholders from `discovery` and `design` context
2. Remove any sections marked "(if applicable)" that don't apply
3. Present the completed prompt in a copyable code block
4. Tell the user they can paste this into Canva, Figma AI, a new Claude
   conversation, or any AI design tool

---

## Handoff Prompt Template

```
# UI/UX Design Brief — [project_name]

## Project Overview
[project_name] is a [project_type] application for [audience].
It solves: [problem_statement]

## Core Features to Design For
[bulleted list of core_features + custom_features]

## User Types
[list of user roles/types identified during discovery]
Primary user: [primary audience description]

## Tech Stack Context
Frontend: [frontend framework from stack recommendation]
Rendering: [target_platform — SSR, SPA, hybrid, etc.]
Design should account for: [any stack-specific constraints — e.g., Next.js
App Router server components, mobile-first for React Native, etc.]

## Scale & Performance Context
Scale: [scale]
Caching: [caching_needs — relevant for perceived performance and skeleton states]

## Design Priorities
The team's top priorities are: [priorities, comma-separated]
[If Performance is a priority]: Designs should minimize layout shift and
support skeleton/loading states for all data-fetching views.
[If Developer Experience is a priority]: Prefer clean, composable component
structures over highly custom one-off designs.

## Pages / Screens Needed
Based on the feature set, the following pages/screens are required:

[Generate this list from core_features + custom_features. Examples below:]
- Landing / Marketing page (if applicable)
- Sign Up / Log In / Password Reset (if Auth)
- Dashboard / Home (primary post-login view)
- [Feature-specific screens — one per major feature]
- Account / Settings
- Admin panel (if Admin Dashboard feature)
- 404 / Error states

## Interaction & UX Requirements
- [ ] Responsive design required (mobile + desktop)
- [ ] Dark mode support: [yes/no — prompt user if unclear]
- [ ] Accessibility: WCAG 2.1 AA compliance recommended
- [ ] Loading states: skeleton screens for all async data views
- [ ] Empty states: designs for zero-data scenarios on all list views
- [ ] Error states: inline form validation, toast/alert patterns

## Brand & Visual Direction (if applicable)
[If user has provided brand info — include here. Otherwise:]
No brand guidelines established yet. Please propose a visual direction that
fits a [professional/playful/minimal/bold — adapt to project context] aesthetic
appropriate for [audience].

Suggested starting point:
- Typography: [clean sans-serif for SaaS / geometric for tech / friendly rounded
  for consumer — adapt based on project type and audience]
- Color palette: [prompt for direction or suggest neutral + accent]
- Component style: [flat/material/glassmorphism/neumorphic — keep it simple,
  recommend flat/clean for most projects]

## Out of Scope for This Design Phase
- Backend architecture (handled separately)
- Database schema (handled separately)
- Authentication flow implementation details
- Detailed animation/motion design (unless explicitly requested)

## Deliverables Requested
- [ ] High-fidelity mockups for all pages listed above
- [ ] Component library / design system basics (colors, typography, spacing, buttons)
- [ ] Mobile and desktop variants for key screens
- [ ] Design tokens exportable to CSS variables or Tailwind config

## Notes for the Designer
[Any additional context from the discovery session worth surfacing — team
constraints, existing brand elements, competitor references, etc.]
```

---

## Customization Notes

When populating the template, apply these judgments:

**Project type → screen list:**
- Web/SaaS: landing page, auth, dashboard, feature screens, settings, admin
- Mobile: onboarding, auth, home/feed, feature screens, profile, settings
- API-only: skip — no UI to design (note this to the user)
- Desktop: app shell, main workspace, settings/preferences, onboarding

**Scale → design complexity:**
- Personal/Hobby: minimal screens, basic components
- Startup/MVP: focus on core flow only, defer edge cases
- Production+: full screen coverage including empty/error states
- Enterprise: include admin, audit views, RBAC-aware UI patterns

**Audience → visual direction:**
- Developers/technical users: minimal, functional, dark mode likely preferred
- Business users: professional, clean, data-dense is acceptable
- Consumers: friendly, accessible, mobile-first
- Enterprise: conservative, trustworthy, dense information architecture

**Caching → perceived performance:**
- Aggressive or Offline-first: call out skeleton screens and optimistic UI explicitly
- Basic or None: standard loading states are sufficient

# Authenticated Visual QA

**Status: BLOCKED / NOT VERIFIED.**

The patched target cannot compile:

```
Module not found: Can't resolve './components/theme/ThemeContext'
```

Because the production build fails and the test suite cannot load `PageTransition`, no patched application was started. No authenticated browser session, route navigation, screenshot, or theme check was performed.

Do not interpret the existing 64/64 public-page matrix as coverage for this patched target. That matrix covered public pages and `/signin` redirects, not authenticated screens, and it was run against a different tree.

---

## 1. Routes requiring verification

All are **NOT VERIFIED** for the patched target:

### Candidate
`/feed`, `/in/jobs`, `/in/jobs/:slug`, `/in/jobs/alerts`, `/in/network`, `/notifications`, `/in/interviews`, `/in/interviews/:id`, `/in/interview/:id`, `/in/profile`, `/in/analytics`, `/in/resume/builder`, `/in/practice`, `/in/practice/:sessionId`, `/in/ai-agents`, `/in/shareable-profiles`, `/in/coaching` and its six subroutes, `/settings`, `/billing`, `/search`, `/in/:slug`.

### Organization
`/org/jobs`, `/org/jobs/:id`, `/org/candidates`, `/org/interviews`, `/org/pipeline`, `/org/hire`, `/org/analytics`, `/org/insights`, `/org/reports`, `/org/integrations`, `/org/team`, `/org/billing`, `/org/browse`, `/org/user/:id`, `/org/candidate-analysis`, `/org/ai-agents`, `/org/:slug`.

### Interview workflows
`/org/interviews`, `/in/interviews`, `/in/interviews/:id`, `/in/interview/:id`, `/in/interviews/analysis`, `/in/interviews/:id/analysis`.

### Page manager
`/page`, `/page/profile`, `/page/visibility`, `/page/settings`, `/page/posts`, `/page/posts/:id`, `/page/candidates`, `/page/interviews`, `/page/pipeline`, `/page/analytics`, `/page/agents`, `/page/team`, `/page/billing`, `/page/create`.

### Authorized admin (separate app; out of this patch)
Admin `/`, `/users`, `/organizations`, `/plans`, `/ai-providers`, `/outbox`, `/content`.

---

## 2. Required matrix once the package is fixed

For each route:

- Real candidate, organization, page-manager, and authorized superadmin sessions
- Desktop 1440×900, tablet 768×1024, mobile 390×844
- Light and dark (`recruai-theme`)
- Loaded, empty, loading/skeleton, and API error states
- Full-page screenshots; console/network errors; document overflow; heading count; contrast samples
- Primary interaction, tabs/modals, keyboard focus order

No fake authentication or mocked API responses may be used to mark these verified.

---

## 3. Blocker

The build/test failure must be resolved first. Specifically, the package must include the missing HEAD-only modules and the target files that currently import deleted modules. Until then, visual QA cannot begin.

**Visual QA verdict: BLOCKED.**

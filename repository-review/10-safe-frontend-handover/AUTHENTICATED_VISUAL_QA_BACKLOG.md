# Authenticated Visual QA Backlog

**Status of every route in this document: NOT VERIFIED.**

The existing 64/64 matrix covers 13 public pages and 3 protected routes that only redirect to `/signin`. It does not render an authenticated screen. The 9-test e2e suite is shallow (overflow/h1 only), uses localStorage-only mock auth, and predates the final edits. No route below has been visually verified with a real signed-in session.

Do not fake authentication or API responses to close this backlog. Use the real backend, seeded demo accounts, and a real browser session.

---

## 1. Required test matrix per route

For each route:

- Roles: candidate, organization admin/recruiter, page manager (where applicable), superadmin (authorized admin only)
- Viewports: desktop 1440×900 and mobile 390×844
- Themes: light and dark (`recruai-theme`)
- States: loaded data, empty state, loading/skeleton, API error/retry
- Evidence: full-page screenshot, console/network errors, document overflow check, primary heading, contrast samples, keyboard focus order
- Interactions: primary action, tab/nav switch, modal/drawer open-close, form validation
- No console errors and no horizontal document scroll

---

## 2. Individual dashboard / candidate

| Route | Why it needs real auth | Checks |
|---|---|---|
| `/feed` | Profile rail, saved/applied data, compose FAB | Loaded/empty feed, saved/applied tabs, mobile rail, FAB overlap |
| `/in/jobs` | Saved/applied master-detail | Tabs, filters, apply/save, job detail, empty/error |
| `/in/jobs/:slug` | Job detail + apply flow | Data, apply modal, auth gate |
| `/in/jobs/alerts` | Alerts CRUD | List, create/edit, empty |
| `/in/network` | Follow graph | Suggestions, follow/unfollow, empty |
| `/notifications` | Inbox states | Unread/read, bulk read, empty |
| `/in/interviews` | Upcoming/history tabs | Both tabs, join actions, empty |
| `/in/interviews/:interviewId` | Interview detail | Status, join, transcript |
| `/in/interview/:interviewId` | Live/room entry | Pre-join, token, device permissions, error |
| `/in/profile` | 12+ profile sections | Edit modals, save, upload, empty sections |
| `/in/analytics` | Personal metrics | KPI numbers, score bars, empty |
| `/in/resume/builder` | Builder workspace | Sections, preview, save, mobile |
| `/in/practice` | Practice dashboard | Sessions, start flow, empty |
| `/in/practice/:sessionId` | Practice room | Questions, scoring, completion |
| `/in/ai-agents` | Agent configuration | Cards, config, empty |
| `/in/shareable-profiles` | Public profile links | List/create/copy, empty |
| `/in/coaching` | CVAI overview | Stats, plan, empty |
| `/in/coaching/skills` | Skill assessment | Add/measure, levels |
| `/in/coaching/quizzes` | Quiz list/attempt | Start/submit/score |
| `/in/coaching/projects` | Projects | Submit/evaluate |
| `/in/coaching/plan` | 12-week plan | Steps, completion |
| `/in/coaching/interviews` | Mock interviews | Start/score |
| `/in/coaching/authoring` | Authoring tools | Permission-gated |
| `/settings` | Account/privacy | Theme, export/delete, password |
| `/billing` | Individual billing | Plan, payment methods, usage |
| `/search` | Global search | Query, result types, empty |
| `/in/:slug` | Public candidate profile | Signed-out + signed-in, share metadata |

---

## 3. Organization dashboard, hiring, and candidates

| Route | Checks |
|---|---|
| `/org/jobs` | Job list, create/edit, status, empty |
| `/org/jobs/:id` | Detail, applicants, edit, delete |
| `/org/candidates` | Candidate table, filters, actions |
| `/org/hire` | Search/matching, results, empty/error |
| `/org/pipeline` | Kanban columns, stage move, horizontal scroll, live update |
| `/org/interviews` | Scheduling list, create, status |
| `/org/analytics` | KPI/chart grid, empty |
| `/org/insights` | Insight cards, data |
| `/org/reports` | Report filters, export, empty |
| `/org/integrations` | Integration cards, connect state |
| `/org/team` | Members, invitations, roles |
| `/org/billing` | Plan, usage endpoint (currently missing), payment methods |
| `/org/browse` | Candidate org browsing |
| `/org/user/:userId` | Candidate profile from org view |
| `/org/candidate-analysis` | Analysis workspace, permissions |
| `/org/ai-agents` | Agent config/test |
| `/org/:slug` | Public organization page |

---

## 4. Interview scheduling / waiting room / room / analysis

| Route | Checks |
|---|---|
| `/org/interviews` | Schedule, interviewer assignment, status |
| `/in/interviews` | Candidate upcoming/history |
| `/in/interviews/:interviewId` | Detail, join button, pre-join |
| `/in/interview/:interviewId` | Waiting room, device checks, token, LiveKit permission errors |
| `/in/interview/:interviewId/analysis` | Post-interview analysis |
| `/in/interviews/analysis` | Analysis index |
| `/in/interviews/:interviewId/analysis` | Per-interview analysis |
| `/in/interviews/:interviewId` (manual mode) | Interviewer-response endpoint + bearer token (both currently broken) |

---

## 5. Page manager

| Route | Checks |
|---|---|
| `/page` | Overview, permissions, empty |
| `/page/profile` | Page profile form, upload, save |
| `/page/visibility` | Public/private toggle, save |
| `/page/settings` | Page settings, team permissions |
| `/page/posts` | Posts list, create/edit |
| `/page/posts/:id` | Post detail |
| `/page/candidates` | Candidates |
| `/page/interviews` | Interviews |
| `/page/pipeline` | Pipeline kanban, horizontal scroll |
| `/page/analytics` | Analytics |
| `/page/agents` | AI agents |
| `/page/team` | Team management |
| `/page/billing` | Billing |
| `/page/create` | Create-page flow |

---

## 6. Authorized admin pages (separate app; excluded from this handover)

| Route | Checks |
|---|---|
| Admin `/` | Overview |
| `/users` | User table, plans, manage |
| `/organizations` | Org table |
| `/plans` | Plan catalog |
| `/ai-providers` | Provider config |
| `/outbox` | Retry/outbox |
| `/content` | Content banks |

These require a real superadmin session. The working-tree admin CSS is excluded from the handover; the branch HEAD already contains admin changes.

---

## 7. Auth flows with a live backend

- `/signin` (success, wrong password, unverified → OTP)
- `/register` (success, duplicate, OTP)
- `/forgot-password` (send code, no-account parity)
- `/reset-password` (valid/expired code)
- `/accept-invite` (valid/expired invitation, role grant)
- `/signin` → protected route → refresh-on-reload → logout

The 64/64 matrix checks only layout/contrast on these pages, not the flows.

---

## 8. Exit criteria

The backlog closes only when:

1. Each route has a real authenticated session and real API data.
2. Desktop/mobile and light/dark captures exist.
3. Loaded, empty, loading, and error states are reviewed where they exist.
4. No horizontal overflow, no console errors, and no broken primary actions.
5. The backend dependencies in `BACKEND_DEPENDENCY_LIST.md` are available or the affected screens are explicitly marked out of scope.

Until then, **do not claim authenticated visual verification**.

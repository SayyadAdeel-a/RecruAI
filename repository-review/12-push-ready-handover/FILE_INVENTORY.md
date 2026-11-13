# Frontend Handover File Inventory

**Source:** `adeeldev` @ `47f2cd0` + current intended frontend working-tree changes  
**Target baseline:** `underdev` @ `72480fb`  
**Patch operations:** 349 — 187 additions, 138 modifications, 24 deletions  
**Patch size:** 3,210,106 bytes  
**File-transfer package:** 325 changed/added files + 4 metadata files

The complete per-file list with SHA-256 values and previous hashes is in `PATCH_MANIFEST.json`. `frontend-handover.patch` contains every operation and applies cleanly to the exact baseline.

---

## 1. Additions (187)

| Group | Files | Notes |
|---|---:|---|
| `frontend/src/components/**` | 29 | Includes `theme/ThemeContext`, `ui/Avatar`, `profile/ProfileSection`, `layout/RightRail`, `layout/SignedInAccountBar`, `layout/useActiveRail`, `layout/navStyles`, `jobs/JobCard`, all missing `motion/*`, and `feed/IndividualFeedContainer` |
| `frontend/src/lib/**` | 2 | `cn.js`, `format.js` |
| `frontend/src/stageshow/**` | 155 | Complete source kit (CSS + TSX/TS/SVG); CSS is required by `index.css`; the rest is the design kit source |
| `SYSTEM.md` | 1 | Frontend design-system contract |

The previously missing module families now exist in the patch:

`ThemeContext`, `lib/cn`, `ui/Avatar`, `profile/ProfileSection`, `layout/RightRail`, `layout/SignedInAccountBar`, `layout/useActiveRail`, `motion/AnimatedNumber`, `motion/InteractiveCard`, `motion/MotionIcon`, `motion/PageTransition`, `motion/Stagger`, `feed/IndividualFeedContainer`.

---

## 2. Modifications (138)

| Group | Files |
|---|---:|
| `frontend/src/pages/**` | 95 |
| `frontend/src/components/**` | 31 |
| `frontend/src/utils/**` | 3 |
| `frontend/src/__tests__/PageProfile.test.js` | 1 |
| `frontend/src/App.js`, `index.js`, `index.css`, `setupTests.js` | 4 |
| `frontend/package.json`, `package-lock.json` | 2 |
| `frontend/public/index.html` | 1 |
| `frontend/tailwind.config.js` | 1 |

Key importers are updated: `DashboardSwitcher` uses `IndividualFeedContainer`; `AboutSection` and `LanguagesSection` no longer depend on the deleted `ui/Card`; `ProfileSidebar` is removed with the other dead profile files.

### Test-only compatibility change

`frontend/src/__tests__/PageProfile.test.js` seeds a structurally valid in-memory access token with a future expiry. This lets the existing test use its mocked `/api/auth/me` response instead of hitting the real `/api/auth/refresh` path. **No application behavior changed.** The suite now passes 42/42 on the target.

---

## 3. Deletions (24)

```text
frontend/public/mentee-logo.svg
frontend/src/App.css
frontend/src/components/RoleProtectedRoute.js
frontend/src/components/TeamMemberProfile.jsx
frontend/src/components/layout/DashboardFooter.jsx
frontend/src/components/layout/DashboardLayout.jsx
frontend/src/components/layout/Footer.jsx
frontend/src/components/layout/Header.jsx
frontend/src/components/layout/IndividualNavbar.jsx
frontend/src/components/layout/OrganizationNavbar.jsx
frontend/src/components/ui/Card.jsx
frontend/src/components/ui/Chip.jsx
frontend/src/components/ui/RecommendationCard.jsx
frontend/src/components/ui/StatCard.jsx
frontend/src/logo.svg
frontend/src/pages/Dashboard.jsx
frontend/src/pages/PublicProfile.jsx
frontend/src/pages/dashboards/IndividualDashboard.jsx
frontend/src/pages/individual/AppliedJobs.jsx
frontend/src/pages/individual/InterviewHistory.jsx
frontend/src/pages/individual/SavedJobs.jsx
frontend/src/pages/individual/UpcomingInterviews.jsx
frontend/src/pages/individual/components/ProfileSidebar.jsx
frontend/src/utils/performance.js
```

All deletion paths are intentional. The patch deletes them; the ZIP includes `DELETIONS.txt`.

---

## 4. Excluded

- `frontend/src/pages/SystemStatus.jsx` — protected; target version retained unchanged.
- `admin/**`, `backend/**`, `database/**` — outside the frontend handover.
- `frontend/scripts/visual-check-key.cjs`, `visual-sweep.cjs` — demo credentials.
- `frontend/scripts/visual-verify.cjs`, `visual-assert.cjs` — optional verification tooling.
- `frontend/e2e/**`, `frontend/playwright.config.js` — optional e2e tooling.
- `frontend/qa-*.mjs`, `frontend/qa-shots-*/**` — QA tooling/evidence.
- `frontend/public/qa-gallery.html` — dev-only QA gallery.
- `.gitignore`, `skills-lock.json`, `.commandcode/**`, `.posit/**`, `.tabnine/**`, `agent/**`, `skills/**` — tooling/shared config.
- `repository-review/**` — audit reports.

---

## 5. Package contents

`frontend-handover.zip` contains:

```text
frontend/...                 (all added/modified app files, paths preserved)
SYSTEM.md
DELETIONS.txt
PATCH_MANIFEST.json
BASELINE.json
frontend-handover.patch
FILE_INVENTORY.md
BUILD_TEST_LINT_RESULTS.md
IMPORT_RESOLUTION_REPORT.md
PROTECTED_EXCLUSIONS_REPORT.md
BACKEND_DEPENDENCIES.md
BROTHER_MERGE_INSTRUCTIONS.md
FINAL_READINESS.md
```

No `node_modules`, build output, QA evidence, admin, backend, or credential scripts are included.

---

## 6. Reproducibility

- Patch headers: 349; manifest operations: 349; no missing or extra headers.
- `git apply --check` against a clean `underdev` worktree: **PASS**.
- Applied to a third clean worktree, tests/build/lint passed.
- The applied tree differs from the validated patched tree only by line-ending normalization (all normalized-content comparisons are identical).

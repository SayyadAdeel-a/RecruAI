# Frontend Patch Manifest

**Package root:** `repository-review/10-safe-frontend-handover/`  
**Patch tree:** `patch/` (preserves repository paths)  
**Machine manifest:** `patch/PATCH_MANIFEST.json`  
**Baseline:** `BASELINE.json`  
**Contents:** 184 copied files + 3 metadata files + `DELETIONS.txt`  
**Size:** ~2.78 MB

This is a file-level patch, not a Git commit, branch, or remote transfer.

---

## 1. Patch operations

| Operation | Count | How it is represented |
|---|---:|---|
| Replace modified frontend file | **142** | `patch/frontend/<same path>` |
| Create required frontend file | **42** | `patch/frontend/...` + `patch/SYSTEM.md` |
| Delete frontend file | **25** | `patch/DELETIONS.txt` |
| **Copied files total** | **184** | `PATCH_MANIFEST.json.apply` |

### Additions (42)

| Group | Count |
|---|---:|
| Shared sidebar module `navStyles.js` | 1 |
| Job card module `JobCard.jsx` | 1 |
| `stageshow` CSS closure | 39 |
| `SYSTEM.md` | 1 |

### Modified files by area (142)

| Area | Count |
|---|---:|
| `frontend/src/pages/individual` | 40 |
| `frontend/src/pages/organization` | 18 |
| `frontend/src/pages` (public/auth/root pages) | 20 |
| `frontend/src/pages/cvai` | 8 |
| `frontend/src/pages/page` | 5 |
| `frontend/src/pages/dashboards` | 1 |
| `frontend/src/components` (all subdirectories + root components) | 43 |
| `frontend/src` root (`App.js`, `index.css`, `setupTests.js`) | 3 |
| `frontend/src/utils` | 1 |
| `frontend/public/index.html` | 1 |
| `frontend/package.json`, `frontend/package-lock.json` | 2 |

The exact per-file list, byte size, and SHA-256 are in `PATCH_MANIFEST.json`.

---

## 2. Required additions

See `REQUIRED_UNTRACKED_FILES.md` for the dependency evidence.

- `frontend/src/components/layout/navStyles.js` — imported by all three shells.
- `frontend/src/components/jobs/JobCard.jsx` — re-exported by `JobBits.jsx`; circular pair must travel together.
- 39 `stageshow` CSS files — closure of `index.css` → `stageshow.css` → 38 imports.
- `SYSTEM.md` — frontend design-system contract.

`stageshow` TSX/TS components, SVG brands, and 8 unreferenced CSS files are intentionally omitted. They are not in the application import graph.

---

## 3. Deletions (25)

Apply only after verifying the target branch still has these files and still does not reference them:

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
frontend/src/components/ui/Segmented.jsx
frontend/src/components/ui/StatCard.jsx
frontend/src/components/ui/Tabs.jsx
frontend/src/logo.svg
frontend/src/pages/Dashboard.jsx
frontend/src/pages/PublicProfile.jsx
frontend/src/pages/dashboards/IndividualDashboard.jsx
frontend/src/pages/individual/AppliedJobs.jsx
frontend/src/pages/individual/InterviewHistory.jsx
frontend/src/pages/individual/SavedJobs.jsx
frontend/src/pages/individual/UpcomingInterviews.jsx
frontend/src/utils/performance.js
```

The 2 root 0-byte junk deletions (`$null`, `(a.getAttribute('aria-label')`) are **not** part of the frontend patch; remove or ignore them separately.

---

## 4. Exclusions

| Excluded | Reason |
|---|---|
| `frontend/src/pages/SystemStatus.jsx` | Protected; modified in HEAD and working tree |
| `admin/src/index.css` | Protected admin/Superadmin styling |
| 9 admin HEAD files / 5 backend HEAD files | Not frontend; branch-level only |
| `frontend/scripts/visual-check-key.cjs`, `visual-sweep.cjs` | Demo credentials |
| `frontend/scripts/visual-verify.cjs`, `visual-assert.cjs` | Optional verification tooling |
| `frontend/e2e/stageshow-craft.spec.js` | Optional e2e; not required to build |
| `.gitignore`, `skills-lock.json`, `.commandcode/taste/...` | Tooling/shared config |
| `.posit/`, `.tabnine/`, `agent/`, `skills/` | Duplicate installed skill trees |
| `frontend/qa-shots-*`, `qa-evidence/`, `visual-audit/`, `repository-review/` | Evidence/artifacts |

---

## 5. Verification performed

| Check | Result |
|---|---|
| Copied files exist and are readable | **PASS** — 184/184 |
| Relative JS/JSX imports resolve inside patch or to a file tracked at `HEAD` | **PASS** — 0 missing |
| CSS `@import` references resolve inside patch | **PASS** — 0 missing |
| `package.json` dependencies vs lockfile root | **PASS** — 0 missing, 0 extra |
| Protected `SystemStatus.jsx` in patch | **PASS** — absent |
| Admin CSS in patch | **PASS** — absent |
| Credential scripts in patch | **PASS** — absent |
| Credential-shaped strings in patch | **PASS** — none (only form field names) |
| `stageshow` CSS closure | **PASS** — 39/39 |
| Protected hashes unchanged by packaging | **PASS** — match `BASELINE.json` |
| Clean-target `npm ci` + `npm run build` | **NOT RUN** — static import/lock verification only |
| Authenticated browser QA | **NOT RUN** — see backlog |

---

## 6. Status

**READY FOR PATCH TRANSFER (static).**

The package is self-contained for the current frontend import graph, excludes all protected/backend/admin/credential content, and preserves paths. It is **not** declared merge-ready until a clean-target build/test and authenticated visual QA are completed by the brother.

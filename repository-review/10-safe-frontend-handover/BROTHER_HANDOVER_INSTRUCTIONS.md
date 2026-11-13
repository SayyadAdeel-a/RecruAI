# Brother Handover Instructions

This package is a **file-level frontend patch**, not a branch, commit, or merge.

**Do not merge `adeeldev` into your branch.** Its `HEAD` (`47f2cd0`) contains 5 backend files and 9 admin files plus 4,087 files of unrelated history. The isolated patch is under `patch/`.

---

## 1. Review first

Read, in order:

1. `FINAL_HANDOVER_READINESS.md`
2. `SAFE_HANDOVER_INVENTORY.md`
3. `FRONTEND_PATCH_MANIFEST.md`
4. `PROTECTED_FILE_EXCLUSIONS.md`
5. `BACKEND_DEPENDENCY_LIST.md`
6. `AUTHENTICATED_VISUAL_QA_BACKLOG.md`

Do not apply anything until you are satisfied with the exclusions and the protected-file decision.

---

## 2. Prepare your branch

1. Work on a clean branch of your own repository.
2. Commit or stash your local work so the patch can be reviewed separately.
3. Confirm `frontend/package.json` and `frontend/package-lock.json` do not contain local changes you need to keep. The patch replaces both.
4. Do **not** copy this package into the app tree; it lives under `repository-review/` for review only.

---

## 3. Apply the patch

From the package directory, with your repository root as the destination:

**PowerShell**

```powershell
# 1. Copy modified and new frontend files, preserving paths.
Copy-Item -Recurse -Force .\patch\frontend\* <your-repo>\frontend\
# 2. Copy the design-system document.
Copy-Item -Force .\patch\SYSTEM.md <your-repo>\SYSTEM.md
# 3. Delete the files listed in patch\DELETIONS.txt, one by one, after verifying them.
```

**Unix/macOS**

```bash
cp -R patch/frontend/. /path/to/your-repo/frontend/
cp patch/SYSTEM.md /path/to/your-repo/SYSTEM.md
# Delete each path in patch/DELETIONS.txt after verifying it.
```

The patch includes:

- 142 modified frontend files
- `frontend/src/components/layout/navStyles.js`
- `frontend/src/components/jobs/JobCard.jsx`
- 39 `stageshow` CSS files
- `SYSTEM.md`

It does **not** include:

- `frontend/src/pages/SystemStatus.jsx`
- `admin/src/index.css`
- any backend or migration file
- demo-credential scripts
- `.gitignore`, `skills-lock.json`, tooling directories
- QA screenshots or audit evidence

---

## 4. Deletions

`patch/DELETIONS.txt` lists 25 frontend files. Before deleting:

1. Confirm each path exists and is unchanged from upstream.
2. Confirm no import in your branch references it.
3. Delete only the listed path.

The two root junk files (`$null`, `(a.getAttribute('aria-label')`) are not frontend and are not in the list. Handle them separately or leave them.

---

## 5. Build and tests

Use Node 22 / npm 10 (the repository CI expectation):

```bash
cd frontend
npm ci
npm run build
npm run test:ci
```

Expected:

- `npm ci`: clean install consistent with `package-lock.json`.
- `npm run build`: compiled successfully. If it fails, check that `navStyles.js`, `JobCard.jsx`, and the 39 `stageshow` CSS files were copied.
- `npm run test:ci`: **40 passed, 2 failed** (`PageProfile` auth-refresh mock). This is known and pre-existing; do not report 42/42. See `../09-final-independent-audit/TEST_FAILURE_ANALYSIS.md`.

Optional: `npm run e2e` — the untracked spec is not in this patch, so only use it if you have added it yourself.

---

## 6. Protected files and shared config

- **SystemStatus:** excluded. Leave your branch’s version untouched. If you want the working-tree contrast changes, review them separately; they are present in `HEAD` and in the dirty tree.
- **Admin:** excluded. The branch `HEAD` already has 9 admin changes; do not copy them from this patch.
- **Backend:** excluded. Apply the separate backend dependency work from `BACKEND_DEPENDENCY_LIST.md`.
- **`.gitignore` / `skills-lock.json` / `.commandcode`:** excluded. Keep your own tooling config.
- **Package files:** included. If your branch has different dependency versions, compare `package.json` and the lockfile manually before overwriting.

---

## 7. Required backend work

Before claiming the affected screens work:

1. Add `GET /api/organizations/<id>/usage`.
2. Add `POST /api/interviews/<id>/interviewer-response`.
3. Emit `application_stage_changed` after a pipeline stage update.
4. Fix `join_org`’s `...utils` import to `..utils`.
5. Replace InterviewRoom’s `localStorage.getItem("token")` with the in-memory auth header/cookie.

These are documented, not implemented. Do not claim Billing usage or manual interview replies work without them.

---

## 8. Authenticated visual QA

Run the backlog in `AUTHENTICATED_VISUAL_QA_BACKLOG.md` with real sessions and real data. At minimum:

- `/feed`, `/in/jobs`, `/in/profile`, `/in/coaching`, `/settings`, `/billing`
- `/org/jobs`, `/org/candidates`, `/org/pipeline`, `/org/interviews`, `/org/team`, `/org/billing`
- `/page` and its subroutes
- Interview detail/waiting room/room/analysis
- Real superadmin pages if the admin app is in scope

Capture full-page desktop/mobile light/dark screenshots. Do not use fake auth or mocked API responses to mark these verified.

---

## 9. What not to do

- Do not merge or cherry-pick the branch.
- Do not copy `admin/`, `backend/`, or `repository-review/` into the app.
- Do not copy the two demo-credential scripts.
- Do not overwrite SystemStatus or admin CSS.
- Do not claim 42/42 tests or full visual coverage.
- Do not commit/push on my behalf; this handover created no commit.

---

## 10. Acceptance checklist

- [ ] Patch applied with paths preserved
- [ ] 25 deletions applied after verification
- [ ] `navStyles.js`, `JobCard.jsx`, 39 `stageshow` CSS files present
- [ ] `SystemStatus.jsx` and admin CSS unchanged by the patch
- [ ] `npm ci` clean
- [ ] `npm run build` green
- [ ] `npm run test:ci` result recorded as 40/42 with known failures
- [ ] Backend dependencies tracked separately
- [ ] Authenticated visual QA backlog executed or explicitly deferred
- [ ] No credential scripts, tooling duplicates, or evidence directories committed

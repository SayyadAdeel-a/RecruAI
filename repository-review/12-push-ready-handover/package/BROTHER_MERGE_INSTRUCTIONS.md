# Brother Merge Instructions

This is a **frontend-only file patch** against:

```
underdev @ 72480fbc2acf0b9d7ef696140f6320a5e9c2d60e
```

Do not merge the `adeeldev` branch. Its history contains backend/admin changes and unrelated commits. Apply only this patch or the ZIP contents.

---

## 1. Inspect first

```bash
git rev-parse HEAD
# must be 72480fbc2acf0b9d7ef696140f6320a5e9c2d60e

git status --porcelain
# must be empty. If it is not, stop and review your local work first.
```

From the handover directory:

```bash
git apply --stat frontend-handover.patch
git apply --check -p1 frontend-handover.patch   # dry run; no files changed
```

If `git apply --check` fails, do **not** force it. Compare your target commit and local changes.

---

## 2. Apply

```bash
# from your repository root
git apply -p1 /path/to/frontend-handover.patch
```

The patch includes additions, modifications, and the 24 deletions. It does not require staging.

### ZIP alternative

1. Extract `frontend-handover.zip`.
2. Copy `package/frontend/` over your `frontend/` directory, preserving paths.
3. Copy `package/SYSTEM.md` to the repository root.
4. Delete every path listed in `package/DELETIONS.txt` (after confirming the path exists and is dead).

Do not copy `admin/`, `backend/`, `SystemStatus.jsx`, or any QA/credential script; they are not in the package.

---

## 3. Verify

Use Node 22 / npm 10+ (this validation used Node 26.3.0 / npm 11.16.0):

```bash
cd frontend
npm ci
npm run test:ci
npm run build
npx eslint src --ext .js,.jsx
```

Expected:

- `npm ci`: exit 0
- `npm run test:ci`: **42/42 passed**
- `npm run build`: success; current validation bundle `main.bf508d20.js`, 117.8 kB gzip
- lint: exit 0

Start the app and load `/` and `/signin`; no console errors.

---

## 4. What changed in tests

`frontend/src/__tests__/PageProfile.test.js` seeds a structurally valid in-memory access token for the existing PageProfile test. This avoids the real refresh path and lets the test use its mocked `/api/auth/me` response. No application behavior was changed. Review this one test diff explicitly.

---

## 5. Protected/backend boundaries

- `frontend/src/pages/SystemStatus.jsx`: not in the patch; your target version remains.
- `admin/**`: not in the patch.
- `backend/**` and database/migrations: not in the patch.
- Demo-credential scripts: not in the patch.
- The five backend dependencies are documented in `BACKEND_DEPENDENCIES.md`; apply them separately.

---

## 6. Apply order

1. Confirm target commit and clean worktree.
2. `git apply --check`.
3. `git apply`.
4. `npm ci`.
5. Tests, build, lint.
6. Review the test-only change and the deleted-file list.
7. Commit/push only after your own review.

No commit, push, merge, or rebase was performed by the handover process.

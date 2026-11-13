# Protected File Exclusions

Both protected files are **modified in HEAD and further modified in the working tree**. Neither is in the handover package. Neither was edited, restored, or reverted during this mission.

---

## 1. `frontend/src/pages/SystemStatus.jsx`

| Fact | Value |
|---|---|
| HEAD vs `underdev` | **195 lines changed** (`135 insertions / 85 deletions` across both protected files; SystemStatus is the larger part) |
| Working tree vs HEAD | **32 lines changed** (18 insertions / 18 deletions) |
| Current SHA-256 | `557b9ff4570d2b6d73bdcaa6c50d6599987e2b649904f9951052fe61a61fbdfc` |
| In handover | **No** |
| Modified during this mission | **No** — hash matches the baseline captured before packaging |

Working-tree diff (all presentational):

- Default status badge text: `dark:text-gray-300` → `dark:text-gray-600`
- Uptime and service uptime: `text-green-600` → `text-brand-text`
- “Try Again”, “Report Issue”, “Submit Issue”, “Subscribe” hover: `hover:bg-[#009b3f]` → `hover:bg-brand/90`
- Pagination muted text: `dark:text-gray-400` → `dark:text-gray-500`
- Placeholders: `placeholder:text-gray-400` → `placeholder:text-gray-500`

No data fetching, route, auth, or status calculation was changed. The origin is the theme/contrast pass (HEAD commit `47f2cd0` plus the later working-tree accessibility sweep). It remains excluded because the handover explicitly treats SystemStatus as protected.

---

## 2. `admin/src/index.css`

| Fact | Value |
|---|---|
| HEAD vs `underdev` | Part of the **9 admin files** changed in `HEAD`; admin CSS alone shows **25 lines changed** vs `underdev` |
| Working tree vs HEAD | **4 lines changed** — accent and accent-soft lightness 42% → 38% |
| Current SHA-256 | `d9d4fc0baa5c99abf97a0a6265a7cf7abdf0d0cec340b11ae1d9ae0d47535665` |
| In handover | **No** |
| Modified during this mission | **No** — hash matches baseline |

This is a contrast adjustment for white-on-orange admin buttons. It affects Superadmin presentation. The whole admin area is excluded from the frontend handover; functional admin files are present in `HEAD` but are not part of the patch.

---

## 3. Backend and database

| Area | Working tree | HEAD vs `underdev` | Handover |
|---|---|---|---|
| Backend | **No changes** (`git diff --stat -- backend` empty) | **5 files** already changed in `HEAD` | **Excluded** |
| Database/migrations | **No changes** | No handover changes | **Excluded** |

Backend files in `HEAD` (not included in the patch): `backend/api/auth/session.py`, `backend/api/org/applications.py`, `backend/app.py`, `backend/config.py`, `backend/utils/kafka_service.py`.

This is why a branch merge is unsafe: the branch’s HEAD contains protected backend/admin work even though the working-tree delta does not.

---

## 4. Other exclusions from the handover

| Path | Reason |
|---|---|
| `frontend/scripts/visual-check-key.cjs` | Contains demo credentials (see credential report) |
| `frontend/scripts/visual-sweep.cjs` | Contains demo credentials |
| `frontend/scripts/visual-verify.cjs` | Optional verification tooling; not needed to build |
| `frontend/scripts/visual-assert.cjs` | Optional verification tooling; not needed to build |
| `frontend/e2e/stageshow-craft.spec.js` | Optional e2e; untracked; not part of the build |
| `.gitignore` | Root/shared tooling config; not frontend runtime |
| `skills-lock.json` | Agent-tooling lockfile |
| `.commandcode/taste/design/taste.md` | Tooling state |
| `.posit/`, `.tabnine/`, `agent/`, `skills/` | Duplicate installed skill trees |
| `frontend/qa-shots-*`, `qa-evidence/`, `visual-audit/` | Generated evidence |
| `repository-review/` | Audit reports; not app code |
| `$null`, `(a.getAttribute('aria-label')` | Deleted 0-byte junk; not frontend |

---

## 5. Mission integrity

- Baseline hashes recorded in `BASELINE.json` were re-computed after the package was built: **both match**.
- No write, restore, checkout, or edit was issued to either protected file.
- No protected file exists inside `patch/`.
- No backend or migration file exists inside `patch/`.

**Status: PASS for mission integrity. WARNING for the working tree** — both protected files remain modified from earlier work and must be explicitly accepted or reverted by the brother outside this handover.

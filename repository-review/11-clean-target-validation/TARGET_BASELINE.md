# Target Baseline

**Validation date:** 2026-11-13  
**Target selected:** `underdev`  
**Target commit:** `72480fbc2acf0b9d7ef696140f6320a5e9c2d60e`  
**Target remote ref:** `upstream/underdev` (same commit)  
**Package baseline:** `adeeldev` @ `47f2cd01c0a15f91ff9bafb18ecfcd26180ddcea` (one commit ahead of target)

> Target identification is based on the handover reports’ direction to apply onto the development branch and on `underdev` being the package’s direct ancestor. If the intended target is `upstream/dev` or another branch, this validation does not apply and the decision should be re-run for that baseline.

---

## 1. Target state

| Item | Value |
|---|---|
| Branch | `underdev` (detached worktree) |
| HEAD | `72480fb` |
| Worktree status | **clean** — 0 modified, 0 staged, 0 deleted, 0 untracked |
| Package baseline branch | `adeeldev` @ `47f2cd0` |
| Commits between target and package baseline | 1 (`47f2cd0`) |
| Existing target work at risk | **None** — the worktree is clean |

The target was checked out at `repository-review/11-clean-target-validation/target-baseline` with `git worktree add --detach ... underdev`. The original working tree was not used or modified.

---

## 2. Package paths vs target paths

| Operation | Count | Target overlap |
|---|---:|---|
| Replace modified file | 142 | All 142 paths exist in target |
| Create new file | 42 | **None** exist in target |
| Delete file | 25 | 23 exist; 2 absent (`ui/Segmented.jsx`, `ui/Tabs.jsx`) |

- All 142 target versions differ from the HEAD versions the package was built from. The target is older; this is expected, but it means every replacement overwrites the target’s older file. Because the target is clean, no uncommitted target work is lost.
- The 42 additions are clean additions: `navStyles.js`, `JobCard.jsx`, 39 `stageshow` CSS files, and `SYSTEM.md`.
- The 2 absent deletion targets are no-ops (the files do not exist at `underdev`).

---

## 3. Target-only imports that the package does not update

The target is not just an older copy of the package baseline; it contains files that reference modules the package deletes, and it lacks modules the package imports. The most important:

| Target file | Problem |
|---|---|
| `frontend/src/pages/DashboardSwitcher.jsx` | Imports `./dashboards/IndividualDashboard`, which the package deletes. The package does not include the newer `DashboardSwitcher`. |
| `frontend/src/pages/individual/components/sections/AboutSection.jsx` | Imports `ui/Card`, which the package deletes. The package’s `Profile.jsx` imports this target file; the package does not include the newer section. |
| `frontend/src/pages/individual/components/sections/LanguagesSection.jsx` | Same `ui/Card` issue; imported by the package’s `Profile.jsx`. |
| `frontend/src/pages/individual/components/ProfileSidebar.jsx` | Imports `ui/Card`; not included in the package. |

The target also lacks HEAD-added modules that the package’s files import: `components/theme/ThemeContext`, `lib/cn`, `ui/Avatar`, `profile/ProfileSection`, `layout/RightRail`, `layout/SignedInAccountBar`, `layout/useActiveRail`, `motion/*`, and others. The actual build error is `./components/theme/ThemeContext`.

These are not target worktree changes; they are the HEAD→`underdev` delta that the package failed to carry.

---

## 4. Baseline conclusion

**Target baseline available and clean.** No existing target work was at risk from uncommitted changes.

However, the package is **not complete for this target**: replacing/deleting paths without including the intervening HEAD-added/modified frontend files produces a broken tree. The isolated application report records the failure.

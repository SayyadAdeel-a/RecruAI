# Path Overlap and Conflicts

**Target:** `underdev` @ `72480fb` (clean)  
**Package:** 142 replacements, 42 additions, 25 deletions

---

## 1. Overlap summary

| Operation | Target exists | Target differs from HEAD | Action |
|---|---:|---:|---|
| Replace (142) | 142 | 142 | Overwrites the older target version with the redesigned file |
| Create (42) | 0 | n/a | Clean additions; no path collision |
| Delete (25) | 23 | 21 | Removes target files; 2 paths absent (no-op) |

The target worktree is clean, so there are **no uncommitted target changes** to lose. The 142 target versions differ from HEAD because `underdev` is one commit behind the package baseline; every replacement is an intentional transfer of HEAD+working-tree content.

However, “no path collision” is not the same as “complete transfer.” The package omitted HEAD-only files that the target still needs.

---

## 2. Build-blocking target references

These target files are **not in the package**, are not deleted, and still import modules the package deletes:

| Target file | Import | Deleted target | In package? |
|---|---|---|---|
| `frontend/src/pages/DashboardSwitcher.jsx` | `./dashboards/IndividualDashboard` | `pages/dashboards/IndividualDashboard.jsx` | **No** |
| `frontend/src/pages/individual/components/sections/AboutSection.jsx` | `components/ui/Card` | `components/ui/Card.jsx` | **No** |
| `frontend/src/pages/individual/components/sections/LanguagesSection.jsx` | `components/ui/Card` | `components/ui/Card.jsx` | **No** |
| `frontend/src/pages/individual/components/ProfileSidebar.jsx` | `components/ui/Card` | `components/ui/Card.jsx` | **No** |

`AboutSection.jsx` and `LanguagesSection.jsx` are imported by the package’s new `Profile.jsx`, so they are in the build graph. `DashboardSwitcher.jsx` is imported by the package’s `App.js`.

`ProfileSidebar.jsx` may be outside the current graph; it is still a stale target file with a broken import if reached.

---

## 3. Missing HEAD-only modules

The package files import these modules, which exist at `HEAD` but **do not exist in the target and are not in the package**:

```text
frontend/src/components/theme/ThemeContext.jsx
frontend/src/lib/cn.js
frontend/src/components/ui/Avatar.jsx
frontend/src/components/profile/ProfileSection.jsx
frontend/src/components/layout/RightRail.jsx
frontend/src/components/layout/SignedInAccountBar.jsx
frontend/src/components/layout/useActiveRail.js
frontend/src/components/motion/AnimatedNumber.jsx
frontend/src/components/motion/InteractiveCard.jsx
frontend/src/components/motion/MotionIcon.jsx
frontend/src/components/motion/PageTransition.jsx
frontend/src/components/motion/Stagger.jsx
frontend/src/components/feed/IndividualFeedContainer.jsx
```

These are tracked at `HEAD` (verified with `git ls-files`) but absent at `underdev`. The package’s manifest only includes working-tree modifications, so they were omitted. The first production build error is:

```
Module not found: Error: Can't resolve './components/theme/ThemeContext'
```

A static resolver found **94 unresolved relative imports** after applying the package.

---

## 4. Deletions requiring human review

- 23 of 25 deletion paths exist at target; 2 (`ui/Segmented.jsx`, `ui/Tabs.jsx`) are absent and are no-ops.
- 21 of the 23 existing files have a target version that differs from HEAD. Deleting them removes the target’s older variant. They are believed dead, but the target content was not the content used to make the deletion decision.
- The two absent files mean the deletion list is target-dependent; the package must be checked against the actual target before deletion.

No deletion was silently skipped or forced beyond the two absent files. `Segmented.jsx`/`Tabs.jsx` were recorded as no-ops.

---

## 5. Conflict classification

| Class | Count | Status |
|---|---:|---|
| Clean additions | 42 | PASS |
| Intended replacements (target clean) | 142 | WARNING — human diff review recommended |
| No-op deletions | 2 | PASS |
| Deletions of existing target files | 23 | WARNING — review target versions |
| Target files with imports to deleted modules | 4 | **FAIL** |
| Missing HEAD-only modules | 13+ | **FAIL** |

**Verdict: FAIL.** The package cannot be applied to this target without producing a broken build. The omission is systemic: the package carries working-tree changes but not the HEAD→`underdev` frontend delta that those changes depend on.

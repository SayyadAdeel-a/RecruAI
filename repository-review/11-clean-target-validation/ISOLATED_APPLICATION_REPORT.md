# Isolated Application Report

**Method:** two disposable Git worktrees at `underdev` (`72480fb`):

- `repository-review/11-clean-target-validation/target-baseline` — unmodified target
- `repository-review/11-clean-target-validation/target-patched` — package applied

The original working tree and the handover package were not modified. No branch was checked out or rewritten; no commit/push/merge/rebase/reset/clean was run.

---

## 1. Application result

| Operation | Result |
|---|---:|
| Files copied from `patch/` | **184** |
| Files deleted in target | **23** |
| Deletion paths absent from target (no-op) | **2** — `ui/Segmented.jsx`, `ui/Tabs.jsx` |
| Target status after package | **166** entries (128 M, 23 D, 15 untracked entries) |
| Backend/admin/SystemStatus changed | **0** |
| Protected hashes compared with baseline | **match** |
| Package credential scripts copied | **0** |

The target was clean before application (0 modified, 0 staged, 0 untracked). No target work was lost by overwriting.

---

## 2. What broke

### Build

```text
$ cd target-patched/frontend
$ npm run build
> react-scripts build
Creating an optimized production build...
Failed to compile.

Module not found: Error: Can't resolve './components/theme/ThemeContext'
in 'target-patched/frontend/src'
```

Exit code: **1**.

### Tests

```text
$ npm run test:ci
PASS authSession.test.js
PASS slugPaths.test.js
PASS searchRanking.test.js
PASS orgPath.test.js
FAIL src/__tests__/PageProfile.test.js
  ● Test suite failed to run
    Cannot find module '../motion/PageTransition' from
    'src/components/page/PageManagerLayout.jsx'
Test Suites: 1 failed, 4 passed, 5 total
Tests:       40 passed, 40 total
```

Exit code: **1**.

Neither failure is the known PageProfile auth-mock issue. Both are missing-module failures caused by the package’s incomplete file set.

---

## 3. Static dependency check after apply

A resolver over every relative JS/JSX import in `target-patched` found:

- **94 unresolved relative imports**
- **13+ missing module families**, including:
  - `components/theme/ThemeContext`
  - `lib/cn`
  - `components/ui/Avatar`
  - `components/profile/ProfileSection`
  - `components/layout/RightRail`
  - `components/layout/SignedInAccountBar`
  - `components/layout/useActiveRail`
  - `components/motion/AnimatedNumber`
  - `components/motion/InteractiveCard`
  - `components/motion/MotionIcon`
  - `components/motion/PageTransition`
  - `components/motion/Stagger`
- Two target files still import deleted modules:
  - `DashboardSwitcher.jsx` → `IndividualDashboard`
  - `AboutSection.jsx` / `LanguagesSection.jsx` → `ui/Card`
  - `ProfileSidebar.jsx` → `ui/Card` (outside current graph)

The package’s own self-containment check passed because it resolved against `HEAD`; the target is `HEAD~1` and lacks those files.

---

## 4. Protected/backend boundary

| Check | Result |
|---|---|
| `frontend/src/pages/SystemStatus.jsx` status in target | clean; hash equals target baseline |
| `admin/src/index.css` status in target | clean; hash equals target baseline |
| Backend paths changed | none |
| Database paths changed | none |
| Demo credential scripts copied | none |

Path safety is intact; functional completeness is not.

---

## 5. Conclusion

The package can be copied and deleted into `underdev`, but the resulting tree **cannot build or test**. This is a package-completeness failure, not a target-branch conflict.

**Isolated application: FAIL.**

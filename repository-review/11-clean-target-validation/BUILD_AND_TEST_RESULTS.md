# Build and Test Results

**Environment:** Node `v26.3.0`, npm `11.16.0`  
**Target:** `underdev` @ `72480fb`  
**Baseline worktree:** `target-baseline` (clean)  
**Patched worktree:** `target-patched` (package applied)

---

## 1. Commands and exit codes

| Step | Baseline | Patched |
|---|---|---|
| `npm ci` | **0** | **0** |
| `npm run test:ci` | **0** | **1** |
| `npm run build` | **0** | **1** |
| `npx eslint src --ext .js,.jsx` | **0** (4 warnings) | **0** (no output) |
| Dev-server startup | not needed | **BLOCKED** — build fails first |

Both installs completed without dependency resolution errors. The patched dependency tree is consistent with its `package-lock.json`; the failure is missing source modules, not missing packages.

---

## 2. Baseline test result

```text
Test Suites: 5 passed, 5 total
Tests:       42 passed, 42 total
Snapshots:   0 total
Time:        4.704 s
```

Exit code **0**.

This is the key comparison the prior audit asked for: the target baseline is **42/42 green**. The 40/42 result described in the previous audit belongs to the `adeeldev` working tree, not to `underdev`.

---

## 3. Patched test result

```text
PASS src/__tests__/authSession.test.js
PASS src/__tests__/slugPaths.test.js
PASS src/__tests__/searchRanking.test.js
PASS src/__tests__/orgPath.test.js
FAIL src/__tests__/PageProfile.test.js
  ● Test suite failed to run

    Cannot find module '../motion/PageTransition' from
    'src/components/page/PageManagerLayout.jsx'

Test Suites: 1 failed, 4 passed, 5 total
Tests:       40 passed, 40 total
```

Exit code **1**.

The failure is not the pre-existing auth-mock failure. It is a **new missing-module regression** introduced by applying the package to the target. `PageTransition` exists at HEAD and is imported by the package’s `PageManagerLayout`, but it was not included in the package.

---

## 4. Baseline build result

```text
> react-scripts build
Compiled successfully.
...
check-bundle: main.69ff563d.js 320.3 kB raw, 94.4 kB gzip
```

Exit code **0**. Prebuild env check passed. Postbuild bundle check passed.

The only noise was the Browserslist data-age notice (11 months old).

---

## 5. Patched build result

```text
> react-scripts build
Creating an optimized production build...
Failed to compile.

Module not found: Error: Can't resolve './components/theme/ThemeContext'
in 'target-patched/frontend/src'
```

Exit code **1**. The build stops before bundle generation. `ThemeContext` exists at HEAD and is imported by the package’s `App.js`, but it was not included in the package.

A static resolver found **94 unresolved relative imports** after patch application; the build reports the first one. This is a systemic completeness failure, not a single accidental omission.

---

## 6. Lint

- Baseline: **exit 0**, 4 warnings in `src/pages/Dashboard.jsx` (`FiAward`, `FiTrendingUp`, `FiBriefcase`, `FiBell` unused).
- Patched: **exit 0**, no lint output. ESLint does not resolve module paths, so a clean lint result does not contradict the build’s missing-module failure.

---

## 7. Route imports and startup

- Lazy import resolution: **FAIL** — 94 unresolved relative imports after apply.
- Production startup: **BLOCKED** — build fails.
- Development startup/navigation: **NOT VERIFIED** — would surface the same unresolved imports.
- No authenticated browser session was started because the patched app cannot compile.

---

## 8. Regression conclusion

| Category | Finding |
|---|---|
| Pre-existing failures reproduced on target baseline | **None** — 42/42 pass |
| New failures from package application | **Yes** — build and test suite failure |
| Dependency install failure | **No** |
| Lockfile inconsistency | **No** |
| Missing source modules in package | **Yes** — 13+ module families |

**Build/test verdict: FAIL.** The package is not applicable to `underdev` in its current form.

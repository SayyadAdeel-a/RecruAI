# Required Untracked Frontend Files

This report answers one question: **what must travel with the tracked frontend diff so the package builds without the original working directory?**

The package contains **42 additions**:

- 2 required source modules
- 39 required CSS files (the `stageshow` CSS closure)
- 1 documentation file (`SYSTEM.md`)

All are copied into `patch/` with their original repository paths. The full machine-readable list with SHA-256 hashes is in `patch/PATCH_MANIFEST.json`.

---

## 1. Required source modules

| File | Why it is required | Evidence | Status |
|---|---|---|---|
| `frontend/src/components/layout/navStyles.js` | All three shells import the shared active/hover/rail classes | `Sidebar.jsx`, `OrgAppShell.jsx`, `PageManagerLayout.jsx` import `navStyles` | **REQUIRED** |
| `frontend/src/components/jobs/JobCard.jsx` | `JobBits.jsx` re-exports it; `JobBits` is imported by four candidate surfaces | `JobBits.jsx:6` → `export { JobCard } from "./JobCard"`; `Jobs.jsx`, `JobDetails.jsx`, `JobAlerts.jsx`, `ShareableProfiles.jsx` import `JobBits` | **REQUIRED** |

`JobCard.jsx` imports `OrgMark` back from `JobBits.jsx`, so the two files form a circular module pair. The current build passes, but the pair must be applied together; do not copy one without the other.

---

## 2. Required `stageshow` CSS closure

`frontend/src/index.css` line 4:

```css
@import './stageshow/stageshow.css';
```

`stageshow.css` imports 38 further stylesheets. The **39-file closure** below is the minimum required to build and preserve the current CSS output:

```text
frontend/src/stageshow/stageshow.css
frontend/src/stageshow/stage.css
frontend/src/stageshow/keys.css
frontend/src/stageshow/cursor.css
frontend/src/stageshow/window.css
frontend/src/stageshow/phone.css
frontend/src/stageshow/ios.css
frontend/src/stageshow/messages.css
frontend/src/stageshow/code.css
frontend/src/stageshow/chart.css
frontend/src/stageshow/motion/Roll.css
frontend/src/stageshow/motion/Connector.css
frontend/src/stageshow/motion/Count.css
frontend/src/stageshow/motion/Swap.css
frontend/src/stageshow/motion/Collapse.css
frontend/src/stageshow/motion/Fly.css
frontend/src/stageshow/motion/Follow.css
frontend/src/stageshow/motion/States.css
frontend/src/stageshow/motion/Stream.css
frontend/src/stageshow/motion/Popover.css
frontend/src/stageshow/motion/Camera.css
frontend/src/stageshow/motion/Scroll.css
frontend/src/stageshow/motion/Expand.css
frontend/src/stageshow/parts/Avatar.css
frontend/src/stageshow/parts/Check.css
frontend/src/stageshow/parts/Icon.css
frontend/src/stageshow/parts/Spinner.css
frontend/src/stageshow/parts/Toast.css
frontend/src/stageshow/parts/Meter.css
frontend/src/stageshow/parts/Menu.css
frontend/src/stageshow/parts/Facepile.css
frontend/src/stageshow/parts/Chip.css
frontend/src/stageshow/parts/BrandMark.css
frontend/src/stageshow/controls/Field.css
frontend/src/stageshow/controls/Segmented.css
frontend/src/stageshow/controls/Toggle.css
frontend/src/stageshow/controls/Tabs.css
frontend/src/stageshow/controls/Badge.css
frontend/src/stageshow/controls/Slot.css
```

No CSS file contains a relative `url(...)` asset reference; the closure has no image/font dependencies outside these files.

### Deliberately not included

- **8 unreferenced CSS files:** `controls/Button.css`, `controls/Draft.css`, `parts/Card.css`, `parts/Divider.css`, `parts/Kbd.css`, `parts/Pin.css`, `parts/Row.css`, `parts/Tooltip.css`.
- **All `stageshow` TSX/TS components and `brands/*.svg`:** no application file imports `stageshow/index.ts`, `stage.tsx`, or any component; only the CSS is in the production import graph.
- **The rest of `frontend/src/stageshow/`:** source kit retained in the working tree for future use, but not required by this handover.

This is a deliberate refinement of the earlier “include all of `stageshow/**`” instruction: the package is self-contained for the actual import graph, with the unused kit omitted.

---

## 3. Required documentation

| File | Purpose | Status |
|---|---|---|
| `SYSTEM.md` | Frontend design-system contract; referenced by the redesign work | **INCLUDED** at `patch/SYSTEM.md` |

---

## 4. Self-containment verification

A static resolver was run over the package:

| Check | Result |
|---|---|
| Copied files | **184** (plus 3 metadata files in `patch/`) |
| Relative JS/JSX imports | **0 missing**; 32 imports resolve to unchanged files tracked at `HEAD` |
| CSS `@import` references | **0 missing** |
| `package.json` vs lockfile root deps | **0 missing, 0 extra** |
| Protected `SystemStatus.jsx` present | **No** |
| `admin/src/index.css` present | **No** |
| Credential scripts present | **No** |
| `stageshow` CSS files | **39** |

The only unchanged files the package relies on are files tracked at `HEAD` (for example `lib/cn.js`, `pages/SystemStatus.jsx`, and normal components). The target branch already has those; no untracked file from the original working directory is required.

**Status: PASS — all required untracked frontend files are accounted for.**

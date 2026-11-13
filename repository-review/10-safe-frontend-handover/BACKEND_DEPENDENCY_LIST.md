# Backend Dependency List

These items are **not implemented** in this handover. They are required for the frontend features below to work end-to-end. Backend files were not modified.

---

## 1. `GET /api/organizations/<id>/usage`

| Field | Detail |
|---|---|
| Caller | `frontend/src/pages/organization/Billing.jsx:68` |
| Auth | Bearer header from `getAuthHeaders()` + HttpOnly cookie |
| Expected response | `{ "usage": [ ... ] }` |
| Fields consumed | `operation_type`, `created_at` (or `timestamp`), `provider`, `model`, `tokens_used` |
| Feature | Organization Billing → “Token usage history” panel |
| Failure today | Route absent → non-2xx → `setTokenUsage([])` → panel shows “0 records”/empty state; no error surfaced |
| Status | **MISSING / BLOCKER** |

The org route inventory contains `/organizations/<id>`, `/permissions`, `/profile`, `/slug`, `/visibility`, `/team-members`, `/interviews`, `/analytics`, `/dashboard/stats`, but no `/usage`. `backend/api/billing` has no usage route.

---

## 2. `POST /api/interviews/<id>/interviewer-response`

| Field | Detail |
|---|---|
| Caller | `frontend/src/pages/InterviewRoom.jsx:219` |
| Body | `{ content, sender_name, sender_type: "interviewer", sender_user_id }` |
| Expected response | `{ id, content, sender_name, created_at, sender_type, sender_user_id }` |
| Feature | Manual interview mode / interviewer replies in the interview room |
| Failure today | Route absent → 404 → “Failed to send response”; message is not appended |
| Status | **MISSING / BLOCKER** |

The interviews route inventory contains `/interviews`, `/interviews/<id>`, `/messages`, `/complete`, `/decision`, `/conversation`, `/analyze`, `/analysis`, `/video-token`, `/recording/*`, `/chat`, `/ai/*`, but no `/interviewer-response`.

---

## 3. `application_stage_changed` emitter

| Field | Detail |
|---|---|
| Frontend listeners | `frontend/src/pages/organization/Pipeline.jsx:173`, `frontend/src/pages/individual/Jobs.jsx:98` |
| Expected event | Emitted after `PUT /pipeline/application/<app_id>/stage` (route exists at `backend/api/org/applications.py:465`) |
| Payload expected by UI | Application id, new stage, and the owning job/post identifiers used by the handlers |
| Feature | Live pipeline card movement; live candidate “Applied” stage updates without a refetch |
| Failure today | No backend emitter found anywhere in `backend/`; listeners never fire |
| Status | **MISSING / BLOCKER** |

---

## 4. `join_org` handler import issue

| Field | Detail |
|---|---|
| Frontend emit | `frontend/src/utils/socket.js:88–90` → `join_org` `{ org_id }`; called from `App.js:276` |
| Backend handler | `backend/api/sockets.py:84` |
| Bug | `backend/api/sockets.py:102` imports `from ...utils.org_permissions import get_org_access`; from `backend.api.sockets`, `...utils` is one level too deep (elsewhere the file uses `..utils.cache`) |
| Effect | Import raises; the `except Exception` at line 110 logs `join_org auth failed` and returns before `join_room("org_<id>")` |
| Feature | Org-scoped realtime room (pipeline/notification events) |
| Status | **BROKEN / BLOCKER** |

Static finding; not exercised against a live broker in this mission.

---

## 5. InterviewRoom `Bearer null`

| Field | Detail |
|---|---|
| Code | `frontend/src/pages/InterviewRoom.jsx:218` → `const token = localStorage.getItem("token")` |
| Header | `Authorization: Bearer ${token}` |
| Reality | Access tokens are held in memory only (`utils/auth.js`); `localStorage.token` is `null` |
| Effect | Even if the endpoint existed, the request is unauthenticated (`Bearer null`) |
| Feature | Manual interviewer responses (same screen as item 2) |
| Status | **BROKEN / BLOCKER** |

The correct pattern is the in-memory auth header plus `credentials: "include"` (as used by the rest of the app), but this mission does not implement it.

---

## Feature-to-dependency summary

| Frontend feature | Dependencies |
|---|---|
| Org Billing usage history | 1 |
| Manual interview room replies | 2 + 5 |
| Live org pipeline updates | 3 + 4 |
| Live candidate applied-stage updates | 3 + 4 |

No backend or database change is included in this handover. These items belong in a separate backend PR and must land before or with the frontend merge.

# Backend Dependencies (Separate — Not in Patch)

These items are **not implemented** and are **not part of the frontend patch**. They must land in a separate backend change before the affected screens are called complete.

---

## 1. `GET /api/organizations/<id>/usage`

| Field | Detail |
|---|---|
| Frontend caller | `frontend/src/pages/organization/Billing.jsx` |
| Request | `GET`, bearer auth + HttpOnly cookie |
| Expected response | `{ "usage": [ ... ] }` |
| Consumed fields | `operation_type`, `created_at` / `timestamp`, `provider`, `model`, `tokens_used` |
| Feature | Organization Billing → Token usage history |
| Current behavior | Route absent; panel silently shows zero records |

## 2. `POST /api/interviews/<id>/interviewer-response`

| Field | Detail |
|---|---|
| Frontend caller | `frontend/src/pages/InterviewRoom.jsx` |
| Request body | `{ content, sender_name, sender_type: "interviewer", sender_user_id }` |
| Expected response | `{ id, content, sender_name, created_at, sender_type, sender_user_id }` |
| Feature | Manual interview mode / interviewer replies |
| Current behavior | Route absent; reply fails |

## 3. `application_stage_changed` emitter

| Field | Detail |
|---|---|
| Frontend listeners | `frontend/src/pages/organization/Pipeline.jsx`, `frontend/src/pages/individual/Jobs.jsx` |
| Expected trigger | After `PUT /pipeline/application/<app_id>/stage` |
| Feature | Live pipeline movement and candidate applied-stage updates |
| Current behavior | No backend emitter exists; listeners never fire |

## 4. `join_org` import issue

| Field | Detail |
|---|---|
| Frontend emit | `frontend/src/utils/socket.js` → `join_org` `{ org_id }` |
| Backend handler | `backend/api/sockets.py` |
| Bug | `from ...utils.org_permissions import get_org_access` is one level too deep; should resolve from `backend.utils` |
| Effect | Handler catches the import error and returns before joining the org room |
| Feature | Org-scoped realtime room |

## 5. InterviewRoom `Bearer null`

| Field | Detail |
|---|---|
| Code | `localStorage.getItem("token")` in `InterviewRoom.jsx` |
| Reality | Access tokens are memory-only; the value is `null` |
| Effect | Even with the endpoint, the request is unauthenticated |
| Feature | Manual interviewer responses |
| Correct direction | Use the in-memory auth header plus `credentials: "include"` (not implemented here) |

---

## Feature dependency map

| Frontend feature | Requires |
|---|---|
| Org Billing usage history | 1 |
| Manual interview replies | 2 + 5 |
| Live org pipeline | 3 + 4 |
| Live candidate stage updates | 3 + 4 |

No backend or database change is included in `frontend-handover.patch` or `frontend-handover.zip`.

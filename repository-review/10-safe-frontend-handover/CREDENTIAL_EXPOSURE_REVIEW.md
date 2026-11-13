# Credential Exposure Review

**Files inspected (not reproduced):**

- `frontend/scripts/visual-check-key.cjs`
- `frontend/scripts/visual-sweep.cjs`

Both are **untracked** and **excluded from the handover package**.

---

## 1. What the scripts contain

The two scripts hard-code three demo account identifiers and a shared demo password for the local seeded database:

- Accounts use `.test` email addresses (reserved, non-public test domain).
- The password is a shared demo password used across the three seeded accounts.
- The scripts use those credentials to log in through the local app at `http://localhost:3000` for screenshot/contrast sweeps.

No API keys, bearer tokens, private keys, OAuth secrets, database URLs, or production service credentials were found in either script.

**No credential values are reproduced in this report.**

---

## 2. Classification

| Question | Finding |
|---|---|
| Actual secrets? | **No** — no service/API secret. They are working credentials for local demo users. |
| Dummy credentials? | **Yes** — `.test` addresses and a shared demo password for seeded data. |
| Test-only accounts? | **Yes, by design** — the accounts are named for demo roles (candidate, org admin, superadmin) and match the local seed data. |
| Publicly exposed configuration? | **Not in Git** — both scripts are untracked. They are local files, not published. |
| Real production risk? | **Conditional** — if the same demo accounts/passwords exist in a shared staging or production environment, the values are usable there. No evidence of such deployment was found in the repository. |

The scripts are not part of the application bundle, are not imported by frontend code, and are not included in `patch/`.

---

## 3. Handover scan

A scan of the 184 copied patch files for credential patterns and demo-account strings found:

- **0** demo account emails
- **0** demo passwords
- **0** private-key / cloud-key / GitHub-token patterns
- Only ordinary source-code password **field names** in reset/invite/settings forms

The two scripts remain in the original working tree, untouched, as required. They are not deleted or rewritten.

---

## 4. Rotation recommendation

If the demo accounts exist only in the local developer database, rotation is optional. If they exist in any shared, staging, demo, or production environment:

1. Rotate the shared demo password for all three accounts.
2. Revoke existing sessions/refresh tokens for those accounts.
3. Confirm the accounts are disabled or scoped to non-production data.
4. Replace hard-coded credentials in local scripts with environment variables or a local `.env` that is ignored.
5. Audit authentication logs for unexpected use of the three demo identities.
6. Keep `frontend/scripts/visual-*.cjs` out of Git and out of the handover.

**Notification:** no real production secret was found; exposure is limited to local/test demo accounts. Rotation is recommended only if those accounts are reachable outside the local machine.

---

## 5. Status

**PASS with WARNING.**

- Handover contains no credentials: **PASS**
- Scripts excluded and untouched: **PASS**
- Real secrets found: **No**
- Rotation required: **Conditional** on whether the demo accounts are shared/deployed

---
name: splitwise-check-group-balances
description: Read a Splitwise group's balances and who owes whom.
api: splitwise
generated: '2026-07-21'
method: generated
source: Grounded in openapi/splitwise-openapi.json operations (paths/methods; the spec declares no operationIds).
operations:
- GET /get_groups
- GET /get_group/{id}
- GET /get_expenses
- GET /get_friends
---

# Check group balances

Summarize what each member owes within a Splitwise group.

## Auth
Send `Authorization: Bearer <token>`. Base URL: `https://secure.splitwise.com/api/v3.0`.

## Steps
1. `GET /get_groups` — list the user's groups; pick the target `group_id`.
2. `GET /get_group/{id}` — read `members[]` and the `simplified_debts[]` (or `original_debts[]`)
   array; each debt has `from` (user id), `to` (user id), `amount`, and `currency_code`.
3. `GET /get_expenses?group_id={id}&limit=20&offset=0` — page through recent expenses for detail;
   increase `offset` by `limit` until fewer than `limit` rows return.
4. Optionally `GET /get_friends` to read cross-group per-friend `balance[]` amounts.
5. Present net balances per member and the minimal set of simplified debts.

## Rules
- Amounts are decimal strings (e.g. "414.5"); keep them as strings to avoid float rounding.
- `simplified_debts` is Splitwise's minimized settle-up plan; `original_debts` is the raw ledger.
- A `200` may still carry `errors`; inspect the body. See errors/splitwise-problem-types.yml.

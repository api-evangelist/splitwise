---
name: splitwise-log-shared-expense
description: Log a shared expense in Splitwise and split it among group members.
api: splitwise
generated: '2026-07-21'
method: generated
source: Grounded in openapi/splitwise-openapi.json operations (paths/methods; the spec declares no operationIds).
operations:
- GET /get_current_user
- GET /get_groups
- GET /get_group/{id}
- GET /get_categories
- POST /create_expense
- GET /get_expense/{id}
---

# Log a shared expense

Create an expense in Splitwise and split it among the members of a group.

## Auth
Send `Authorization: Bearer <token>` — an OAuth 2.0 access token (authorization-code flow) or a
personal API key from https://secure.splitwise.com/apps. Base URL: `https://secure.splitwise.com/api/v3.0`.

## Steps
1. `GET /get_current_user` — confirm the authenticated user and their `default_currency`.
2. `GET /get_groups` (or `GET /get_group/{id}`) — find the target group and its member user ids.
3. `GET /get_categories` — pick a subcategory id (create_expense requires a subcategory, not a parent).
4. `POST /create_expense` — send `cost`, `description`, `currency_code`, `group_id`, and the split.
   For an even split use `split_equally: true`; for a custom split send `users__{i}__user_id`,
   `users__{i}__paid_share`, and `users__{i}__owed_share` (paid and owed shares must each sum to `cost`).
5. Verify: a `200` does NOT mean success — inspect the response body. Check the returned `expenses[]`
   and that `errors` is empty. Optionally `GET /get_expense/{id}` to confirm.

## Rules
- No idempotency key exists; do not blindly retry `create_expense` or you will create duplicates.
- On `400` inspect `errors.base[]` (e.g. unrecognized parameter); on `403` the user is not a group member.
- See conventions/splitwise-conventions.yml and errors/splitwise-problem-types.yml.

---
name: Authenticate and check BCB account balances
description: Obtain an OAuth2 client-credentials token and read BCB Group accounts, balances and transactions.
api: openapi/bcb-group-payments-openapi.json
operations: [Auth_GetTokenV1, Accounts_ListAccountsV3, Accounts_BalanceV4, Accounts_TransactionsV3, Accounts_TransactionDetailV3]
---

# Authenticate and check BCB account balances

Use this to read balances and transaction history from BCB Group.

## Setup
- Base URL: `https://api.bcb.group` (production) or `https://api.uat.bcb.group` (sandbox). Credentials are per-environment — start in sandbox.

## Steps
1. **Get a token** — `Auth_GetTokenV1` (`POST /v1/auth/oauth/token`) with JSON body `{"client_id","client_secret"}`. Read `access_token` and `expires_in` (unix expiry). Present it as `Authorization: Bearer <access_token>` on every subsequent call.
2. **List accounts** — `Accounts_ListAccountsV3` (`GET /v3/accounts`) to enumerate bank, custody and wallet accounts and their `accountId`/`iban`.
3. **Get a balance** — `Accounts_BalanceV4` (`GET /v4/accounts/balance/{accountId}`) for available/pending balance.
4. **List transactions** — `Accounts_TransactionsV3` (`GET /v3/accounts/{accountId}/transactions`) paging with `limit` + `pageToken` and optional `date_From`/`date_To` (YYYY-MM-DD).
5. **Transaction detail** — `Accounts_TransactionDetailV3` (`GET /v3/accounts/{accountId}/transactions/{transactionId}`).

## Rules
- Refresh the token before `expires_in`. `401` means missing/invalid token; `403` means insufficient permissions.
- Prefer webhooks over polling (see the webhooks skill); BCB monitors for polling abuse.

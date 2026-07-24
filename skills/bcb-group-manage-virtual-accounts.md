---
name: Manage BCB virtual accounts
description: Create, inspect, pay from, update and close BCB virtual IBAN accounts.
api: openapi/bcb-group-payments-openapi.json
operations: [Auth_GetTokenV1, VirtualAccounts_CreateVirtualAccountV2, VirtualAccounts_ListAccountsV1, VirtualAccounts_AuthorisePaymentV1, VirtualAccounts_UpdateOwnerAddressV1, VirtualAccounts_UpdateOwnerBankDetailsV1, VirtualAccounts_CloseVirtualAccountV1]
---

# Manage BCB virtual accounts

Provision and operate multi-currency virtual IBAN accounts under a segregated BCB account.

## Steps
1. **Token** — `Auth_GetTokenV1` (`POST /v1/auth/oauth/token`); Bearer on all calls.
2. **Create** — `VirtualAccounts_CreateVirtualAccountV2` (`POST /v2/accounts/{accountId}/virtual`) for the owner(s). Include a unique `correlationId`; the response returns the assigned `iban`.
3. **List** — `VirtualAccounts_ListAccountsV1` (`GET /v1/accounts/{accountId}/virtual/all-account-data`) to inspect all virtual accounts for the segregated account.
4. **Pay** — `VirtualAccounts_AuthorisePaymentV1` (`POST /v1/accounts/{accountId}/virtual/{iban}/payment`).
5. **Update owner** — `VirtualAccounts_UpdateOwnerAddressV1` (`PUT .../{iban}/owner-address`) or `VirtualAccounts_UpdateOwnerBankDetailsV1` (`PUT .../{iban}/owner-bank-details`). A `400` means the request is invalid; `404` means the accountId/IBAN is unknown.
6. **Close** — `VirtualAccounts_CloseVirtualAccountV1` (`POST .../{iban}/close`).

## Rules
- Validate the IBAN's holding bank first with `Tools_BankLookup` (`GET /v1/bank-lookup`) when you only have an IBAN.
- Paginate list responses with `limit` + `pageToken`.

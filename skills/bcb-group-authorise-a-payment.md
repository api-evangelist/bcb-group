---
name: Authorise a BCB payment with idempotency
description: Create a beneficiary, authorise a payment, and track its status safely with correlationId idempotency.
api: openapi/bcb-group-payments-openapi.json
operations: [Auth_GetTokenV1, Beneficiaries_CreateBeneficiaryV4, Beneficiaries_ListBeneficiariesV3, Payments_AuthorisePaymentV5, Payments_PaymentByIdV1, Payments_VerificationOfPayeeApproveV1, Payments_VerificationOfPayeeCancelV1]
---

# Authorise a BCB payment with idempotency

Send a payment from a BCB account to a beneficiary and confirm it settled.

## Steps
1. **Token** — `Auth_GetTokenV1` (`POST /v1/auth/oauth/token`); use the Bearer token on all calls.
2. **Beneficiary** — reuse one via `Beneficiaries_ListBeneficiariesV3` (`GET /v3/beneficiaries`) or create with `Beneficiaries_CreateBeneficiaryV4` (`POST /v4/accounts`).
3. **Authorise** — `Payments_AuthorisePaymentV5` (`POST /v5/payments/authorise`). The payload varies by scheme/currency (check the per-type examples). Set a unique `correlationId` on the request for exactly-once idempotency — safe to retry the same correlationId after a network failure without duplicating the payment.
4. **Verification of Payee (EUR)** — if the beneficiary name does not match, resolve with `Payments_VerificationOfPayeeApproveV1` (`POST /v1/payments/{accountId}/verification-of-payee/{end2endId}/approve`) or `Payments_VerificationOfPayeeCancelV1` (`.../cancel`). A `409` means the payment is not in an unverified state.
5. **Track** — `Payments_PaymentByIdV1` (`GET /v1/payments/{accountId}/transaction/{transactionId}`), or subscribe to the payment-status webhook, to confirm a `Complete` status.

## Rules
- Reuse the same `correlationId` on retries; never mint a new one for a retried payment.
- On `500`, retry with the original correlationId.

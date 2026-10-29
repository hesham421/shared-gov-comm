# ADR-PTY-024 — Error Catalog: PLATFORM-STD rows, status mapping and slugs

Status  : ACCEPTED (non-breaking) · Stage : P3.1 · Module : PTY v1 · Date : 2026-10-29

## Context
The engine requires infrastructure errors (malformed request, unauthenticated, forbidden, not found, server failure) to be Error Catalog rows with rule `PLATFORM-STD` and an ADR. The SRS gives no message text for them. The SRS also does not say which HTTP status each business rule maps to. The profile fixes the code format `{MOD}-{http}[-{SLUG}]` and the statuses the platform can emit (200, 201, 400, 401, 403, 404, 409, 413, 415, 422, 500).

## Decision
- **Status mapping for rule rows.**
  - 422: a rule that refuses the content of a request (format, range, required value, lookup value, flag off, state that the caller can fix).
  - 409: a rule that refuses because of other data that already exists — RULE-PTY-002 email taken, RULE-PTY-009 role held, RULE-PTY-026 request no longer pending.
  - 403: RULE-PTY-029 (only the requester cancels), because it is an authorisation outcome.
  - 500: RULE-PTY-030 (audit not recorded), because it is a server-side failure that the caller can only retry.
- **Slugs.** Upper-case words that name the meaning (e.g. `PTY-422-NAME-REQUIRED`). They are stable, because downstream stages cite the code and not the text.
- **PLATFORM-STD rows and their messages:**
  - PTY-400-VALIDATION — «الطلب غير صالح: تحقّق من الحقول المرسلة.» / "The request is not valid: check the submitted fields."
  - PTY-401-UNAUTHENTICATED — «سجّل الدخول للمتابعة.» / "Sign in to continue."
  - PTY-403-FORBIDDEN — «ليست لديك صلاحية لتنفيذ هذا الإجراء.» / "You do not have permission to perform this action."
  - PTY-404-CUSTOMER-NOT-FOUND — «العميل غير موجود.» / "The customer was not found."
  - PTY-404-SUPPLIER-NOT-FOUND — «المورّد غير موجود.» / "The supplier was not found."
  - PTY-404-PARTY-NOT-FOUND — «الطرف غير موجود.» / "The party was not found."
  - PTY-404-ADDRESS-NOT-FOUND — «العنوان غير موجود.» / "The address was not found."
  - PTY-404-CREDIT-OVERRIDE-NOT-FOUND — «طلب تجاوز حد الائتمان غير موجود.» / "The credit override request was not found."
  - PTY-500-INTERNAL — «حدث خطأ غير متوقع. أعد المحاولة.» / "An unexpected error occurred. Try again."
- A record of another tenant reads as not found.
- No row describes a network failure, because the platform is one deployable and PTY calls only in-process interfaces.

## Consequences
- Every operation of `api-spec-pty.yaml` answers 400, 401, 403 and 500. The global search (API-PTY-027) is the exception for 403: it answers an empty list instead (REQ-PTY-043).
- The texts of the PLATFORM-STD rows are this module's defaults. If the given erp-core publishes platform-wide texts for these statuses, the executor may use them, but the codes do not change.

## Traces
backend-execution-plan-pty.md §Error Catalog · REQ-PTY-043, REQ-PTY-090 · RULE-PTY-002, RULE-PTY-009, RULE-PTY-026, RULE-PTY-029, RULE-PTY-030

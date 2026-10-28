# ADR-CAT-044 — Platform-standard error rows of the CAT backend

Status : ACCEPTED (non-breaking) · Stage : P3.1 · Module : CAT v2 · Date : 2026-10-28

## Context
The SRS RULEs cover every business refusal of the catalogue, but an endpoint can also fail for reasons no RULE names: the caller is not authenticated, lacks the page permission, names a record that does not exist (or belongs to another tenant — contract §0 "a record of another tenant reads as not found"), sends a malformed body, sends a stale `version`, uploads a file above the server's multipart limit or of a content type the endpoint does not read, or the server fails. The engine requires these to be Error Catalog rows with rule `PLATFORM-STD` and an ADR. The SRS gives no message text for them.

## Decision
The plan's Error Catalog carries six `PLATFORM-STD` rows, each an instance of `{MOD}-{http}[-{SLUG}]`. The two multipart rows (`CAT-413-PAYLOAD-TOO-LARGE`, `CAT-415-UNSUPPORTED-MEDIA-TYPE`) are deleted: CAT has no multipart endpoint since ADR-CAT-056.

| Code | HTTP | Raised when | Message ar · en |
|---|---|---|---|
| CAT-400-REQUEST-INVALID | 400 | body or parameter missing, of the wrong type, or failing a declared length/format before any RULE runs | الطلب غير صالح. راجع الحقول المحددة ثم أعد المحاولة. · The request is not valid. Check the highlighted fields and try again. |
| CAT-401-UNAUTHENTICATED | 401 | no valid bearer token | انتهت جلستك أو لم تسجّل الدخول. سجّل الدخول ثم أعد المحاولة. · You are not signed in or your session has ended. Sign in, then try again. |
| CAT-403-FORBIDDEN | 403 | the caller lacks the endpoint's `PERM_<PAGE_CODE>_<ACTION>` | ليس لديك صلاحية لتنفيذ هذه العملية. اطلب الصلاحية من مالك النشاط. · You do not have permission to perform this operation. Ask the business owner for access. |
| CAT-404-NOT-FOUND | 404 | the identifier names no record of the caller's tenant | السجل المطلوب غير موجود. حدّث القائمة ثم أعد المحاولة. · The requested record was not found. Refresh the list and try again. |
| CAT-409-STALE-VERSION | 409 | the `version` echoed by an update differs from the stored one (optimistic lock) | عدّل مستخدم آخر هذا السجل في الأثناء. أعد تحميله ثم حاول مجددًا. · This record was changed by someone else in the meantime. Reload it and try again. |
| CAT-500-SERVER-ERROR | 500 | any unexpected failure; the transaction rolls back and no event is delivered | حدث خطأ غير متوقع ولم يُحفظ شيء. حاول مرة أخرى لاحقًا. · An unexpected error occurred, and nothing was saved. Try again later. |

These are `CAT-` codes, so each is a constant of `CatErrorCodes` with its own entry in `messages.properties` (en) and `messages_ar.properties` (ar), carrying the text above (checklist C11). The texts follow the core's shared messages for `VALIDATION_ERROR`, `ACCESS_DENIED`, `NOT_FOUND`, `CONCURRENT_MODIFICATION` and `INTERNAL_ERROR`, each with a next action added. The Error Catalog rows carry them verbatim. (Amended at the gate-analysis round 2, G6: the earlier text "the given platform's standard text; CAT invents none" left the six CAT codes without bundle entries.)

A seventh `PLATFORM-STD` row, `CAT-404-LOOKUP-KEY-UNKNOWN`, belongs to ADR-CAT-065. Failures that a RULE names (number series — RULE-CAT-005, capability flags — RULE-CAT-009, rolled-back change — RULE-CAT-049) keep their own rows. No row describes a network failure: every given and module interface is in-process.

## Consequences
- The API document answers every operation with the rows it can raise; the frontend shows the envelope's messages.
- A further infrastructure case is a new row with its ar and en text and a revision of this ADR's successor, never a free-text error.

## Traces
REQ-CAT-001 · REQ-CAT-010 · REQ-CAT-035 · REQ-CAT-110 · REQ-CAT-040 · RULE-CAT-049

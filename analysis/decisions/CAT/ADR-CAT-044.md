# ADR-CAT-044 — Platform-standard error rows of the CAT backend

Status : ACCEPTED (non-breaking) · Stage : P3.1 · Module : CAT v2 · Date : 2026-10-28

## Context
The SRS RULEs cover every business refusal of the catalogue, but an endpoint can also fail for reasons no RULE names: the caller is not authenticated, lacks the page permission, names a record that does not exist (or belongs to another tenant — contract §0 "a record of another tenant reads as not found"), sends a malformed body, sends a stale `version`, uploads a file above the server's multipart limit or of a content type the endpoint does not read, or the server fails. The engine requires these to be Error Catalog rows with rule `PLATFORM-STD` and an ADR. The SRS gives no message text for them.

## Decision
The plan's Error Catalog carries eight `PLATFORM-STD` rows, each an instance of `{MOD}-{http}[-{SLUG}]`:

| Code | HTTP | Raised when |
|---|---|---|
| CAT-400-REQUEST-INVALID | 400 | body or parameter missing, of the wrong type, or failing a declared length/format before any RULE runs |
| CAT-401-UNAUTHENTICATED | 401 | no valid bearer token |
| CAT-403-FORBIDDEN | 403 | the caller lacks the endpoint's `PERM_<PAGE_CODE>_<ACTION>` |
| CAT-404-NOT-FOUND | 404 | the identifier names no record of the caller's tenant |
| CAT-409-STALE-VERSION | 409 | the `version` echoed by an update differs from the stored one (optimistic lock) |
| CAT-413-PAYLOAD-TOO-LARGE | 413 | the multipart request exceeds the server limit before the service sees it |
| CAT-415-UNSUPPORTED-MEDIA-TYPE | 415 | the request content type is not the one the endpoint reads |
| CAT-500-SERVER-ERROR | 500 | any unexpected failure; the transaction rolls back and no event is delivered |

Their message text is the given platform's standard `LocalizedException` text (messageAr / messageEn); CAT invents none. Failures that a RULE names (number series — RULE-CAT-005, capability flags — RULE-CAT-009, rolled-back change — RULE-CAT-049) keep their own rows. No row describes a network failure: every given and module interface is in-process.

## Consequences
- The API document answers every operation with the rows it can raise; the frontend shows the envelope's messages.
- A ninth infrastructure case is a new row and a revision of this ADR's successor, never a free-text error.

## Traces
REQ-CAT-001 · REQ-CAT-010 · REQ-CAT-035 · REQ-CAT-110 · REQ-CAT-040 · RULE-CAT-049

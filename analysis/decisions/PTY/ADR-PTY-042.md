# ADR-PTY-042 — One open Credit override request per sale; a use is bound to the sale it was requested for

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise (G13) · Module : PTY v1 · Date : 2026-10-29

## Context
Two gaps remained in the request rules:
- Two PENDING requests could exist for the same Customer and sale document reference.
- A use (API-PTY-036) compared only the Customer and the amount. An approval requested for sale A could therefore be used on sale B of the same Customer with a smaller amount.

## Decision
1. **One open request per sale** (RULE-PTY-034, REQ-PTY-113). A request is open while its statusCode is PENDING or APPROVED. The partial unique index UQ_PTY_CREDIT_OVERRIDE_REQUEST_OPEN_SOURCE `(TENANT_ID, CUSTOMER_FK, SOURCE_DOCUMENT_REF) WHERE STATUS_CODE IN ('PENDING', 'APPROVED')` enforces it.
   - The service reads the open request first (QR-PTY-040) so that the refusal names its docNo.
   - The index is the race backstop: a second insert that slips past the read is translated to the same refusal after re-reading the holder.
   - The refusal is PTY-409-REQUEST-OPEN-FOR-SALE (409).
   - A REJECTED, CANCELLED or USED request does not block a new one for the same sale.
2. **A use is bound to its source sale** (RULE-PTY-028, extended). A use must carry a `usedDocumentRef` equal to the request's `sourceDocumentRef`. Any other reference is refused with PTY-422-APPROVAL-NOT-COVERING, the same refusal as another Customer or a larger amount.
   - The consumer's document reference is the consumer's own stable key for the sale, so a completed sale uses the reference it requested under (AC-PTY-102, AC-PTY-110: "POS-1-000455").
   - This is the stricter of the two options. Approval is a control on one sale, and letting an approval travel to another sale would defeat it.
3. `usedDocumentRef` stays a recorded column (DBF-PTY-059) for the audit trail.

## Consequences
- SRS: RULE-PTY-034 and REQ-PTY-113 are added with an AC. RULE-PTY-028, REQ-PTY-097 and REQ-PTY-099 state the binding, and AC-PTY-133 covers a mismatched reference.
- The db-script adds the partial unique index. The backend plan adds QR-PTY-040 and catalog row PTY-409-REQUEST-OPEN-FOR-SALE. The api-spec gains the new 409 code on API-PTY-019.
- Contract: CON-PTY-016 and CON-PTY-018 gain the error meanings. These are additive, and no consumer has been built yet.

## Traces
RULE-PTY-028, RULE-PTY-034 · REQ-PTY-097, REQ-PTY-099, REQ-PTY-113 · DBF-PTY-055, DBF-PTY-059, DBF-PTY-060, DBF-PTY-063 · API-PTY-019, API-PTY-036 · QR-PTY-040

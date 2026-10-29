# ADR-PTY-033 — Credit values on the Customer screen: read-only, edited on Customer credit

Status  : ACCEPTED (non-breaking) · Stage : P3.2 · Module : PTY v1 · Date : 2026-10-29

## Context
The SRS states two things about credit fields on the Customer screen (SCR-REQ-PTY-001 → SCR-PTY-001):
- SCR-REQ-PTY-001 B3 (Input), verbatim: "Credit fields are not on this screen; they belong to SCR-REQ-PTY-003 (REQ-PTY-070)."
- AC-PTY-082 (REQ-PTY-073), verbatim: "the Customer screen shows the creditLimit and paymentTermDays fields, the menu shows Customer credit, Credit override requests and Credit control settings, and 0 setup steps are presented".
- AC-PTY-078 (REQ-PTY-070), verbatim: "the Customer screen shows 0 credit fields and 0 credit filters" (flag off).

Read as written, B3 forbids the fields and AC-PTY-082 requires them. B3 is the screen's Input section; AC-PTY-082 asks that the fields be shown. The Customer read (API-PTY-002) carries `creditLimit` and `paymentTermDays` only while `b2bCredit` is on (REQ-PTY-071); the Customer update body (API-PTY-003, `CustomerUpdateRequest`) carries no credit field; credit is written only through API-PTY-017 / API-PTY-018 (SCR-PTY-003).

## Decision
- SCR-PTY-001 shows `creditLimit` and `paymentTermDays` as **read-only display values**, in a "Credit" group, only while `b2bCredit` is on and the values are present in the API-PTY-002 response.
- They are never inputs on SCR-PTY-001, never in its search filters or result columns, and never sent by its Save.
- The group carries an "Edit credit" «تعديل الائتمان» link to SCR-PTY-003 (`/pty/customer-credits/:customerId`), offered to users holding PERM_PTY_CUSTOMER_CREDIT_VIEW. Editing stays on SCR-PTY-003.
- While the flag is off the group is absent: 0 credit fields and 0 credit filters (AC-PTY-078).

## Why it is not breaking
B3 is respected: SCR-PTY-001 takes no credit input. AC-PTY-082 is respected: the fields are shown once the flag is on. AC-PTY-078 is respected: nothing is shown while it is off. No REQ, RULE, field or permission is added, and no locked decision changes.

## Consequences
- The ui-ux-spec block SCR-PTY-001 and the F1/F2/F3/F4 SUBs of SCR-PTY-001 carry the read-only group. The `CustomerFormValues` type excludes the credit fields.
- If a later SRS version wants credit to be edited on the Customer screen, that is a change to SCR-REQ-PTY-001 B3 and to the Customer update operation, not to this plan.

## Traces
SCR-PTY-001, SCR-PTY-003 · REQ-PTY-070, REQ-PTY-071, REQ-PTY-073 · AC-PTY-078, AC-PTY-082 · API-PTY-002, API-PTY-003, API-PTY-017

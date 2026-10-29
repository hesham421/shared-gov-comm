<!-- source: PHASE:F3 / SUB:F3-SCR-CAT-004 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-048, API-CAT-049, REQ-CAT-036, REQ-CAT-037, REQ-CAT-038, REQ-CAT-039, REQ-CAT-040, REQ-CAT-041, REQ-CAT-139, SCR-CAT-004 -->
<!-- SUB:F3-SCR-CAT-004:START traces=REQ-CAT-139,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,API-CAT-048,API-CAT-049,SCR-CAT-004 -->
### F3 · SCR-CAT-004 — Item import

| Field | Validator | Message |
|---|---|---|
| file | required · extension `.xlsx` · ≤ 10 MB | RULE-CAT-044 |

The row count (≤ 5,000), the header row and every row rule are checked by the server only (RULE-CAT-044, RULE-CAT-045). The client never parses the sheet.
<!-- SUB:F3-SCR-CAT-004:END -->

<!-- source: PHASE:F3 / SUB:F3-SCR-CAT-005 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-007, API-CAT-042, API-CAT-050, API-CAT-056, REQ-CAT-101, REQ-CAT-102, REQ-CAT-103, REQ-CAT-104, REQ-CAT-148, SCR-CAT-005 -->
<!-- SUB:F3-SCR-CAT-005:START traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-148,API-CAT-007,API-CAT-042,API-CAT-050,API-CAT-056,SCR-CAT-005 -->
### F3 · SCR-CAT-005 — Barcode labels

| Field | Validator | Message |
|---|---|---|
| lines | ≥ 1 line (Print is disabled while empty) | — |
| copies (per line) | integer 1–999 | RULE-CAT-036 |
| total copies | ≤ 5,000 | RULE-CAT-036 |
| barcode (per line) | lines with an empty label barcode (no active GTIN or INTERNAL barcode in the base unit — ADR-CAT-079) block Print and are flagged; each flagged line offers "Generate internal barcode" (REQ-CAT-148), which clears the flag on success | RULE-CAT-037 ({count} filled with the flagged lines) |
<!-- SUB:F3-SCR-CAT-005:END -->

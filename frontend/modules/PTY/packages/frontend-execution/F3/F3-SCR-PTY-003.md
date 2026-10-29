<!-- source: PHASE:F3 / SUB:F3-SCR-PTY-003 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-056, AC-PTY-077, AC-PTY-087, AC-PTY-088, AC-PTY-096, AC-PTY-122, AC-PTY-123, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-030, API-PTY-031, REQ-PTY-048, REQ-PTY-064, REQ-PTY-065, REQ-PTY-066, REQ-PTY-067, REQ-PTY-068, REQ-PTY-069, REQ-PTY-070, REQ-PTY-072, REQ-PTY-073, REQ-PTY-077, REQ-PTY-078, REQ-PTY-080, REQ-PTY-085, REQ-PTY-106, REQ-PTY-108, REQ-PTY-109, SCR-PTY-003 -->
<!-- SUB:F3-SCR-PTY-003:START traces=REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-106,REQ-PTY-108,REQ-PTY-109,AC-PTY-056,AC-PTY-077,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-122,AC-PTY-123,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-030,API-PTY-031,SCR-PTY-003 -->
### F3 · SCR-PTY-003 — Customer credit

| Form | Fields and validators | Mandatory marks | Submits to |
|---|---|---|---|
| `CreditForm` | `creditLimit` optional decimal ≥ 0 → RULE-PTY-022 (empty = `null` = no credit allowance, REQ-PTY-068); `paymentTermDays` → RULE-PTY-008; refinement: when the loaded standing has `isWalkInFl = true` and either field is non-empty → RULE-PTY-011 on the form | 0 (SRS B3) | CREDIT-SAVE |
| `CreditSearchForm` | the `CreditSearchFilter` fields; `carriesCredit` yes / no / any | 0 | the route's search parameters (CREDIT-SEARCH-QUERY) |

A Seller/Cashier (VIEW only) gets `CreditForm` read-only and no Save or Clear credit. Clear credit is a command: disabled while `CreditForm` is dirty.
<!-- SUB:F3-SCR-PTY-003:END -->

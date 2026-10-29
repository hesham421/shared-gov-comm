<!-- source: PHASE:F4 / SUB:F4-SCR-PTY-003 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-056, AC-PTY-077, AC-PTY-087, AC-PTY-088, AC-PTY-096, AC-PTY-122, AC-PTY-123, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-030, API-PTY-031, REQ-PTY-048, REQ-PTY-064, REQ-PTY-065, REQ-PTY-066, REQ-PTY-067, REQ-PTY-068, REQ-PTY-069, REQ-PTY-070, REQ-PTY-072, REQ-PTY-073, REQ-PTY-077, REQ-PTY-078, REQ-PTY-080, REQ-PTY-085, REQ-PTY-106, REQ-PTY-108, REQ-PTY-109, REQ-PTY-110, SCR-PTY-003 -->
<!-- SUB:F4-SCR-PTY-003:START traces=REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-106,REQ-PTY-108,REQ-PTY-109,REQ-PTY-110,AC-PTY-056,AC-PTY-077,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-122,AC-PTY-123,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-030,API-PTY-031,SCR-PTY-003 -->
### CREDIT-SCREEN — SCR-PTY-003            traces=REQ-PTY-064,REQ-PTY-069,REQ-PTY-077,REQ-PTY-085,REQ-PTY-108,API-PTY-016,API-PTY-017,API-PTY-030,API-PTY-031
Chunk        : `pty-customer-credit`
Routes       :
| Route | Element | Guard |
|---|---|---|
| `/pty/customer-credits` | `CreditSearchPage` | `b2bCredit` + PERM_PTY_CUSTOMER_CREDIT_VIEW |
| `/pty/customer-credits/:customerId` | `CreditEntryPage` (read-only without UPDATE) | `b2bCredit` + PERM_PTY_CUSTOMER_CREDIT_VIEW |
Guard        : flag off → not mounted (the shell's not-found / home); no VIEW → the unauthorized page
Facade       : CREDIT-FACADE (F2)
Cross-module : none
Composition  : `container page`. `CreditEntryPage` = read-only identity group, `CreditForm` (credit limit, payment terms), read-only standing (open exposure, available credit), then the inline `CreditHistoryPane` (read-only table with the body's own pager — no nested scroll region, no save).
Saves        : ONE — `Save` runs CREDIT-SAVE (one call). Clear credit is a separate confirmed command, disabled while dirty.
Navigation   : row click → `/pty/customer-credits/:customerId` · "Credit override requests" (PERM_PTY_CREDIT_OVERRIDE_VIEW) → `/pty/credit-overrides?customerId=:customerId` · "Credit control settings" (PERM_PTY_CREDIT_SETTINGS_VIEW) → `/pty/credit-settings` · entry `/pty/customer-credits?carriesCredit=true` seeds the filter (REQ-PTY-077).
<!-- SUB:F4-SCR-PTY-003:END -->

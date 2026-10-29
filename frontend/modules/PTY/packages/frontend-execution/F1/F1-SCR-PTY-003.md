<!-- source: PHASE:F1 / SUB:F1-SCR-PTY-003 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-056, AC-PTY-077, AC-PTY-087, AC-PTY-088, AC-PTY-096, AC-PTY-122, AC-PTY-123, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-030, API-PTY-031, REQ-PTY-048, REQ-PTY-064, REQ-PTY-065, REQ-PTY-066, REQ-PTY-067, REQ-PTY-068, REQ-PTY-069, REQ-PTY-070, REQ-PTY-072, REQ-PTY-073, REQ-PTY-077, REQ-PTY-078, REQ-PTY-080, REQ-PTY-085, REQ-PTY-106, REQ-PTY-108, REQ-PTY-109, SCR-PTY-003 -->
<!-- SUB:F1-SCR-PTY-003:START traces=REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-106,REQ-PTY-108,REQ-PTY-109,AC-PTY-056,AC-PTY-077,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-122,AC-PTY-123,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-030,API-PTY-031,SCR-PTY-003 -->
### F1 · SCR-PTY-003 — Customer credit

| Type | Kind | Source |
|---|---|---|
| `CustomerCredit`, `CustomerCreditRow`, `CustomerCreditUpdateRequest`, `CreditHistoryEntry` | generated | API-PTY-016, 017, 018, 030, 031 |
| `CreditSearchFilter` | frontend | the query parameters of API-PTY-030 (`code`, `name`, `customerGroupCode`, `carriesCredit`, `page`, `size`, `sort`) |
| `CreditHistoryFilter` | frontend | `{ customerId, page, size }` — API-PTY-031 |
| `CreditFormValues` | frontend | `CustomerCreditUpdateRequest` as form state (`creditLimit`, `paymentTermDays`; empty input = `null`, REQ-PTY-068) |
<!-- SUB:F1-SCR-PTY-003:END -->

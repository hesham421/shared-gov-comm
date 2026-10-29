<!-- source: PHASE:F1 / SUB:F1-SCR-PTY-002 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-011, AC-PTY-021, AC-PTY-029, AC-PTY-031, API-PTY-001, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-028, API-PTY-029, REQ-PTY-006, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-016, REQ-PTY-017, REQ-PTY-018, REQ-PTY-020, REQ-PTY-021, REQ-PTY-022, REQ-PTY-023, REQ-PTY-024, REQ-PTY-026, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, SCR-PTY-002 -->
<!-- SUB:F1-SCR-PTY-002:START traces=REQ-PTY-006,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,AC-PTY-011,AC-PTY-021,AC-PTY-029,AC-PTY-031,API-PTY-001,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-028,API-PTY-029,SCR-PTY-002 -->
### F1 · SCR-PTY-002 — Suppliers

| Type | Kind | Source |
|---|---|---|
| `Supplier`, `SupplierRow`, `SupplierCreateRequest`, `SupplierUpdateRequest`, `ActivationRequest`, `Confirmation` | generated | API-PTY-006, 007, 008, 009, 010, 028 |
| `Customer`, `CustomerCreateRequest` | generated | API-PTY-001 (Make customer sends `{partyId}` only) |
| `Address`, `AddressRequest`, `DefaultAddressRequest` | generated | API-PTY-011, 013, 014, 015, 029 |
| `SupplierSearchFilter` | frontend | the query parameters of API-PTY-028 (`code`, `name`, `mobile`, `email`, `taxRegistrationNo`, `isActive`, `page`, `size`, `sort`) |
| `EmailHolderLookup` | frontend | `{ email }` — the RULE-PTY-002 action's input; reads API-PTY-026 then API-PTY-028 and keeps the row whose normalised email equals it (ADR-PTY-043); shared with SCR-PTY-001 |
| `SupplierFormValues` | frontend | the editable subset of `SupplierUpdateRequest` (no `code`) + `addresses: AddressDraft[]` |
| `AddressDraft`, `QuickRegisterResult` | frontend | shared with SCR-PTY-001 (`QuickRegisterResult.id` = `Supplier.supplierPk`) |
<!-- SUB:F1-SCR-PTY-002:END -->

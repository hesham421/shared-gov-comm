<!-- source: PHASE:F3 / SUB:F3-SCR-PTY-002 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-011, AC-PTY-021, AC-PTY-029, AC-PTY-031, API-PTY-001, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-028, API-PTY-029, REQ-PTY-006, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-016, REQ-PTY-017, REQ-PTY-018, REQ-PTY-020, REQ-PTY-021, REQ-PTY-022, REQ-PTY-023, REQ-PTY-024, REQ-PTY-026, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, SCR-PTY-002 -->
<!-- SUB:F3-SCR-PTY-002:START traces=REQ-PTY-006,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,AC-PTY-011,AC-PTY-021,AC-PTY-029,AC-PTY-031,API-PTY-001,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-028,API-PTY-029,SCR-PTY-002 -->
### F3 · SCR-PTY-002 — Suppliers

| Form | Fields and validators | Mandatory marks | Submits to |
|---|---|---|---|
| `SupplierEntryForm` | `partyNames`; `mobile`; `email`; `paymentTermDays` (shown whatever the state of `b2bCredit`, ADR-PTY-012); `taxRegistrationNo` optional (Advanced); `addresses: AddressDraft[]` | 1 (name group — SRS B3, AC-PTY-021) | SUPPLIER-SAVE |
| `AddressLineForm` (drawer) | shared with SCR-PTY-001 | 2 | returns the line — no call |
| `SupplierQuickRegisterForm` | `partyNames`; `mobile`; `email` | 1 (name group) | SUPPLIER-QUICK-REGISTER |
| `SupplierSearchForm` | the `SupplierSearchFilter` fields; `isActive` yes / no / any | 0 | the route's search parameters (SUPPLIER-SEARCH-QUERY) |

A Warehouse user (VIEW only) gets every form control read-only and no Save. Dirty tracking and command disabling as SCR-PTY-001.
<!-- SUB:F3-SCR-PTY-002:END -->

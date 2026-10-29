<!-- source: PHASE:F3 / SUB:F3-SCR-PTY-001 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-007, AC-PTY-010, AC-PTY-013, AC-PTY-055, AC-PTY-057, AC-PTY-078, AC-PTY-082, API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-026, API-PTY-029, REQ-PTY-001, REQ-PTY-004, REQ-PTY-005, REQ-PTY-006, REQ-PTY-007, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-012, REQ-PTY-013, REQ-PTY-014, REQ-PTY-015, REQ-PTY-023, REQ-PTY-024, REQ-PTY-025, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-039, REQ-PTY-040, REQ-PTY-047, REQ-PTY-049, REQ-PTY-070, REQ-PTY-071, REQ-PTY-073, SCR-PTY-001 -->
<!-- SUB:F3-SCR-PTY-001:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-047,REQ-PTY-049,REQ-PTY-070,REQ-PTY-071,REQ-PTY-073,AC-PTY-007,AC-PTY-010,AC-PTY-013,AC-PTY-055,AC-PTY-057,AC-PTY-078,AC-PTY-082,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-026,API-PTY-029,SCR-PTY-001 -->
### F3 · SCR-PTY-001 — Customers

| Form | Fields and validators | Mandatory marks | Submits to |
|---|---|---|---|
| `CustomerEntryForm` | `partyNames`; `customerGroupCode` required, active CUSTOMER_GROUP code → RULE-PTY-018 (default RETAIL); `mobile`; `email`; `taxRegistrationNo` optional (Advanced); `addresses: AddressDraft[]` (each line passed `addressLine` when confirmed in its drawer) | 2 (name group, customer group — SRS B3) | CUSTOMER-SAVE |
| `AddressLineForm` (drawer) | `addressLine`; `markDefault` boolean | 2 (address type, line 1) | returns the line to `CustomerEntryForm` — no call |
| `CustomerQuickRegisterForm` | `partyNames`; `mobile`; `email` | 1 (name group — AC-PTY-007) | CUSTOMER-QUICK-REGISTER |
| `CustomerSearchForm` | the `CustomerSearchFilter` fields; `customerGroupCode` from CUSTOMER_GROUP (any value, active or not); `isActive` yes / no / any | 0 | the route's search parameters (CUSTOMER-SEARCH-QUERY) |

Dirty tracking: `formState.isDirty` of `CustomerEntryForm` covers the header and the address draft; the facade's commands (Deactivate, Reactivate, Make supplier) are disabled while it is true, and leaving the route while dirty asks «لديك تغييرات غير محفوظة. هل تريد المغادرة؟» / "You have unsaved changes. Leave anyway?". The read-only credit values (ADR-PTY-033) are not form fields.
<!-- SUB:F3-SCR-PTY-001:END -->

<!-- source: PHASE:F1 / SUB:F1-SCR-PTY-001 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-007, AC-PTY-010, AC-PTY-013, AC-PTY-055, AC-PTY-057, AC-PTY-078, AC-PTY-082, API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-026, API-PTY-029, REQ-PTY-001, REQ-PTY-004, REQ-PTY-005, REQ-PTY-006, REQ-PTY-007, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-012, REQ-PTY-013, REQ-PTY-014, REQ-PTY-015, REQ-PTY-023, REQ-PTY-024, REQ-PTY-025, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-039, REQ-PTY-040, REQ-PTY-047, REQ-PTY-049, REQ-PTY-070, REQ-PTY-071, REQ-PTY-073, SCR-PTY-001 -->
<!-- SUB:F1-SCR-PTY-001:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-047,REQ-PTY-049,REQ-PTY-070,REQ-PTY-071,REQ-PTY-073,AC-PTY-007,AC-PTY-010,AC-PTY-013,AC-PTY-055,AC-PTY-057,AC-PTY-078,AC-PTY-082,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-026,API-PTY-029,SCR-PTY-001 -->
### F1 · SCR-PTY-001 — Customers

| Type | Kind | Source |
|---|---|---|
| `Customer`, `CustomerRow`, `CustomerCreateRequest`, `CustomerUpdateRequest`, `ActivationRequest`, `Confirmation` | generated | API-PTY-001, 002, 003, 004, 005, 026 |
| `Supplier`, `SupplierCreateRequest` | generated | API-PTY-006 (Make supplier sends `{partyId}` only) |
| `Address`, `AddressRequest`, `DefaultAddressRequest` | generated | API-PTY-011, 013, 014, 015, 029 |
| `CustomerSearchFilter` | frontend | the query parameters of API-PTY-026 (`code`, `name`, `mobile`, `email`, `taxRegistrationNo`, `customerGroupCode`, `isActive`, `page`, `size`, `sort`) |
| `CustomerFormValues` | frontend | the editable subset of `CustomerUpdateRequest` (no `code` — RULE-PTY-007; no credit field — ADR-PTY-033) + `addresses: AddressDraft[]` |
| `AddressDraft` | frontend | `AddressRequest` + `isDefaultFl` + `key` (addressPk, or a client key for a new line) + `op: 'unchanged' \| 'new' \| 'changed' \| 'removed'` + `markDefault: boolean` |
| `QuickRegisterResult` | frontend | `{ id: Customer.customerPk; code: Customer.code }` — what the in-place registration returns to the host (REQ-PTY-008) |
| `CustomerCreditView` | frontend | `Pick<Customer, 'creditLimit' \| 'paymentTermDays' \| 'currencyCode'>` — read-only display only, rendered when the flag is on and the fields are present (REQ-PTY-071, ADR-PTY-033); `currencyCode` formats the amount and is not shown while the tenant has one currency (ADR-PTY-037) |
| `EmailHolderLookup` | frontend | `{ email }` — the RULE-PTY-002 action's input (ADR-PTY-043) |
<!-- SUB:F1-SCR-PTY-001:END -->

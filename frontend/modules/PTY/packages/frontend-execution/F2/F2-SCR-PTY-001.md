<!-- source: PHASE:F2 / SUB:F2-SCR-PTY-001 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-007, AC-PTY-010, AC-PTY-013, AC-PTY-018, AC-PTY-036, AC-PTY-055, AC-PTY-057, AC-PTY-078, AC-PTY-082, API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-026, API-PTY-029, API-PTY-038, REQ-PTY-001, REQ-PTY-004, REQ-PTY-005, REQ-PTY-006, REQ-PTY-007, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-012, REQ-PTY-013, REQ-PTY-014, REQ-PTY-015, REQ-PTY-023, REQ-PTY-024, REQ-PTY-025, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-039, REQ-PTY-040, REQ-PTY-047, REQ-PTY-049, REQ-PTY-070, REQ-PTY-071, REQ-PTY-073, REQ-PTY-110, SCR-PTY-001 -->
<!-- SUB:F2-SCR-PTY-001:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-047,REQ-PTY-049,REQ-PTY-070,REQ-PTY-071,REQ-PTY-073,REQ-PTY-110,AC-PTY-007,AC-PTY-010,AC-PTY-013,AC-PTY-018,AC-PTY-036,AC-PTY-055,AC-PTY-057,AC-PTY-078,AC-PTY-082,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-026,API-PTY-029,API-PTY-038,SCR-PTY-001 -->
### F2 · SCR-PTY-001 — Customers

```yaml name=screen-hooks
screen: SCR-PTY-001
hooks:
  - hook: CUSTOMER-SEARCH-QUERY
    kind: read
    api: [API-PTY-026]
    cache_key: "[pty, customers, search, {code, name, mobile, email, taxRegistrationNo, customerGroupCode, isActive, page, size, sort}]"
    errors: "STD"
    loading: LOCAL
    invalidation: "—"
  - hook: CUSTOMER-QUERY
    kind: read
    api: [API-PTY-002]
    cache_key: "[pty, customers, customerId]"
    errors: "STD; PTY-404-CUSTOMER-NOT-FOUND → not-found state, text: catalogue PTY-404-CUSTOMER-NOT-FOUND"
    loading: LOCAL
    invalidation: "—"
  - hook: PARTY-ADDRESSES-QUERY
    kind: read
    api: [API-PTY-029]
    cache_key: "[pty, parties, partyFk, addresses]"
    errors: "STD; PTY-404-PARTY-NOT-FOUND → not-found state, text: catalogue PTY-404-PARTY-NOT-FOUND"
    loading: LOCAL
    invalidation: "—"
  - hook: EMAIL-HOLDER-QUERY
    kind: read
    api: [API-PTY-026, API-PTY-028]
    cache_key: "[pty, customers, search, {email, isActive}] then [pty, suppliers, search, {email, isActive}]"
    errors: "STD; a failure leaves the RULE-PTY-002 refusal shown without navigation"
    loading: LOCAL
    invalidation: "—"
  - hook: CUSTOMER_GROUP-LOOKUP
    kind: lookup
    api: [API-PTY-038]
    cache_key: "[pty, lookups, CUSTOMER_GROUP]"
    loading: LOCAL
  - hook: ADDRESS_TYPE-LOOKUP
    kind: lookup
    api: [API-PTY-038]
    cache_key: "[pty, lookups, ADDRESS_TYPE]"
    loading: LOCAL
  - hook: CUSTOMER-SCREEN-INIT
    kind: init
    cache_key: "[app-config] + [auth, permissions]"
    loading: LOCAL
  - hook: CUSTOMER-SAVE
    kind: mutation
    api: [API-PTY-001, API-PTY-003, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015]
    errors: "STD; PTY-422-NAME-REQUIRED → inline on the name group, text: RULE-PTY-001 message (SRS); PTY-409-EMAIL-TAKEN → inline on email, text: RULE-PTY-002 message (SRS), with the action open that party (EMAIL-HOLDER-QUERY, ADR-PTY-043); PTY-422-EMAIL-FORMAT → inline on email, text: RULE-PTY-003 message (SRS); PTY-422-MOBILE-FORMAT → inline on mobile, text: RULE-PTY-004 message (SRS); PTY-422-CUSTOMER-GROUP-INVALID → inline on customerGroupCode, text: RULE-PTY-018 message (SRS); PTY-422-CODE-READ-ONLY → form banner, text: RULE-PTY-007 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → not-found state; address codes as ADDRESSES-SYNC, PTY-422-ROLE-INACTIVE → form banner, text: RULE-PTY-005 message (SRS)"
    loading: LOCAL
    invalidation: "[pty, customers] · [pty, suppliers] · [pty, customer-credits] · [pty, parties, partyFk, addresses]"
  - hook: CUSTOMER-QUICK-REGISTER
    kind: mutation
    api: [API-PTY-001]
    errors: "STD; PTY-422-NAME-REQUIRED → inline on the name group, text: RULE-PTY-001 message (SRS); PTY-409-EMAIL-TAKEN → inline on email, text: RULE-PTY-002 message (SRS); PTY-422-EMAIL-FORMAT → inline on email, text: RULE-PTY-003 message (SRS); PTY-422-MOBILE-FORMAT → inline on mobile, text: RULE-PTY-004 message (SRS)"
    loading: LOCAL
    invalidation: "[pty, customers, search]"
  - hook: CUSTOMER-DEACTIVATE
    kind: mutation
    api: [API-PTY-004]
    errors: "STD; PTY-422-WALK-IN-DEACTIVATE → banner on the record, text: RULE-PTY-010 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, customers] · [pty, customer-credits]"
  - hook: CUSTOMER-REACTIVATE
    kind: mutation
    api: [API-PTY-005]
    errors: "STD; PTY-409-EMAIL-TAKEN → banner on the record, text: RULE-PTY-002 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, customers] · [pty, customer-credits]"
  - hook: CUSTOMER-MAKE-SUPPLIER
    kind: mutation
    api: [API-PTY-006]
    errors: "STD; PTY-409-ROLE-HELD → banner on the record, text: RULE-PTY-009 message (SRS); PTY-422-WALK-IN-SUPPLIER → banner on the record, text: RULE-PTY-012 message (SRS); PTY-404-PARTY-NOT-FOUND → banner, text: catalogue PTY-404-PARTY-NOT-FOUND"
    loading: LOCAL
    invalidation: "[pty, suppliers]"
  - hook: CUSTOMER-FACADE
    kind: facade
```

#### SCR-PTY-001 · CUSTOMER-SEARCH-QUERY — API-PTY-026     traces=API-PTY-026,REQ-PTY-013,REQ-PTY-001
Kind read query. Cache key is the whole `CustomerSearchFilter` (every filter, page, size, sort). Default filter `{page: 0, size: 20}`; `isActive` unset lists active and inactive with the status column.

#### SCR-PTY-001 · CUSTOMER-QUERY — API-PTY-002     traces=API-PTY-002,REQ-PTY-012,REQ-PTY-071
Kind read query, `forNewDocument` not sent (false) — the back-office opens inactive Customers too. The credit fields are present only while the flag is on; the screen renders them read-only (ADR-PTY-033). Gives `partyFk` to PARTY-ADDRESSES-QUERY and ADDRESSES-SYNC.

#### SCR-PTY-001 · CUSTOMER-SAVE — API-PTY-001 · API-PTY-003 + ADDRESSES-SYNC     traces=API-PTY-001,API-PTY-003,REQ-PTY-004,REQ-PTY-012,REQ-PTY-030,REQ-PTY-031,REQ-PTY-033,REQ-PTY-035
Kind mutation, the screen's **one** Save. It owns ordered calls:
1. the record call — new: API-PTY-001 without `partyId` (body from the form; `customerGroupCode` sent as chosen); existing: API-PTY-003 without `code`, sent only when the header changed;
2. ADDRESSES-SYNC with the `partyFk` the record call returned (or the loaded one) — sent **only after** call 1 succeeded (or was skipped as unchanged), and **not sent at all** when the address set is unchanged.
A refusal of call 1 sends nothing else; the form and the address draft stay as typed. If call 2 fails after call 1 succeeded, the record is saved: a new Customer moves to `/pty/customers/:customerId` (replace) keeping the unsent lines in the draft, and the error is shown on the failing line. RULE-PTY-002 action: "Open that party" «فتح ذلك الطرف» runs EMAIL-HOLDER-QUERY and opens the Customer, or else the Supplier, that holds the entered email (ADR-PTY-043).

#### SCR-PTY-001 · CUSTOMER-QUICK-REGISTER — API-PTY-001     traces=API-PTY-001,REQ-PTY-005,REQ-PTY-008,AC-PTY-007,AC-PTY-010
Kind mutation used by the in-place Quick register only. Sends names, mobile, email; `customerGroupCode` omitted (server default RETAIL, REQ-PTY-007). On success returns `QuickRegisterResult {id, code}` to the host through its callback; the host's screen stays open.

#### SCR-PTY-001 · CUSTOMER-DEACTIVATE / CUSTOMER-REACTIVATE — API-PTY-004 · API-PTY-005     traces=API-PTY-004,API-PTY-005,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-028,REQ-PTY-029,REQ-PTY-047,AC-PTY-055
Kind mutation (commands). Deactivate shows the response's confirmation text (RULE-PTY-006) as a toast. Reactivate sends `{"isActiveFl": true}` (ADR-PTY-031).

#### SCR-PTY-001 · CUSTOMER-MAKE-SUPPLIER — API-PTY-006     traces=API-PTY-006,REQ-PTY-025,REQ-PTY-027,REQ-PTY-049,AC-PTY-057
Kind mutation (command). Body `{partyId: partyFk}` only — no Party field is asked (REQ-PTY-025). On success navigates to `/pty/suppliers/:supplierPk` of the response.

#### SCR-PTY-001 · CUSTOMER-SCREEN-INIT and CUSTOMER-FACADE
INIT: permissions PERM_PTY_CUSTOMER_VIEW / _CREATE / _UPDATE / _DELETE, PERM_PTY_SUPPLIER_CREATE (Make supplier), PERM_PTY_SUPPLIER_VIEW (the Supplier step of EMAIL-HOLDER-QUERY), PERM_PTY_CUSTOMER_CREDIT_VIEW (Edit credit); `b2bCredit`; lookups CUSTOMER_GROUP, ADDRESS_TYPE; the Customer by id when the route carries `:customerId`.
FACADE: owns the search filter (from the route), the selected Customer, the address draft (`AddressDraft[]` built from PARTY-ADDRESSES-QUERY), `isDirty`, derived `isBusy`; exposes `search(filter)`, `save()`, `deactivate()`, `reactivate()`, `makeSupplier()`, `quickRegister(values)`, `addLine/updateLine/removeLine/markDefault` (draft only). Commands are rejected by the facade while `isDirty` (ADR-PTY-034).
<!-- SUB:F2-SCR-PTY-001:END -->

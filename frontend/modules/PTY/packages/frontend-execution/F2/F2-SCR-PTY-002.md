<!-- source: PHASE:F2 / SUB:F2-SCR-PTY-002 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-011, AC-PTY-013, AC-PTY-021, AC-PTY-029, AC-PTY-031, API-PTY-001, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-028, API-PTY-029, API-PTY-038, REQ-PTY-006, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-016, REQ-PTY-017, REQ-PTY-018, REQ-PTY-020, REQ-PTY-021, REQ-PTY-022, REQ-PTY-023, REQ-PTY-024, REQ-PTY-026, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-110, SCR-PTY-002 -->
<!-- SUB:F2-SCR-PTY-002:START traces=REQ-PTY-006,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-110,AC-PTY-011,AC-PTY-013,AC-PTY-021,AC-PTY-029,AC-PTY-031,API-PTY-001,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-028,API-PTY-029,API-PTY-038,SCR-PTY-002 -->
### F2 · SCR-PTY-002 — Suppliers

```yaml name=screen-hooks
screen: SCR-PTY-002
hooks:
  - hook: SUPPLIER-SEARCH-QUERY
    kind: read
    api: [API-PTY-028]
    cache_key: "[pty, suppliers, search, {code, name, mobile, email, taxRegistrationNo, isActive, page, size, sort}]"
    errors: "STD"
    loading: LOCAL
    invalidation: "—"
  - hook: SUPPLIER-QUERY
    kind: read
    api: [API-PTY-007]
    cache_key: "[pty, suppliers, supplierId]"
    errors: "STD; PTY-404-SUPPLIER-NOT-FOUND → not-found state, text: catalogue PTY-404-SUPPLIER-NOT-FOUND"
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
  - hook: ADDRESS_TYPE-LOOKUP
    kind: lookup
    api: [API-PTY-038]
    cache_key: "[pty, lookups, ADDRESS_TYPE]"
    loading: LOCAL
  - hook: SUPPLIER-SCREEN-INIT
    kind: init
    cache_key: "[app-config] + [auth, permissions]"
    loading: LOCAL
  - hook: SUPPLIER-SAVE
    kind: mutation
    api: [API-PTY-006, API-PTY-008, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015]
    errors: "STD; PTY-422-NAME-REQUIRED → inline on the name group, text: RULE-PTY-001 message (SRS); PTY-409-EMAIL-TAKEN → inline on email, text: RULE-PTY-002 message (SRS), with the action open that party (EMAIL-HOLDER-QUERY, ADR-PTY-043); PTY-422-EMAIL-FORMAT → inline on email, text: RULE-PTY-003 message (SRS); PTY-422-MOBILE-FORMAT → inline on mobile, text: RULE-PTY-004 message (SRS); PTY-422-PAYMENT-TERMS-RANGE → inline on paymentTermDays, text: RULE-PTY-008 message (SRS); PTY-422-CODE-READ-ONLY → form banner, text: RULE-PTY-007 message (SRS); PTY-404-SUPPLIER-NOT-FOUND → not-found state; address codes as ADDRESSES-SYNC, PTY-422-ROLE-INACTIVE → form banner, text: RULE-PTY-005 message (SRS)"
    loading: LOCAL
    invalidation: "[pty, suppliers] · [pty, customers] · [pty, customer-credits] · [pty, parties, partyFk, addresses]"
  - hook: SUPPLIER-QUICK-REGISTER
    kind: mutation
    api: [API-PTY-006]
    errors: "STD; PTY-422-NAME-REQUIRED → inline on the name group, text: RULE-PTY-001 message (SRS); PTY-409-EMAIL-TAKEN → inline on email, text: RULE-PTY-002 message (SRS); PTY-422-EMAIL-FORMAT → inline on email, text: RULE-PTY-003 message (SRS); PTY-422-MOBILE-FORMAT → inline on mobile, text: RULE-PTY-004 message (SRS)"
    loading: LOCAL
    invalidation: "[pty, suppliers, search]"
  - hook: SUPPLIER-DEACTIVATE
    kind: mutation
    api: [API-PTY-009]
    errors: "STD; PTY-404-SUPPLIER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, suppliers]"
  - hook: SUPPLIER-REACTIVATE
    kind: mutation
    api: [API-PTY-010]
    errors: "STD; PTY-409-EMAIL-TAKEN → banner on the record, text: RULE-PTY-002 message (SRS); PTY-404-SUPPLIER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, suppliers]"
  - hook: SUPPLIER-MAKE-CUSTOMER
    kind: mutation
    api: [API-PTY-001]
    errors: "STD; PTY-409-ROLE-HELD → banner on the record, text: RULE-PTY-009 message (SRS); PTY-404-PARTY-NOT-FOUND → banner, text: catalogue PTY-404-PARTY-NOT-FOUND"
    loading: LOCAL
    invalidation: "[pty, customers]"
  - hook: SUPPLIER-FACADE
    kind: facade
```

#### SCR-PTY-002 · SUPPLIER-SEARCH-QUERY — API-PTY-028     traces=API-PTY-028,REQ-PTY-018
Kind read query; key = the whole `SupplierSearchFilter`; default `{page: 0, size: 20}`. The `email` filter (ADR-PTY-043) is also read by EMAIL-HOLDER-QUERY.

#### SCR-PTY-002 · SUPPLIER-QUERY — API-PTY-007     traces=API-PTY-007,REQ-PTY-017,REQ-PTY-024
Kind read query, `forNewDocument` not sent. Gives `partyFk` to the address hooks.

#### SCR-PTY-002 · SUPPLIER-SAVE — API-PTY-006 · API-PTY-008 + ADDRESSES-SYNC     traces=API-PTY-006,API-PTY-008,REQ-PTY-016,REQ-PTY-017,REQ-PTY-022,REQ-PTY-030,REQ-PTY-031,REQ-PTY-035
Kind mutation, the screen's one Save; same ordering as CUSTOMER-SAVE: record call (API-PTY-006 new without `partyId` / API-PTY-008 existing without `code`, only when the header changed) → ADDRESSES-SYNC only after it succeeded and only when the address set changed. Invalidates the Customer keys too: a Party holding both roles shows the change on both screens (REQ-PTY-024, AC-PTY-029).

#### SCR-PTY-002 · SUPPLIER-QUICK-REGISTER — API-PTY-006     traces=API-PTY-006,REQ-PTY-008,REQ-PTY-016,AC-PTY-011,AC-PTY-021
Kind mutation for the in-place Quick register from a purchase document; returns `{id: supplierPk, code}` to the host.

#### SCR-PTY-002 · SUPPLIER-DEACTIVATE / SUPPLIER-REACTIVATE — API-PTY-009 · API-PTY-010     traces=API-PTY-009,API-PTY-010,REQ-PTY-020,REQ-PTY-021,REQ-PTY-023,REQ-PTY-028,REQ-PTY-029
Kind mutation (commands). Deactivation toast = the response's RULE-PTY-006 text; reactivation sends `{"isActiveFl": true}`.

#### SCR-PTY-002 · SUPPLIER-MAKE-CUSTOMER — API-PTY-001     traces=API-PTY-001,REQ-PTY-026,REQ-PTY-027,AC-PTY-031
Kind mutation (command). Body `{partyId: partyFk}` only; the server sets customer group RETAIL. On success navigates to `/pty/customers/:customerPk`.

#### SCR-PTY-002 · SUPPLIER-SCREEN-INIT and SUPPLIER-FACADE
INIT: PERM_PTY_SUPPLIER_VIEW / _CREATE / _UPDATE / _DELETE, PERM_PTY_CUSTOMER_CREATE (Make customer), PERM_PTY_CUSTOMER_VIEW (the Customer step of EMAIL-HOLDER-QUERY); lookup ADDRESS_TYPE; the Supplier by id when the route carries `:supplierId`. FACADE: as CUSTOMER-FACADE for Suppliers (filter, selection, address draft, `isDirty`, commands rejected while dirty).
<!-- SUB:F2-SCR-PTY-002:END -->

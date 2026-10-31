<!-- source: PHASE:F2 / SUB:F2-SCR-INV-001 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-004, AC-INV-007, AC-INV-008, AC-INV-011, API-INV-001, API-INV-002, API-INV-003, API-INV-004, API-INV-005, API-INV-006, API-INV-007, API-INV-008, API-INV-009, REQ-INV-003, REQ-INV-004, REQ-INV-005, REQ-INV-006, REQ-INV-007, REQ-INV-008, REQ-INV-009, REQ-INV-015, REQ-INV-029, REQ-INV-030, REQ-INV-031, SCR-INV-001 -->
<!-- SUB:F2-SCR-INV-001:START traces=SCR-INV-001,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-015,REQ-INV-029,REQ-INV-030,REQ-INV-031,AC-INV-004,AC-INV-007,AC-INV-008,AC-INV-011,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009 -->
### F2 · SCR-INV-001 — Warehouses

```yaml name=screen-hooks
screen: SCR-INV-001
hooks:
  - {hook: WAREHOUSES-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: WAREHOUSES-QUERY, kind: read, api: [API-INV-001], cache_key: "['inv','warehouses',filter{code,name,branchPk,active,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: WAREHOUSE-QUERY, kind: read, api: [API-INV-002], cache_key: "['inv','warehouse',warehousePk]", errors: "INV-404-WAREHOUSE-NOT-FOUND → message, drawer closes", loading: LOCAL, invalidation: "—"}
  - {hook: BRANCHES-LOOKUP, kind: lookup, api: [API-INV-009], cache_key: "['inv','lookup','branches',{active}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: WAREHOUSE-CREATE, kind: mutation, api: [API-INV-003], errors: "NAME-TAKEN → inline nameAr; BRANCH-INACTIVE / BRANCH-NOT-FOUND → inline branchPk", invalidation: "['inv','warehouses'], ['inv','lookup','warehouses'], ['inv','context']"}
  - {hook: WAREHOUSE-UPDATE, kind: mutation, api: [API-INV-004], errors: "NAME-TAKEN → inline nameAr; BRANCH-INACTIVE → inline branchPk; STALE-VERSION → reload", invalidation: "['inv','warehouses'], ['inv','warehouse',id], ['inv','lookup','warehouses']"}
  - {hook: WAREHOUSE-DEACTIVATE, kind: mutation, api: [API-INV-005], errors: "WAREHOUSE-DEFAULT / WAREHOUSE-IN-USE → message", invalidation: "['inv','warehouses'], ['inv','warehouse',id], ['inv','lookup','warehouses'], ['inv','context']"}
  - {hook: WAREHOUSE-REACTIVATE, kind: mutation, api: [API-INV-006], errors: "BRANCH-INACTIVE → message; STALE-VERSION → reload", invalidation: "['inv','warehouses'], ['inv','warehouse',id], ['inv','lookup','warehouses'], ['inv','context']"}
  - {hook: WAREHOUSE-SET-DEFAULT, kind: mutation, api: [API-INV-007], errors: "WAREHOUSE-DEFAULT-INACTIVE → message; STALE-VERSION → reload", invalidation: "['inv','warehouses'], ['inv','lookup','warehouses'], ['inv','context']"}
  - {hook: WAREHOUSES-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| WAREHOUSES-SCREEN-INIT | Permission read: VIEW / CREATE / UPDATE / DELETE of `INV_WAREHOUSES`. Context: `activeBranchCount`; while it is 1, the branch filter, column and field are hidden (REQ-INV-029, REQ-INV-030). Lookups: BRANCHES-LOOKUP when more than one branch is active. Entity by id: WAREHOUSE-QUERY when `entry=<id>` |
| WAREHOUSES-QUERY | read. Ordered by code on the server (REQ-INV-009). Key = the full filter |
| WAREHOUSE-QUERY | read, enabled only while the entry drawer is open on an id |
| WAREHOUSE-CREATE | mutation; body `{nameAr, nameEn, branchPk | null}`. INV-409-WAREHOUSE-NAME-TAKEN → inline on nameAr, text: RULE-INV-002 message (SRS) · INV-422-BRANCH-INACTIVE → inline on branchPk, text: RULE-INV-009 message (SRS) · INV-404-BRANCH-NOT-FOUND → inline on branchPk, text: catalogue · generic. On success the drawer closes and the search refetches |
| WAREHOUSE-UPDATE | mutation; body `{nameAr, nameEn, branchPk, version}`. `code` is never sent, and a hidden branch sends the record's `branchPk`. INV-409-WAREHOUSE-NAME-TAKEN → inline on nameAr, text: RULE-INV-002 message (SRS) · INV-422-BRANCH-INACTIVE → inline on branchPk, text: RULE-INV-009 message (SRS) · INV-422-WAREHOUSE-CODE-READONLY → message, text: RULE-INV-001 message (SRS) · INV-409-STALE-VERSION → reload · generic |
| WAREHOUSE-DEACTIVATE | mutation (DELETE, no body), after a confirmation. INV-409-WAREHOUSE-DEFAULT → message, text: RULE-INV-003 message (SRS), with the next action "Set as default" on another row · INV-409-WAREHOUSE-IN-USE → message, text: RULE-INV-004 message (SRS), with the next action "View balances", which opens SCR-INV-003 seeded with `warehousePk` |
| WAREHOUSE-REACTIVATE | mutation `{version}`. INV-422-BRANCH-INACTIVE → message, text: RULE-INV-009 message (SRS) · INV-409-STALE-VERSION → reload |
| WAREHOUSE-SET-DEFAULT | mutation `{version}` of the target. INV-409-WAREHOUSE-DEFAULT-INACTIVE → message, text: RULE-INV-006 message (SRS) · INV-409-STALE-VERSION → reload |
| WAREHOUSES-FACADE | Owns the list from query data, the filter `{code,name,branchPk,active,page,size}` mirrored to the URL, the entry navigation `entry`, the form's dirty state and derived loading. Operations: `save()` (create or update), `deactivate(id)`, `reactivate(id, version)`, `setDefault(id, version)`, `openEntry(id | 'new')`, `closeEntry()` |
<!-- SUB:F2-SCR-INV-001:END -->

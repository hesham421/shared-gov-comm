<!-- source: PHASE:F2 / SUB:F2-SCR-INV-002 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-026, AC-INV-029, AC-INV-030, API-INV-008, API-INV-009, API-INV-010, API-INV-011, API-INV-012, API-INV-013, API-INV-014, API-INV-015, REQ-INV-022, REQ-INV-023, REQ-INV-024, REQ-INV-025, REQ-INV-026, REQ-INV-027, REQ-INV-028, REQ-INV-032, SCR-INV-002 -->
<!-- SUB:F2-SCR-INV-002:START traces=SCR-INV-002,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-032,AC-INV-026,AC-INV-029,AC-INV-030,API-INV-008,API-INV-009,API-INV-010,API-INV-011,API-INV-012,API-INV-013,API-INV-014,API-INV-015 -->
### F2 · SCR-INV-002 — Branches

```yaml name=screen-hooks
screen: SCR-INV-002
hooks:
  - {hook: BRANCHES-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: BRANCHES-QUERY, kind: read, api: [API-INV-009], cache_key: "['inv','branches',filter{code,name,active,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: BRANCH-QUERY, kind: read, api: [API-INV-010], cache_key: "['inv','branch',branchPk]", errors: "INV-404-BRANCH-NOT-FOUND → message, drawer closes", loading: LOCAL, invalidation: "—"}
  - {hook: BRANCH-CREATE, kind: mutation, api: [API-INV-011], errors: "BRANCH-NAME-TAKEN → inline nameAr", invalidation: "['inv','branches'], ['inv','lookup','branches'], ['inv','context']"}
  - {hook: BRANCH-UPDATE, kind: mutation, api: [API-INV-012], errors: "BRANCH-NAME-TAKEN → inline nameAr; STALE-VERSION → reload", invalidation: "['inv','branches'], ['inv','branch',id], ['inv','lookup','branches']"}
  - {hook: BRANCH-DEACTIVATE, kind: mutation, api: [API-INV-013], errors: "BRANCH-DEFAULT / BRANCH-IN-USE → message", invalidation: "['inv','branches'], ['inv','branch',id], ['inv','lookup','branches'], ['inv','context']"}
  - {hook: BRANCH-REACTIVATE, kind: mutation, api: [API-INV-014], errors: "STALE-VERSION → reload", invalidation: "['inv','branches'], ['inv','branch',id], ['inv','lookup','branches'], ['inv','context']"}
  - {hook: BRANCH-SET-DEFAULT, kind: mutation, api: [API-INV-015], errors: "BRANCH-DEFAULT-INACTIVE → message; STALE-VERSION → reload", invalidation: "['inv','branches'], ['inv','lookup','branches']"}
  - {hook: BRANCHES-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| BRANCHES-SCREEN-INIT | Permission read: VIEW / CREATE / UPDATE / DELETE of `INV_BRANCHES`. Entity by id: BRANCH-QUERY when `entry=<id>` |
| BRANCHES-QUERY | read, ordered by code (REQ-INV-028) |
| BRANCH-CREATE | mutation `{nameAr, nameEn}`. INV-409-BRANCH-NAME-TAKEN → inline on nameAr, text: RULE-INV-011 message (SRS) · generic |
| BRANCH-UPDATE | mutation `{nameAr, nameEn, version}`; `code` is never sent. INV-409-BRANCH-NAME-TAKEN → inline on nameAr, text: RULE-INV-011 message (SRS) · INV-422-BRANCH-CODE-READONLY → message, text: RULE-INV-010 message (SRS) · INV-409-STALE-VERSION → reload |
| BRANCH-DEACTIVATE | mutation (DELETE), after a confirmation. INV-409-BRANCH-DEFAULT → message, text: RULE-INV-012 message (SRS) · INV-409-BRANCH-IN-USE → message, text: RULE-INV-013 message (SRS), with the next action "View warehouses", which opens SCR-INV-001 seeded with `branchPk` |
| BRANCH-REACTIVATE | mutation `{version}`. INV-409-STALE-VERSION → reload |
| BRANCH-SET-DEFAULT | mutation `{version}`. INV-409-BRANCH-DEFAULT-INACTIVE → message, text: RULE-INV-015 message (SRS) · INV-409-STALE-VERSION → reload |
| BRANCHES-FACADE | Owns the list, the filter `{code,name,active,page,size}`, the entry navigation `entry` and derived loading. Operations: `save()`, `deactivate`, `reactivate`, `setDefault`, `openEntry`, `closeEntry` |
<!-- SUB:F2-SCR-INV-002:END -->

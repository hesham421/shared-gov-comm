<!-- source: PHASE:F2 / SUB:F2-SCR-INV-011 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-132, AC-INV-133, API-INV-008, API-INV-048, API-INV-049, REQ-INV-065, REQ-INV-066, REQ-INV-118, REQ-INV-119, SCR-INV-011 -->
<!-- SUB:F2-SCR-INV-011:START traces=SCR-INV-011,REQ-INV-065,REQ-INV-066,REQ-INV-118,REQ-INV-119,AC-INV-132,AC-INV-133,API-INV-008,API-INV-048,API-INV-049 -->
### F2 · SCR-INV-011 — Inventory settings

```yaml name=screen-hooks
screen: SCR-INV-011
hooks:
  - {hook: SETTINGS-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: SETTINGS-QUERY, kind: read, api: [API-INV-048], cache_key: "['inv','settings']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: SETTINGS-SAVE, kind: mutation, api: [API-INV-049], errors: "HORIZON-RANGE → inline nearExpiryDays", invalidation: "['inv','settings'], ['inv','lots']"}
  - {hook: SETTINGS-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| SETTINGS-SCREEN-INIT | Permission read: `PERM_INV_SETTINGS_VIEW`, `_UPDATE`. Context: `lotExpiryFl`, which decides whether the horizon is shown |
| SETTINGS-QUERY | read. A tenant that never saved shows "allowed" and 30 days (REQ-INV-066, AC-INV-132) |
| SETTINGS-SAVE | mutation `{allowNegativeStockFl, nearExpiryDays}`. With lotExpiry off, the read horizon is sent unchanged. INV-422-HORIZON-RANGE → inline on nearExpiryDays, text: catalogue INV-422-HORIZON-RANGE «أدخل عدد أيام من 1 إلى 365.» / "Enter a number of days from 1 to 365." (AC-INV-133) · generic |
| SETTINGS-FACADE | Owns the form state seeded from SETTINGS-QUERY, `isDirty` and derived loading. Operation: `save()` |
<!-- SUB:F2-SCR-INV-011:END -->

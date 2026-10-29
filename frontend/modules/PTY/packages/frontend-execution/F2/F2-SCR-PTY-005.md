<!-- source: PHASE:F2 / SUB:F2-SCR-PTY-005 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-097, AC-PTY-098, AC-PTY-101, API-PTY-024, API-PTY-025, API-PTY-038, REQ-PTY-070, REQ-PTY-086, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-090, SCR-PTY-005 -->
<!-- SUB:F2-SCR-PTY-005:START traces=REQ-PTY-070,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,AC-PTY-097,AC-PTY-098,AC-PTY-101,API-PTY-024,API-PTY-025,API-PTY-038,SCR-PTY-005 -->
### F2 · SCR-PTY-005 — Credit control settings

```yaml name=screen-hooks
screen: SCR-PTY-005
hooks:
  - hook: SETTINGS-QUERY
    kind: read
    api: [API-PTY-024]
    cache_key: "[pty, credit-settings]"
    errors: "STD"
    loading: LOCAL
    invalidation: "—"
  - hook: CREDIT_OVERRUN_ACTION-LOOKUP
    kind: lookup
    api: [API-PTY-038]
    cache_key: "[pty, lookups, CREDIT_OVERRUN_ACTION]"
    loading: LOCAL
  - hook: SETTINGS-SCREEN-INIT
    kind: init
    cache_key: "[app-config] + [auth, permissions]"
    loading: LOCAL
  - hook: SETTINGS-SAVE
    kind: mutation
    api: [API-PTY-025]
    errors: "STD; PTY-403-FORBIDDEN → form banner, the shown setting is reloaded, text: catalogue PTY-403-FORBIDDEN"
    loading: LOCAL
    invalidation: "[pty, credit-settings]"
  - hook: SETTINGS-FACADE
    kind: facade
```

#### SCR-PTY-005 · SETTINGS-QUERY — API-PTY-024     traces=API-PTY-024,REQ-PTY-087,AC-PTY-098
Kind read query. `isDefault: true` → the «القيمة الافتراضية / Default value» note; the returned action (BLOCK_WITH_OVERRIDE when nothing is saved) is preselected.

#### SCR-PTY-005 · SETTINGS-SAVE — API-PTY-025     traces=API-PTY-025,REQ-PTY-086,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,AC-PTY-097,AC-PTY-101
Kind mutation, the one Save; body `{creditOverrunActionCode}` (`isDefault` is read-only, never sent).

#### SCR-PTY-005 · SETTINGS-SCREEN-INIT and SETTINGS-FACADE
INIT: `b2bCredit`; PERM_PTY_CREDIT_SETTINGS_VIEW / _UPDATE; lookup CREDIT_OVERRUN_ACTION. FACADE: the setting, `isDirty`, `save()`.
<!-- SUB:F2-SCR-PTY-005:END -->

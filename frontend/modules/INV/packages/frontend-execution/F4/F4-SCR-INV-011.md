<!-- source: PHASE:F4 / SUB:F4-SCR-INV-011 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-071, AC-INV-072, AC-INV-132, API-INV-008, API-INV-048, API-INV-049, REQ-INV-065, REQ-INV-066, REQ-INV-118, REQ-INV-119, SCR-INV-011 -->
<!-- SUB:F4-SCR-INV-011:START traces=SCR-INV-011,REQ-INV-065,REQ-INV-066,REQ-INV-118,REQ-INV-119,AC-INV-071,AC-INV-072,AC-INV-132,API-INV-008,API-INV-048,API-INV-049 -->
### SETTINGS-SCREEN — SCR-INV-011
- **Guard**: `PERM_INV_SETTINGS_VIEW`; Save needs `_UPDATE`.
- **Facade**: SETTINGS-FACADE.
- **Cross-module**: none.
- **Composition**: container page. Fields → none.
- **Saves**: ONE (Save → API-INV-049).
- **Components**: `InventorySettingsForm`, with an "Allow negative stock" switch and the "Near-expiry horizon (days)" number field, shown only with lotExpiry on.
<!-- SUB:F4-SCR-INV-011:END -->

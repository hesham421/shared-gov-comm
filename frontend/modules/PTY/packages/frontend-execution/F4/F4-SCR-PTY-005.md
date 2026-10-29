<!-- source: PHASE:F4 / SUB:F4-SCR-PTY-005 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-078, AC-PTY-097, AC-PTY-098, AC-PTY-101, API-PTY-024, API-PTY-025, REQ-PTY-070, REQ-PTY-086, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-090, SCR-PTY-005 -->
<!-- SUB:F4-SCR-PTY-005:START traces=REQ-PTY-070,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,AC-PTY-078,AC-PTY-097,AC-PTY-098,AC-PTY-101,API-PTY-024,API-PTY-025,SCR-PTY-005 -->
### SETTINGS-SCREEN — SCR-PTY-005            traces=REQ-PTY-086,REQ-PTY-087,REQ-PTY-090,API-PTY-024,API-PTY-025
Chunk        : `pty-credit-settings`
Routes       :
| Route | Element | Guard |
|---|---|---|
| `/pty/credit-settings` | `CreditSettingsPage` (read-only without UPDATE) | `b2bCredit` + PERM_PTY_CREDIT_SETTINGS_VIEW |
Guard        : flag off → not mounted; no VIEW → the unauthorized page
Facade       : SETTINGS-FACADE (F2)
Cross-module : none
Composition  : `container page`; one radio group, no secondary detail.
Saves        : ONE — `Save` runs SETTINGS-SAVE (one call).
Navigation   : none out; reached from the menu and from SCR-PTY-003.
<!-- SUB:F4-SCR-PTY-005:END -->

<!-- source: PHASE:F3 / SUB:F3-SCR-PTY-005 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-097, AC-PTY-098, AC-PTY-101, API-PTY-024, API-PTY-025, REQ-PTY-070, REQ-PTY-086, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-090, SCR-PTY-005 -->
<!-- SUB:F3-SCR-PTY-005:START traces=REQ-PTY-070,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,AC-PTY-097,AC-PTY-098,AC-PTY-101,API-PTY-024,API-PTY-025,SCR-PTY-005 -->
### F3 · SCR-PTY-005 — Credit control settings

| Form | Fields and validators | Mandatory marks | Submits to |
|---|---|---|---|
| `CreditSettingsForm` | `creditOverrunActionCode` required, one of the CREDIT_OVERRUN_ACTION codes (WARN, BLOCK_WITH_OVERRIDE), rendered as a two-option radio group | 1 (SRS B3) | SETTINGS-SAVE |

Without PERM_PTY_CREDIT_SETTINGS_UPDATE the radio group is read-only and there is no Save.
<!-- SUB:F3-SCR-PTY-005:END -->

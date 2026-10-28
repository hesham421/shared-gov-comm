# All other checklist lines are met

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T13:36:27+00:00

- C14: the capability flags are read through SettingsApi. The limits in D-10 are rules, not operator-changeable settings.
- F3: code literals such as BUNDLE, PUBLISHED, GTIN and SCALE belong to closed lookups (`open: false`), which users do not maintain.
- F5: ADR-CAT-042 justifies ENT-CAT-011 and ENT-CAT-012.
- F6: `MAX+1` is applied only to sortOrder, never to a user-visible number.
- F9: CORE-GAP files cover the three exceptions.

# Two checklist violations missed in round 1 are added

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: PTY        Version: v1
Written: 2026-10-29T07:49:00+00:00

- **G21 (C9):** document numbers must come from a `CORE_NUMBER_SERIES` row seeded in `V1000+`. The db-script's rule D-P2-07 writes no such row. ADR-PTY-029 registers the series at tenant provisioning instead.
- **G22 (C16):** the credit fields and the override decisions must be visible to a reviewer, but no entity is declared `@Audited`. Audit is hand-written through `AuditApi`.
- **C2 is folded into G7.** The plan never states that entities extend `AuditableEntity`.

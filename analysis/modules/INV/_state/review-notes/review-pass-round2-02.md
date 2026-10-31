# G2 — the plan assumes the core provides each tenant's time zone, and it does not

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T14:49:28+00:00

Confirmed against the skill. A search of `erp-core-usage@1.6.0` for "time zone", `ZoneId` and "clock" finds nothing for the tenant; the only hit is `ZoneId.systemDefault()` in a `module.md` example. Yet the plan's CORE section says "today" comes from the tenant context. The date rules (RULE-INV-038, 039, 051), FEFO selection and the near-expiry job all depend on it. The checklist's F9 line requires a `CORE-GAP-<topic>.md` instead of an unstated workaround. MAJOR, and the fix needs an ADR.

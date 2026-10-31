# G2 fix widened to C16

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T11:33:08+00:00

The plan only says "entity listeners annotated for the given audit". The fix now also asks for `@Audited(entityType = "<TABLE>")` on the seven audited entities, to match checklist C16.

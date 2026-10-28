# G4 stays MAJOR, with checklist C16 added. JPQL has no `RETURNING`. Bulk updates also bypass `@Audited` (C16) and the per-record events of REQ-CAT-008. Under the Item lock, the fix is to load and modify the entities.

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T14:14:22+00:00



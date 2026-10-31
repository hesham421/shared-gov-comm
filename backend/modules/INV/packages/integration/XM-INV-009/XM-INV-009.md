<!-- source: PHASE:INT-C / XM:XM-INV-009 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-106, REQ-INV-109 -->
<!-- XM:XM-INV-009:START traces=REQ-INV-106,REQ-INV-109 -->
### XM-INV-009 — Lot Variant foreign key on VARIANT_FK (DBF-INV-180)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_LOT ADD CONSTRAINT FK_INV_LOT_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_009_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_LOT l WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = l.VARIANT_FK)` = 0
tests      : AC-INV-119, AC-INV-122
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-009:END -->

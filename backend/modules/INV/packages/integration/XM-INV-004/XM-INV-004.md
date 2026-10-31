<!-- source: PHASE:INT-C / XM:XM-INV-004 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-043, REQ-INV-050 -->
<!-- XM:XM-INV-004:START traces=REQ-INV-043,REQ-INV-050 -->
### XM-INV-004 — Reservation Variant foreign key on VARIANT_FK (DBF-INV-076)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_RESERVATION ADD CONSTRAINT FK_INV_RESERVATION_CAT_VARIANT_1 FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_004_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_RESERVATION r WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = r.VARIANT_FK)` = 0
tests      : AC-INV-048, AC-INV-055
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-004:END -->

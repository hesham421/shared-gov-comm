<!-- source: PHASE:INT-C / XM:XM-INV-006 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-078, REQ-INV-080 -->
<!-- XM:XM-INV-006:START traces=REQ-INV-078,REQ-INV-080 -->
### XM-INV-006 — Transfer line Variant foreign key on VARIANT_FK (DBF-INV-106)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_TRANSFER_LINE ADD CONSTRAINT FK_INV_TRANSFER_LINE_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_006_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_TRANSFER_LINE t WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = t.VARIANT_FK)` = 0
tests      : AC-INV-085, AC-INV-087, AC-INV-088
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-006:END -->

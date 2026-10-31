<!-- source: PHASE:INT-C / XM:XM-INV-003 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-053 -->
<!-- XM:XM-INV-003:START traces=REQ-INV-053 -->
### XM-INV-003 — Stock move bundle Variant foreign key on BUNDLE_VARIANT_FK (DBF-INV-056)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT FK_INV_STOCK_MOVE_CAT_VARIANT_2 FOREIGN KEY (BUNDLE_VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);   -- the column is nullable
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_003_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_MOVE m WHERE m.BUNDLE_VARIANT_FK IS NOT NULL AND NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = m.BUNDLE_VARIANT_FK)` = 0
tests      : AC-INV-059
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-003:END -->

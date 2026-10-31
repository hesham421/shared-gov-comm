<!-- source: PHASE:INT-C / XM:XM-INV-002 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-051, REQ-INV-052, REQ-INV-075 -->
<!-- XM:XM-INV-002:START traces=REQ-INV-051,REQ-INV-052,REQ-INV-075 -->
### XM-INV-002 — Stock move Variant foreign key on VARIANT_FK (DBF-INV-053)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_MOVE ADD CONSTRAINT FK_INV_STOCK_MOVE_CAT_VARIANT_1 FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_002_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_MOVE m WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = m.VARIANT_FK)` = 0
tests      : AC-INV-056, AC-INV-057, AC-INV-058, AC-INV-082
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-002:END -->

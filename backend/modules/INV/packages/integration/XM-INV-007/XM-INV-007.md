<!-- source: PHASE:INT-C / XM:XM-INV-007 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-088, REQ-INV-092 -->
<!-- XM:XM-INV-007:START traces=REQ-INV-088,REQ-INV-092 -->
### XM-INV-007 — Stock count line Variant foreign key on VARIANT_FK (DBF-INV-135)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_COUNT_LINE ADD CONSTRAINT FK_INV_STOCK_COUNT_LINE_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_007_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_COUNT_LINE c WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = c.VARIANT_FK)` = 0
tests      : AC-INV-097, AC-INV-098, AC-INV-102
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-007:END -->

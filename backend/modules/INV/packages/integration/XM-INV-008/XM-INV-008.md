<!-- source: PHASE:INT-C / XM:XM-INV-008 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-070, REQ-INV-072, REQ-INV-097 -->
<!-- XM:XM-INV-008:START traces=REQ-INV-070,REQ-INV-072,REQ-INV-097 -->
### XM-INV-008 — Stock adjustment line Variant foreign key on VARIANT_FK (DBF-INV-167)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_ADJUSTMENT_LINE ADD CONSTRAINT FK_INV_STOCK_ADJUSTMENT_LINE_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_008_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_ADJUSTMENT_LINE a WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = a.VARIANT_FK)` = 0
tests      : AC-INV-076, AC-INV-079, AC-INV-107
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-008:END -->

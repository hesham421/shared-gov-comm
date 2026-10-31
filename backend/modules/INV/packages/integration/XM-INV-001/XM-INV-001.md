<!-- source: PHASE:INT-C / XM:XM-INV-001 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-035, REQ-INV-039, REQ-INV-040 -->
<!-- XM:XM-INV-001:START traces=REQ-INV-035,REQ-INV-039,REQ-INV-040 -->
### XM-INV-001 — Stock balance Variant foreign key on VARIANT_FK (DBF-INV-030)
target     : CAT · ENT-CAT-002
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-002
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_STOCK_BALANCE ADD CONSTRAINT FK_INV_STOCK_BALANCE_CAT_VARIANT FOREIGN KEY (VARIANT_FK) REFERENCES CAT_VARIANT (VARIANT_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_001_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_STOCK_BALANCE b WHERE NOT EXISTS (SELECT 1 FROM CAT_VARIANT v WHERE v.VARIANT_PK = b.VARIANT_FK)` = 0
tests      : AC-INV-040, AC-INV-044, AC-INV-045
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-001:END -->

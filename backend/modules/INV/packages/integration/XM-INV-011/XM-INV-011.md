<!-- source: PHASE:INT-C / XM:XM-INV-011 -->
<!-- context: INT-C-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INV-124, REQ-INV-125, REQ-INV-129 -->
<!-- XM:XM-INV-011:START traces=REQ-INV-124,REQ-INV-125,REQ-INV-129 -->
### XM-INV-011 — Item stock settings Item foreign key on ITEM_FK (DBF-INV-209)
target     : CAT · ENT-CAT-001
type       : HARD-FK
contract   : contract-cat.md#CON-CAT-001
requires   : CAT:DELIVERED
do         :
  ddl_patch : ALTER TABLE INV_ITEM_STOCK_SETTINGS ADD CONSTRAINT FK_INV_ITEM_STOCK_SETTINGS_CAT_ITEM FOREIGN KEY (ITEM_FK) REFERENCES CAT_ITEM (ITEM_PK);
  adapter   : none — a structural (foreign-key) edge; its access is the key itself
  config    : Flyway application migration `V<next free ≥ 1002>__inv_xm_inv_011_fk.sql` holding exactly the ddl_patch; pre-check `SELECT COUNT(*) FROM INV_ITEM_STOCK_SETTINGS s WHERE NOT EXISTS (SELECT 1 FROM CAT_ITEM i WHERE i.ITEM_PK = s.ITEM_FK)` = 0
tests      : AC-INV-138, AC-INV-140, AC-INV-145
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INV-011:END -->

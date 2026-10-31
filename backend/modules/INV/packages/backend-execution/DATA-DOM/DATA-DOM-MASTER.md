<!-- source: PHASE:DATA-DOM / SUB:DATA-DOM-MASTER -->
<!-- context: DATA-DOM-HEADER.md — phase-level preamble -->
<!-- traces: DBF-INV-001, DBF-INV-014, DBF-INV-176, REQ-INV-001, REQ-INV-003, REQ-INV-008, REQ-INV-020, REQ-INV-022, REQ-INV-027, REQ-INV-106, REQ-INV-108 -->
<!-- SUB:DATA-DOM-MASTER:START traces=REQ-INV-001,REQ-INV-003,REQ-INV-008,REQ-INV-020,REQ-INV-022,REQ-INV-027,REQ-INV-106,REQ-INV-108,DBF-INV-001,DBF-INV-014,DBF-INV-176 -->
### ENT-INV-001 — Warehouse / المستودع      kind: master
Honours        : CON-INV-001 (identifier BIGINT warehousePk; deactivated stays readable; one active default)
BINDINGS       : table INV_WAREHOUSE · PK DBF-INV-001 (PK_INV_WAREHOUSE) · identity · fields DBF-INV-001 … DBF-INV-013
BUSINESS CODE  : code DBF-INV-004 · `{PREFIX}-{SEQ:4}` prefix WH (e.g. WH-0002) · UQ_INV_WAREHOUSE_CODE · NumberSeriesApi series INV_WAREHOUSE on first save, read-only
DEFAULT FIELDS : master → nameAr DBF-INV-005, nameEn DBF-INV-006, code DBF-INV-004, isActiveFl DBF-INV-007, audit DBF-INV-010 … 013
DTO MEMBERSHIP : create nameAr, nameEn, branchPk (→ DBF-INV-003) · update + version · excluded warehousePk, code, isDefaultFl, isActiveFl, audit · response all
LOOKUP FIELDS  : none
CONSTRAINTS    : UQ_INV_WAREHOUSE_NAME_AR, UQ_INV_WAREHOUSE_TENANT_PK, UQ_INV_WAREHOUSE_IS_DEFAULT_FL (unique index), CHK_INV_WAREHOUSE_IS_DEFAULT_FL, CHK_INV_WAREHOUSE_NAME_AR, FK_INV_WAREHOUSE_INV_BRANCH
DOMAIN RULES   : RULE-INV-001 (domain — code set once, excluded from requests, change refused), 002 (domain: required, non-blank; app: uniqueness by UQ_INV_WAREHOUSE_NAME_AR + QR-INV-003), 003 (domain), 004 (app, QR-INV-007 — stock, ACTIVE reservations and DRAFT documents), 005 (effect: readable, excluded from active lists, master-change event), 006 (domain + UQ_INV_WAREHOUSE_IS_DEFAULT_FL + CHK_INV_WAREHOUSE_IS_DEFAULT_FL), 007 (app, every stock document on save and on post), 009 (app + FK_INV_WAREHOUSE_INV_BRANCH) — scope CREATE / UPDATE / DELETE as §1.3
STATE MACHINE  : none — isActiveFl flag (deactivate / reactivate); isDefaultFl moves in one transaction
CROSS-MODULE   : none
OPERATIONS     : VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS : QR-INV-001 … QR-INV-007

### ENT-INV-002 — Branch / الفرع      kind: master
Honours        : CON-INV-002 (identifier BIGINT branchPk; deactivated stays readable; one active default)
BINDINGS       : table INV_BRANCH · PK DBF-INV-014 (PK_INV_BRANCH) · identity · fields DBF-INV-014 … DBF-INV-025
BUSINESS CODE  : code DBF-INV-016 · `{PREFIX}-{SEQ:4}` prefix BR (e.g. BR-0002) · UQ_INV_BRANCH_CODE · NumberSeriesApi series INV_BRANCH, read-only
DEFAULT FIELDS : master → nameAr DBF-INV-017, nameEn DBF-INV-018, code DBF-INV-016, isActiveFl DBF-INV-019, audit DBF-INV-022 … 025
DTO MEMBERSHIP : create nameAr, nameEn · update + version · excluded branchPk, code, isDefaultFl, isActiveFl, audit · response all
LOOKUP FIELDS  : none
CONSTRAINTS    : UQ_INV_BRANCH_NAME_AR, UQ_INV_BRANCH_TENANT_PK, UQ_INV_BRANCH_IS_DEFAULT_FL (unique index), CHK_INV_BRANCH_IS_DEFAULT_FL, CHK_INV_BRANCH_NAME_AR
DOMAIN RULES   : RULE-INV-010 (app), 011 (UQ_INV_BRANCH_NAME_AR + QR-INV-010), 012 (app), 013 (app, QR-INV-014), 014 (effect), 015 (UQ_INV_BRANCH_IS_DEFAULT_FL + CHK_INV_BRANCH_IS_DEFAULT_FL + app)
STATE MACHINE  : none — isActiveFl flag
CROSS-MODULE   : none
OPERATIONS     : VIEW, CREATE, UPDATE, DELETE
REPOSITORY OPS : QR-INV-008 … QR-INV-014

### ENT-INV-012 — Lot / التشغيلة      kind: master
Honours        : CON-INV-003 (variantFk + code business key; one expiry per lot; created by INV only; expired never sold)
BINDINGS       : table INV_LOT · PK DBF-INV-176 (PK_INV_LOT) · identity · fields DBF-INV-176 … DBF-INV-190
BUSINESS CODE  : code DBF-INV-181 — the lot number written on the goods, entered on the incoming line, unique per Variant (UQ_INV_LOT_VAR_CODE), never generated
DEFAULT FIELDS : master → nameAr DBF-INV-182, nameEn DBF-INV-183 (optional, display code when empty — SRS D-02), code DBF-INV-181, isActiveFl DBF-INV-184, audit DBF-INV-187 … 190
DTO MEMBERSHIP : no create request (lots are created by incoming lines) · update (API-INV-042) expiryDate, nameAr, nameEn, version · response all + expiredFl, daysLeft (derived)
LOOKUP FIELDS  : none
CONSTRAINTS    : UQ_INV_LOT_VAR_CODE, UQ_INV_LOT_TENANT_PK, CHK_INV_LOT_CODE
DOMAIN RULES   : RULE-INV-035, 036 (UQ_INV_LOT_VAR_CODE + app), 038 (effect, derived expiredFl), 039 (in-process issue)
STATE MACHINE  : none — isActiveFl is recomputed after the commit of every move of the lot, in its own short transaction: false while the lot holds no stock in any warehouse (QR-INV-019 by lot), true otherwise (engine step 7)
CROSS-MODULE   : XM-INV-009
OPERATIONS     : VIEW, UPDATE
REPOSITORY OPS : QR-INV-038 … QR-INV-041
<!-- SUB:DATA-DOM-MASTER:END -->

<!-- source: PHASE:F1 / SUB:F1-SCR-INV-008 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-001, API-INV-008, API-INV-040, API-INV-041, API-INV-042, REQ-INV-108, REQ-INV-109, REQ-INV-114, REQ-INV-115, REQ-INV-116, REQ-INV-117, REQ-INV-134, SCR-INV-008, UXD-INV-008 -->
<!-- SUB:F1-SCR-INV-008:START traces=SCR-INV-008,UXD-INV-008,REQ-INV-108,REQ-INV-109,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-134,API-INV-001,API-INV-008,API-INV-040,API-INV-041,API-INV-042 -->
### F1 · SCR-INV-008 — Lots and near expiry
- Schemas: `LotRow`, `LotRowPageEnvelope`, `Lot`, `LotEnvelope`, `LotUpdateRequest`, `Warehouse` (labels).
- `LotFilter {variantPk?, code?, warehousePk?, expiryFrom?, expiryTo?, nearExpiryOnly, page, size}`.
- `LotFormState {expiryDate, nameAr, nameEn, version}` and `LotEntryNav = lotPk`.
<!-- SUB:F1-SCR-INV-008:END -->

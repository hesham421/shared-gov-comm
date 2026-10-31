<!-- source: PHASE:F3 / SUB:F3-SCR-INV-008 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-040, API-INV-042, REQ-INV-108, REQ-INV-109, REQ-INV-116, SCR-INV-008, UXD-INV-008 -->
<!-- SUB:F3-SCR-INV-008:START traces=SCR-INV-008,UXD-INV-008,REQ-INV-108,REQ-INV-109,REQ-INV-116,API-INV-040,API-INV-042 -->
### F3 · SCR-INV-008 — Lots and near expiry
- `lotSearchSchema`: `variantPk`, `code` ≤ 40, `warehousePk`, `expiryFrom` ≤ `expiryTo`, `nearExpiryOnly`.
- `lotFormSchema`:
  - `expiryDate`: required; empty → «أدخل تاريخ انتهاء الصلاحية.» / "Enter the expiry date.".
  - `nameAr`, `nameEn`: ≤ 100.
  - `version`: required.
- An earlier or later expiry is allowed (REQ-INV-108).
<!-- SUB:F3-SCR-INV-008:END -->

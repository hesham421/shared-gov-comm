# ADR-INV-033 — Reads a screen needs that are not operations of api-spec-inv.yaml
Status      : ACCEPTED
Stage       : P3.2 (Frontend — UX Design + Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : UXD-INV-001, UXD-INV-002, UXD-INV-003, UXD-INV-004, UXD-INV-005, UXD-INV-006, UXD-INV-007, UXD-INV-008, UXD-INV-009, UXD-INV-010, UXD-INV-011, REQ-INV-039, REQ-INV-075, REQ-INV-078, REQ-INV-088, REQ-INV-091, REQ-INV-097, REQ-INV-109, REQ-INV-121, REQ-INV-124, REQ-INV-125, REQ-INV-063, REQ-INV-138, API-INV-008, ADR-INV-024

## Context
The frontend plan binds every read of a screen to an operation of `api-spec-inv.yaml` (C9.24). Three kinds of
read that INV screens need are not in that document, and cannot be:

1. **Catalogue data owned by CAT.** A transfer, adjustment or count line carries `variantPk` only
   (`TransferLine`, `StockAdjustmentLine`, `StockCountLine`); a lot row carries `variantPk` only (`LotRow`);
   the balance row carries no Item. Choosing a Variant or an Item (pickers, barcode entry) and labelling a
   `variantPk` need CAT reads: search (CON-CAT-016), barcode lookup (CON-CAT-010), read Variant (CON-CAT-008),
   read Item (CON-CAT-007). The CAT unit-of-measure lookup labels `measureUomCode` (SRS A6, lookups consumed).
2. **Lookup values** of the INV_* keys (INV_ADJUSTMENT_REASON, INV_MOVE_TYPE, INV_DOC_STATUS,
   INV_SOURCE_DOC_TYPE). SRS A6: they are loaded at runtime from the given core lookup module `mdl`.
3. **The user's permissions.** The platform security module (`sec`, given) derives `PERM_<PAGE_CODE>_<ACTION>`
   and the session carries them. No INV operation returns them.

## Decision
- Each CAT read a screen needs is a **UXD** (UXD-INV-001 … 011), minted in the ui-ux-spec and cited by the F-blocks.
  The UXD names the owner module and the CAT contract (CON-CAT-xxx). It cites no CAT API id and no CAT path.
  The id and path are CAT's and resolve in CAT's own artifacts at build time. In the screen-hooks blocks
  these reads are `kind: lookup` hooks with no `api` binding.
- INV_* lookup values are read through one shared `kind: lookup` hook per key. The hook is served by the given
  `mdl` surface and has no `api` binding.
- Permissions are read from the authenticated session (given `sec`) by each screen's `SCREEN-INIT` hook. That hook
  is bound to API-INV-008 (active counts and capability flags) for the rest of its job.
- The frontend executor's mock server serves `api-spec-inv.yaml`. It also stubs the CAT reads, the `mdl` lookup
  values and the session permissions from fixtures. These fixtures are not part of the INV API document.

## Consequences
- No hook of kind `read` cites an operation outside `api-spec-inv.yaml`.
- A user without a CAT read grant gets no picker results. The picker shows the INV-403 catalogue message. INV does
  not widen CAT's grants.
- No REQ, RULE, field, permission or API changes.

## Status
ACCEPTED — non-breaking.

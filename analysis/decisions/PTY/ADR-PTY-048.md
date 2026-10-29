# ADR-PTY-048 — PTY serves its own lookup keys through OwnedLookups (API-PTY-038)

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise 2 (G3) · Module : PTY v1 · Date : 2026-10-29
Amends  : ADR-PTY-034 (the lookup hooks read PTY's own lookup operation, not the shell's mdl lookup client)

## Context
PTY owns five lookup keys, seeded with OWNER_MODULE_CODE `PTY` (db-script BLOCK 8a): CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT and CREDIT_OVERRIDE_STATUS. erp-core requires the owning module to serve its own keys:
- checklist C6: "The module serves only its own lookup keys through `OwnedLookups.read(...)`";
- mdl.md: "serves its own keys to the UI through `OwnedLookups` (never a pass-through for arbitrary keys)".

The backend plan stated no `OwnedLookups` use, and ADR-PTY-034 had the frontend read the keys through the shell's mdl lookup client — the pass-through the core forbids. CAT made the same fix in ADR-CAT-065.

## Decision
1. **New API-PTY-038, `GET /api/v1/pty/lookups/{key}`.** `PtyLookupService.read(key)` returns `com.erp.common.lookup.OwnedLookups.read(key, PTY_LOOKUP_KEYS, PtyErrorCodes.LOOKUP_KEY_UNKNOWN, mdlLookupApi::readActiveValuesByKey)`, mapped to `LookupOption {code, labelAr, labelEn, sortOrder}`. `PTY_LOOKUP_KEYS` holds the five keys of SRS A6, with owner PTY. No PTY table is read.
2. **Authority.** The controller and the service method carry `@PreAuthorize("hasAnyAuthority(T(com.commerce.pty.permission.PtyPermissions).PERM_PTY_CUSTOMER_VIEW, …_SUPPLIER_VIEW, …_CUSTOMER_CREDIT_VIEW, …_CREDIT_OVERRIDE_VIEW, …_CREDIT_SETTINGS_VIEW)")`. Every PTY screen reads one of the keys, and each constant is PTY's own (checklist C13).
3. **Unknown key.** A key outside `PTY_LOOKUP_KEYS`, or one mdl answers 404 for, is answered by the new PLATFORM-STD catalog row PTY-404-LOOKUP-KEY-UNKNOWN (ADR-PTY-024 conventions).
4. **Validation is unchanged.** Writes still validate the open-lookup codes against `MdlLookupApi.readActiveValuesByKey` (checklist C5, RULE-PTY-016, RULE-PTY-018).
5. **Given usage.** `OwnedLookups` is a class of `erp-core:modules.common`. That module publishes no extension point, interface or api (ADR-PTY-044), so `OwnedLookups` is not a published seam and gets no `given-usage` row of its own; C-GIVEN-2 accepts only a published `via`. The call goes through the mdl interface, so the existing row `modules.mdl` (`com.erp.mdl.crossmodule.MdlLookupApi`) names it in its purpose. The SRS A9, the contract Part 4 and the backend plan's given-usage table carry that wording. CAT filed the manifest gap as a platform finding (business-policies-cat.md, PLATFORM FINDINGS row 4), and PTY cites it (business-policies-pty.md, PLATFORM FINDINGS row 2).
6. **Frontend.** The five LOOKUP hooks of the frontend plan call API-PTY-038, kind `lookup`, cache key `["pty","lookups",<KEY>]`, with the long-lived lookup cache of ADR-PTY-034. Choosers show the active values API-PTY-038 returns. A stored code that is no longer active still renders, labelled from the code itself.

## Consequences
- API 37 → 38; catalog 37 → 39 rows (with PTY-422-OVERRIDE-NOT-REQUIRED of ADR-PTY-045); api-spec-pty.yaml gains one operation and the schema `LookupOption`.
- The permission matrix lists API-PTY-038 on every screen row; no permission name is added.
- A backend TC asserts the active values of CUSTOMER_GROUP and the refusal of a foreign key (TC-PTY-200).

## Traces
REQ-PTY-013, REQ-PTY-038, REQ-PTY-039, REQ-PTY-086 · AC-PTY-046 · API-PTY-038 · CON-PTY-006, CON-PTY-007, CON-PTY-008 · erp-core checklist C5, C6, C13 · ADR-PTY-024, ADR-PTY-034, ADR-PTY-044 · ADR-CAT-065

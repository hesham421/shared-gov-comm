# ADR-CAT-078 — The barcode label screen generates the missing internal barcode itself

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 / P3.2 / P4 (gate-analysis round 10, G1) · Module : CAT v2 · Date : 2026-10-28

## Context
- RULE-CAT-037 refuses a label request that lists a Variant with no active barcode, and its next action is «أنشئ لها باركودًا داخليًا ثم اطبع» / "Generate an internal barcode for them, then print".
- Warehouse staff hold PERM_CAT_BARCODE_LABELS_VIEW / CREATE and PERM_CAT_ITEMS_VIEW only (SRS B4). The one operation that generates an internal barcode, API-CAT-015, requires PERM_CAT_ITEMS_CREATE, which only the Owner holds. The «Open item» jump of SCR-CAT-005 opens the Item read-only for them, so the role that prints labels reaches a dead end (KB §6.5 — every error offers a next action the user can take).

Options:
- (a) add a «generate internal barcode» action on the flagged lines of SCR-CAT-005, served by a new operation guarded by PERM_CAT_BARCODE_LABELS_CREATE that reuses the service, the locks and the rules of API-CAT-015;
- (b) widen API-CAT-015 to `hasAnyAuthority(PERM_CAT_ITEMS_CREATE, PERM_CAT_BARCODE_LABELS_CREATE)`;
- (c) make the next action of RULE-CAT-037 depend on the role («ask the owner»).

## Decision
Option (a).
- Option (b) would let a label user add barcodes from the Items screen as well, mixing two pages' permissions on one endpoint and breaking the one-page-per-endpoint rule of the permission matrix.
- Option (c) keeps the dead end for the role that meets it most often.

1. **Operation.** API-CAT-056 `POST /api/v1/cat/barcode-labels/variants/{id}/internal-barcode` (REQ-CAT-148). Body: optional `uomCode`, as API-CAT-015; the screen sends none, so the barcode is bound to the Item's base unit.
2. **Permission.** `@PreAuthorize` on PERM_CAT_BARCODE_LABELS_CREATE (page CAT_BARCODE_LABELS — Owner, Warehouse staff). It is listed under SCR-REQ-CAT-005 CREATE in the permission matrix.
3. **Logic.** The controller calls the same `CatBarcodeService.generateInternal` body as API-CAT-015, through a second entry method carrying the label page's permission: request consistency, RULE-CAT-053, 006, 031, 005, 032, 029 (one retry on a clash), 049; the Item lock (QR-CAT-010) and the scale-prefix lock (QR-CAT-074). Its errors and its catalogue-change event (CREATED, CON-CAT-018) are those of API-CAT-015.
4. **Screen.** A line flagged `no-barcode` on SCR-CAT-005 offers «Generate internal barcode / إنشاء باركود داخلي». On success the line takes the new barcode and its flag is cleared, so Print can run. «Open item» stays for users who hold PERM_CAT_ITEMS_VIEW.
5. **RULE-CAT-037** is unchanged: its next action is now reachable by every role that can print.

## Consequences
- SRS: REQ-CAT-148, AC-CAT-171, SCR-REQ-CAT-005 B1 / B3 / B5, traceability matrix, counts (REQ 148 · AC 171).
- Backend plan: API registry, API-CAT-056 block (SVC-API-INT), RULE-CAT-053 trigger, permission matrix and SEC-BE, QR API lists (QR-CAT-010, 012, 044, 048, 049, 059, 074), error catalog `api` lists, RULE → API coverage, totals (API 56).
- api-spec: one operation, 34 → 35 paths.
- Frontend plan: api-surface binding, LABEL-INTERNAL-BARCODE hook, F3 / F4 / SEC-FE of SCR-CAT-005, ALIGN-FE coverage (54 of 56 used); ui-ux-spec and flow diagram of SCR-CAT-005.
- Tests: TC-CAT-231 (backend), TC-CAT-232 (frontend).

## Traces
REQ-CAT-148 · REQ-CAT-104 · REQ-CAT-084 · AC-CAT-171 · RULE-CAT-037 · RULE-CAT-005 · RULE-CAT-029 · RULE-CAT-031 · RULE-CAT-032 · RULE-CAT-053 · API-CAT-015 · API-CAT-056 · SCR-CAT-005 · TC-CAT-231 · TC-CAT-232

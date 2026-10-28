# ADR-CAT-051 — API coverage of the CAT screens and binding notes

Status : ACCEPTED (non-breaking) · Stage : P3.2 · Module : CAT v2 · Date : 2026-10-28

## Context
The frontend plan binds the six CAT screens to `api-spec-cat.yaml` (51 operations). Some operations serve readers other than CAT screens. A few shapes also leave the screen something to derive.

## Decision
1. **Operations no CAT screen calls (2 of 51).** `API-CAT-011` (read Item unit) and `API-CAT-046` (storefront catalogue read) serve consuming modules (INV, PUR, SLS, SFR) through the contract (CON-CAT-009, CON-CAT-014). The Items screen reads Item units inside `API-CAT-002`. Both stay in the document. Their REQs (REQ-CAT-079, REQ-CAT-122, REQ-CAT-115) are covered by other operations or by SCR-CAT-001's traces.
2. **Operations a CAT screen calls on behalf of the platform.** `API-CAT-047` (catalogue search) is exported by the Items screen module as the catalogue's contribution to the shell's global search (ADR-CAT-043). A hit opens `/cat/items/:itemPk` (SCR-REQ-CAT-001 "from: global search result"). `API-CAT-043` (barcode and scale-label lookup) backs the Items search "Find by barcode" field (SCR-REQ-CAT-001 B5 lists the lookup). Point-of-sale scanning belongs to SLS.
3. **Cross-screen reads that stay inside one page permission.** The Bundle component picker searches Items (`API-CAT-038`, active STANDARD only) and reads the chosen Item's Variants (`API-CAT-002`), not `API-CAT-042`. `API-CAT-042` needs PERM_CAT_BARCODE_LABELS_VIEW, which an Items editor may lack. Component lines are labelled by reading the Variant (`API-CAT-007`).
4. **Reads that may be refused for a custom role.** When a screen's own read succeeds but an auxiliary read returns CAT-403-FORBIDDEN, the affordance that read feeds is hidden, and the screen stays usable:
   - the Category tree (`API-CAT-039`): the Category field and filter are hidden, and the Category column shows the code;
   - the attribute list (`API-CAT-041`): "Generate variants" is hidden;
   - the catalogue settings (`API-CAT-045`): the SCALE barcode type is not offered;
   - the seeded Variant read on Barcode labels (`API-CAT-007`): the seed is dropped.
5. **Derived on the client from data it already holds.**
   - RULE-CAT-037 (`CAT-422-LABEL-VARIANT-NO-BARCODE`) returns only the localized error. The Barcode labels screen marks the lines whose `firstBarcodeValue` (VariantSearchRow) is empty and links each one to its Item.
   - The Items list shows the Category label from the cached Category tree (`categoryFk` → `CategoryNode.nameAr`).
6. **Request fields never sent by the UI.**
   - `code` (create and updates of Item and Variant), `itemKindCode` / `baseUomCode` on Item update, and `conversionFactor` on Item unit update. These fields are read-only in the UI, and the server rejects them under RULE-CAT-004 / 022 / 007 / 028.
   - `bundleComponents` is optional on `API-CAT-001` (default `[]`; RULE-CAT-018 applies only when `itemKindCode` is BUNDLE). The UI omits it for a STANDARD Item. (Amended at the gate-analysis revise, G16: the earlier "send `[]`" workaround is dropped.)
7. **Stories with no navigation of their own.** US-CAT-002 (changes reach every channel) and US-CAT-003 (behaviour follows flags) have no screen of their own. Their flows ride on SCR-CAT-001 (Save re-reads the Item: REQ-CAT-010; flags read on entry: REQ-CAT-012). No navigation is invented for them.

## Consequences
- The operations coverage table of the frontend plan marks `API-CAT-011` and `API-CAT-046` with ✗ and cites this ADR. Every other operation has a screen action and a route.
- No REQ is left without an SCR or F-block.

## Amended (gate-analysis round 6, G5)
The counts above were written against the first P3.1 document (51 operations, "2 of 51" unused). The API document now holds **55 operations**: the gate-analysis revises added API-CAT-052 (active values of a CAT lookup key — ADR-CAT-065) and API-CAT-053 … 055 (reactivation of Categories, Variant attributes and values — ADR-CAT-069). **53 of the 55 are used** by a CAT screen action and route. The unused operations are still exactly **API-CAT-011** (read Item unit) and **API-CAT-046** (storefront catalogue read), for the reasons of §1. The frontend plan's operations coverage table, its registry and `api-spec-cat.yaml` carry the same counts.

## Traces
REQ-CAT-079 · REQ-CAT-086 · REQ-CAT-104 · REQ-CAT-115 · REQ-CAT-122 · REQ-CAT-125 · REQ-CAT-056 · API-CAT-011 · API-CAT-046 · API-CAT-047 · API-CAT-043 · API-CAT-042 · API-CAT-007

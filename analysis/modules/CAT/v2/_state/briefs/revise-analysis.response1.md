All three findings are applied. I edited the files on disk (17 artifacts and ADRs, plus two new ADRs) instead of emitting `<<<FILE>>>` blocks, because whole copies of the backend plan and the SRS would be far too large. I didn't commit anything and I didn't run `gov.py analyze`. The api-spec still parses as valid YAML with 56 operations, and a sweep found no old wording left in any artifact.

**G1 — label barcode (ADR-CAT-079)**
- **Rule:** a label now uses only the Variant's earliest-created active GTIN or INTERNAL barcode whose unit is the Item's base unit.
- **RULE-CAT-037:** it counts only those barcodes. A Variant without one is flagged and gets the existing "Generate internal barcode" action (API-CAT-056).
- **Message change:** the RULE-CAT-037 message now says "no active barcode in the base unit" in both ar and en. It changed everywhere it appears: the SRS, the error catalog, the screen copy, and the backend and frontend tests that quote it.
- **SRS:** REQ-CAT-101, 102, 104 and 148 are reworded, and AC-CAT-103, 104 and 106 are updated.
- **Queries and API:**
  - QR-CAT-016 now returns the label barcode as `firstBarcodeValue`; its text search still matches any barcode.
  - QR-CAT-047 gets a label-only filter, used just by the print endpoint. The Item and Variant reads still return every barcode.
  - The api-spec now describes `firstBarcodeValue` as the label barcode.
- **Frontend:** a line seeded from a single Variant takes its barcode by type and base unit, and the flag on a line reads "No barcode in the base unit".
- **New ACs and TCs:**
  - AC-CAT-172 / TC-CAT-233: the only barcode is a SCALE code (and a second Variant has only a carton barcode). Both get no label until an internal barcode is generated.
  - AC-CAT-173 / TC-CAT-234: a Variant has an earlier carton GTIN and a later piece GTIN. The label prints the piece GTIN.

**G2 — Bundle component quantity unit (ADR-CAT-080)**
- **Rule:** the quantity is always in the base unit of the component Variant's Item.
- **Where it changed:**
  - ENT-CAT-007, REQ-CAT-056 and REQ-CAT-058 in the SRS.
  - CON-CAT-006 and CON-CAT-007 in the contract.
  - The column comment on the quantity in the db-script.
  - QR-CAT-039, which now joins the component Item.
- **New field:** `componentBaseUomCode` is returned on each Bundle line (DTO, derived-property list, api-spec) and shown in the Bundle components pane.
- **ADR-CAT-048:** it gets an amendment note, since this adds a derived property.
- **New AC and TC:** AC-CAT-174 / TC-CAT-235 check that each line returns its unit. TC-CAT-095 and frontend TC-CAT-179 are extended to check it too.

**G3 — stale counts**
- The frontend plan's header, its ALIGN-FE heading and the amended note in ADR-CAT-051 now say 56 operations, 54 used.

**Re-stamped counts:** the SRS and its registry now show AC 174 and 23 ADRs, with ADR-CAT-081 as the next free number. The contract, db-script, DB registry, both plan registries and both test plans carry the same counts. Platform finding 8 in the business policies records that this stale-count defect happened again.
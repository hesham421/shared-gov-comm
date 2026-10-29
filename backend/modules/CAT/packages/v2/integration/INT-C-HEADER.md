<!-- source: PHASE:INT-C — the phase around its blocks -->
<!-- traces: REQ-CAT-001, REQ-CAT-008 -->
## PHASE INT-C — integration (R7, the last phase)

No edge. The db-script XM register is `records: []` (SRS A8 `consumes: []`: CAT is first in the fixed module order and depends on no business module), and nothing in this plan reads another business module's data, so no XM is minted. This phase therefore carries no `XM-*` block and the splitter emits no integration package. Inbound use of CAT by other modules is their own edge, through CAT's published contract (`CatalogueReadApi`, PHASE SVC-API; catalogue-change events, PHASE CORE).

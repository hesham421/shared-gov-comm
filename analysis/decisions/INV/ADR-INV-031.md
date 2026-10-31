# ADR-INV-031 — Two SOFT-READ edges minted at P3.1 (Variant and Item state reads)
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : XM-INV-014, XM-INV-015, RULE-INV-018, RULE-INV-028, RULE-INV-042, RULE-INV-043, RULE-INV-045, REQ-INV-052, REQ-INV-124, REQ-INV-129, CON-CAT-001, CON-CAT-002, CON-CAT-007, CON-CAT-008, CON-CAT-016

## Context
The P2 register holds eleven HARD-FK edges to the Variant and Item of the catalogue module (structural: the
access is the key) and two SOFT-READ edges (bundle components, barcodes). This stage's runtime checks are the
first reads of the Variant's and the Item's state: active Variant of a STANDARD Item (RULE-INV-018, 028),
BUNDLE refusal and Item code for the settings key (RULE-INV-045), the Item's Variants for RULE-INV-042 / 043
and the Item filters, and display names. No registered edge carries that read, and a HARD-FK block's adapter
line is not the place for it.

## Decision
Mint two edges in INT-C, continuing the XM sequence:
- XM-INV-014 SOFT-READ → CAT · ENT-CAT-002 (contract CON-CAT-002), through getVariant (CON-CAT-008) and
  getItem (CON-CAT-007), port `VariantReadPort`.
- XM-INV-015 SOFT-READ → CAT · ENT-CAT-001 (contract CON-CAT-001), through getItem (CON-CAT-007) and
  searchCatalogue (CON-CAT-016), port `ItemReadPort`.
Both point downward (CAT is DAG #1). The orchestrator back-registers them into the register in this run
(records in registry-exec-be-inv.md, `xm-minted`).

## Consequences
- Every runtime read of another module's data is tracked by one edge and one package.
- The XM total of the db-script stays 13; the plan carries 15 blocks.

## Status
ACCEPTED — non-breaking.

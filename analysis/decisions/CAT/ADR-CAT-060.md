# ADR-CAT-060 — An Item unit that active barcodes use stays active

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 (gate-analysis revise, G7) · Module : CAT v2 · Date : 2026-10-28

## Context
RULE-CAT-031 binds a barcode's unit to an active unit of its Item, but only when the barcode is created. `API-CAT-013` deactivated an Item unit without looking at the barcodes bound to it. A CARTON barcode then kept resolving to a unit the Item no longer offers: the "withdrawing a record that something depends on" gap.

Two options were weighed:
- (a) deactivate the unit's barcodes with it (cascade);
- (b) refuse.

A barcode may be printed on goods already in the shop, and a silent cascade would stop it scanning with no warning. So the choice is to refuse, with a next action, in line with RULE-CAT-021 and RULE-CAT-025.

## Decision
1. New RULE-CAT-051: the system shall prevent deactivating an Item unit while active barcodes of the Item's Variants are bound to that unit. Code `CAT-409-ITEM-UNIT-HAS-BARCODES`, with `{count}` in the message and the next action "deactivate those barcodes first".
2. `API-CAT-013` locks the Item (QR-CAT-010) and counts the active barcodes of the Item's Variants in the unit's `uomCode` (new QR-CAT-071) before it deactivates the unit. Barcode creation takes the same Item lock path, so the two cannot interleave.
3. The frontend routes the code to the unit line of ITEM-SAVE, with the RULE-CAT-051 message bound by `text:`.

## Consequences
- New REQ-CAT-128, AC-CAT-130, QR-CAT-071, TC-CAT-183 (backend) and TC-CAT-196 (frontend).

## Traces
REQ-CAT-079 · REQ-CAT-089 · REQ-CAT-128 · RULE-CAT-031 · RULE-CAT-051 · API-CAT-013

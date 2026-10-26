# ADR-CAT-043 — Catalogue search contributes to the global search

Status : ACCEPTED (non-breaking) · Stage : P1 · Module : CAT v2 · Date : 2026-10-26

## Context
US-CAT-022 asks to find Items from "the one global search (items, customers, documents)" [KB:raw-idea.md §6.6]. That search spans several modules. No CAT story or policy owns the search box itself, and the erp-core manifest lists no search-aggregation extension point.

## Decision
CAT exposes a catalogue search operation (REQ-CAT-125 … REQ-CAT-127):
- the term must have ≥ 2 characters
- it returns ≤ 20 active Items, with exact code and barcode matches first
- access is checked against VIEW on CAT_ITEMS

The global search box is composed outside CAT: in the shared frontend shell, or by the module that later owns it. It calls each module's search operation. CAT declares no screen requirement for the search box.

## Consequences
- Party (customers) and the document modules offer equivalent search operations in their own SRS.
- The UX stage (P3.2) places the global search box in the shell.

## Traces
US-CAT-022 · ENT-CAT-001, ENT-CAT-009 · REQ-CAT-125, REQ-CAT-126, REQ-CAT-127 · RULE-CAT-046

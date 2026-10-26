# ADR-CAT-038 — Basis for the catalogue model while project-registry open items are OPEN

Status : ACCEPTED (non-breaking) · Stage : P1 · Module : CAT v2 · Date : 2026-10-26

## Context
The P1 reading protocol requires the project-registry open-question index to be empty for the module. The project registry (bootstrap 1.0.0) still lists the following items as OPEN for P0 dialogue:
- OI-06 (custom attribute seams)
- OI-07 (Item/Variant model)
- OI-08 (brand, unit of measure, Category)
- OI-09 (bundle owner)
- OI-10 (barcodes and scale codes)
- OI-11 (e-invoice obligations)
- OI-12 (name languages)
- OI-15 (media)

The approved PRD (gate prd-approval) cites the module registry's resolutions of the same points: OI-06, OI-07, OI-09, OI-10, OI-12 and OI-15, plus LOOKUPS OWNED brand and unit-of-measure. The domain-profile §8 gives a recommendation for each one (P-03 … P-09, P-12). Questions are forbidden at P1.

## Decision
The SRS applies the approved PRD sources and, where they are silent, the domain-profile §8 recommendations:
- every Item has ≥ 1 Variant (P-04)
- Category is a CAT tree, and brand and unit of measure are CAT-owned mdl lookup keys (P-05)
- bundle composition lives in CAT (P-06)
- a Variant carries 0..n barcodes, and scale codes need no new flag (P-07, OI-10)
- the e-invoice code is held on the Item with a Variant override (P-08)
- the Arabic name is mandatory and the English name optional (P-09, PRD P-3)
- image files live in given `file`, and CAT holds order, main image and descriptions (P-12)

Each point is recorded as DEFAULT D-01 … D-08 in the SRS "Decisions applied".

## Consequences
- The SRS is complete without reopening the PRD.
- If a later P0 dialogue closes an OI differently, the affected ENT/REQ/RULE ids change in a delta version (MODIFIED), not by rewriting this one.
- The orchestrator should mark these OIs as applied for CAT in project-registry §8.

## Traces
US-CAT-005, US-CAT-008, US-CAT-011, US-CAT-012, US-CAT-013, US-CAT-014, US-CAT-016, US-CAT-017, US-CAT-019, US-CAT-021 · ENT-CAT-001, ENT-CAT-002, ENT-CAT-006, ENT-CAT-007, ENT-CAT-009, ENT-CAT-010 · REQ-CAT-025, REQ-CAT-049, REQ-CAT-056, REQ-CAT-083, REQ-CAT-105, REQ-CAT-117

# ADR-CAT-039 — Kind of dependent records

Status : ACCEPTED (non-breaking) · Stage : P1 · Module : CAT v2 · Date : 2026-10-26

## Context
The profile allows only the entity kinds master, transactional, lookup, config and security, and gives each kind default fields. Several CAT records exist only inside one parent master record and are never addressed alone outside it:
- Variant attribute assignment
- Bundle component
- Item unit
- Barcode
- Item media

The master defaults (code, nameAr, nameEn) have no natural meaning for most of these records. Profile check CM-1 requires master entities to carry the master defaults.

## Decision
A dependent record is classified with its parent's kind (master). It carries isActiveFl and the audit fields. The identity defaults (code, nameAr, nameEn) are carried in one of two ways:
- **by the parent:** Variant attribute assignment, Bundle component and Item unit carry no code or names of their own;
- **under a named field:** for Barcode, barcodeValue serves as the code; for Item media, nameAr/nameEn are the image descriptions.

## Consequences
- No new kind is invented.
- The database stage gives these tables audit columns and isActiveFl, and no business-number column.

## Traces
ENT-CAT-005, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010 · US-CAT-011, US-CAT-012, US-CAT-015, US-CAT-016, US-CAT-019

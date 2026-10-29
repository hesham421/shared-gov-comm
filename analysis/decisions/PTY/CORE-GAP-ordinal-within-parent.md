# CORE-GAP-ordinal-within-parent — Does the core assign ordinals of a child within its parent record?

Status : OPEN (asked of erp-core 1.6.0) · Raised by : PTY v1 (gate-analysis revise, G10) · Date : 2026-10-29
Decision that depends on it : ADR-PTY-040

## The gap
Checklist F6 forbids `MAX( + 1` for document numbers, and conventions § Numbering sends every human-readable document number to `NumberSeriesApi.next(code)`. A series is unique per tenant and series code, and resets per period.

Some codes are ordinals of a child **within one parent record**: the "1", "2", "3" of a Party's Addresses in PTY, or the line number of a document line. They are shown to the user but are not document numbers. Nothing in the core states whether such an ordinal is in F6's scope, or which facility assigns it.

## What PTY does meanwhile (ADR-PTY-040)
QR-PTY-024 computes `MAX(CODE) + 1` over the Addresses of one Party, under that Party's row lock (`FOR UPDATE`, QR-PTY-001). UQ_PTY_ADDRESS_PARTY_CODE `(TENANT_ID, PARTY_FK, CODE)` is the backstop. A deactivated Address keeps its code, so codes are never reused.

## Question to the core
1. Is a per-parent ordinal outside F6, provided it is guarded by a lock on the parent row and a unique constraint with the parent key?
2. If not, which facility assigns it? For example, a `NumberSeriesApi.nextWithin(code, parentRef)`, or a documented `@Version`-guarded counter on the parent.

## Impact when answered
If the answer is 1, nothing changes. If it is 2, QR-PTY-024 is replaced by the facility's call, with no column, no endpoint and no contract change.

## Traces
ADR-PTY-040 · DBF-PTY-044 · QR-PTY-024 · CON-PTY-004

# ADR-PTY-040 — The Address code is an ordinal within its Party, assigned under the Party lock

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise (G10) · Module : PTY v1 · Date : 2026-10-29
Depends on : CORE-GAP-ordinal-within-parent (OPEN)

## Context
The Address `code` (DBF-PTY-044) is a system-assigned ordinal inside one Party: "1", "2", "3" (SRS A3). QR-PTY-024 computes it as `MAX(CODE) + 1` over that Party's Addresses. Checklist F6 forbids "a module counter for document numbers: … `MAX(` + 1" and points document numbers to `NumberSeriesApi`.

## Decision
1. The Address code is **not a document number**. SRS A3 says so: it is printed on no document given to a counterparty, it is unique only together with its Party (UQ_PTY_ADDRESS_PARTY_CODE `(TENANT_ID, PARTY_FK, CODE)`), and a counterparty never quotes it. It is a line ordinal of a child within its parent, like a document line number. `NumberSeriesApi` serves numbers that are unique per tenant and series, not per parent record, so a series per Party is not an option.
2. QR-PTY-024 keeps `COALESCE(MAX(CAST(CODE AS INTEGER)), 0) + 1` and reads only the Addresses of the one Party. **The guard is the Party row lock**: QR-PTY-001 runs `FOR UPDATE` first in every Address create (API-PTY-011, 034). Two creates on one Party therefore run one after the other, and UQ_PTY_ADDRESS_PARTY_CODE is the backstop. A deactivated Address keeps its code, so an ordinal is never reused.
3. The question whether the core offers a per-parent ordinal is raised as CORE-GAP-ordinal-within-parent. It is not a workaround (F9): the ordinal is not in F6's subject, and the gap is asked rather than built around.

## Consequences
- The backend plan's QR-PTY-024 cites this ADR. A barrier-synchronised backend TC covers two concurrent Address creates on one Party.
- If the core answers with a per-parent ordinal facility, QR-PTY-024 is replaced by it. No column and no API changes.

## Traces
ENT-PTY-004.code · DBF-PTY-044 · QR-PTY-001, QR-PTY-024 · API-PTY-011, API-PTY-034 · REQ-PTY-030

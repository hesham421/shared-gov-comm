# ADR-INV-040 — INV v1's XM edges to CAT are planned against delivered CAT v2 (resolution events acknowledged)
Status      : ACCEPTED
Stage       : gate analysis (feedback)        Module: INV        Version: v1
Lane        : operator · under Hesham's standing authorisation
Decided     : 2026-10-31
traces      : INV>CAT, XM-INV-001 … XM-INV-015, contract-cat (CAT v2) CON-CAT-001, CON-CAT-002, CON-CAT-005, CON-CAT-006

## Context
`gov.py graph` raised 16 resolution events into INV's manifest (`status.xm_events`), trigger `delivered`,
target CAT v2. These are the module edge INV>CAT and the 15 edges XM-INV-001 … XM-INV-015. An OPEN event
blocks INV's analysis gate until it is answered. The question each one asks: "your edge targets CAT, and
CAT is now delivered as v2. Does your plan hold against what was delivered?"

## Decision
Acknowledge all 16 events. No plan change is needed.
- The published CAT contract (`platform/contracts/contract-cat.md`) is CAT v2 (`Version : v2`, CON 18).
  It was published before INV's P1.5 to P4 ran, so every INV stage read the v2 contract.
- INV's backend execution plan (P3.1) cites only CAT v2 contract items: CON-CAT-001, CON-CAT-002,
  CON-CAT-005 and CON-CAT-006.
- INV's analysis is clean at the gate (`analyze gate:analysis` reports 0 critical · 0 major) against that
  contract.

## Consequences
- The 16 events are recorded as ANSWERED with this ADR (`gov.py answer-feedback`). They are not waived.
- If CAT publishes a v3 that changes a cited contract item, `gov.py graph` raises new events against v3,
  and those events are owed again.

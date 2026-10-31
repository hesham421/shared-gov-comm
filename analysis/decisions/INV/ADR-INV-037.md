# ADR-INV-037 — Track placement and package of the INV test cases
Status      : ACCEPTED
Stage       : P4 (Test Plan)        Module: INV        Version: v1
Lane        : test-gen · claude:opus
Decided     : 2026-10-31
traces      : TC-INV-001, TC-INV-239, AC-INV-001, AC-INV-154, XM-INV-001, XM-INV-015, UXD-INV-001, UXD-INV-011, ADR-INV-024, ADR-INV-033

## Context
The engine derives one TC per AC and names one split unit per TC. Three choices are not mechanical for INV:
- Which track hosts an AC. Many ACs are observed at the API ("is returned", balances, moves, events). Others
  are observed on a screen ("is shown", menu, column, field). Some are both.
- Which unit a TC names when the AC has no REST operation. Provisioning, the daily near-expiry job, the
  architecture rule, events, entity invariants and the in-process `InventoryApi` (CON-INV-006 … 019, ADR-INV-024)
  have no `API-*`.
- Which frontend unit a TC names. Each screen is split into four units (F1 … F4), all of which trace the screen.

## Decision
1. **Track.** An AC whose Then is observable at the API or in-process surface gets a backend TC. An AC whose
   Then names a screen, menu, field, column or layout gets a frontend TC. An AC whose Then is a refusal "shown"
   on a screen, a permission AC, and an AC that reveals controls and keeps data unchanged gets one TC on each
   track. The guard holds: backend 172 TCs for 146 ACs + 15 XM, frontend 67 TCs for 45 ACs + 11 UXD.
2. **Backend package.** The SVC-API SUB holding the exercised `API-*` (CRUD, SEARCH). An in-process operation →
   `SVC-API-INT`; the TC cites the `InventoryApi` method and its contract item instead of an `API-*`.
   Provisioning, the daily job and the architecture rule → `CORE`. An entity invariant asserted without an
   endpoint response (move immutability, warehouse reference on every move, lot identity per Variant, the
   no-record default of Item stock settings) → the DATA-DOM SUB of that entity. Role-preset grants and the 403
   path → `SEC-BE`. An XM-derived TC → its `XM-INV-0nn` block.
3. **Frontend package.** Among the units of the screen whose `traces=` cite the AC or UXD: a server code routed
   to a field or message, or a query state → `F2-SCR-INV-0nn`; a client validator → `F3-SCR-INV-0nn`;
   visibility, route, layout or rendering → `F4-SCR-INV-0nn`; the menu and permission gate → `SEC-FE`. A UXD's
   "rendered" TC names the F3 unit when the foreign field is a search-form picker that F3 binds, otherwise F4;
   its "empty / failure" TC names the F2 unit that declares that state.
4. **Units with no TC.** No AC, XM or UXD derives a case for: backend `DOC`, `ALIGN-BE`; frontend `F1-SCR-INV-*`
   (types), `F3-SCR-INV-012`, `F4-SCR-INV-011`, `ALIGN-FE`. Both test plans list them in their COVERAGE section
   instead of inventing a case.

## Consequences
- Every AC has at least one TC across the two plans, and every XM and cited UXD has its TCs.
- If the profile does not flag the units of point 4 `no_tests`, C10.11 reports them. The fix is upstream (an AC,
  or a `no_tests` flag), not a fabricated case.
- No REQ, AC, RULE, API, SCR or code changes.

## Status
ACCEPTED — non-breaking.

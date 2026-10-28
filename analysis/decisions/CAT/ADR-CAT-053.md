# ADR-CAT-053 — P4 test plans: unbound refusal texts and unseeded AC lookup values

| Item | Value |
|---|---|
| Module | CAT |
| Version | v2 |
| Stage | P4 — Test Plan (lane test-gen), round 2 after `gov.py analyze` |
| Status | ACCEPTED (non-breaking) |
| Date | 2026-10-28 |
| Raised by | claude:opus (test-gen lane) |
| Applies to | `analysis/modules/CAT/v2/P4/backend-test-plan-cat.md`, `analysis/modules/CAT/v2/P4/frontend-test-plan-cat.md` |
| Relates to | ADR-CAT-052 (§1 track split, §3 host data) — not rewritten; this ADR narrows two of its points |

## Context

`gov.py analyze` refused round 1 of P4 on two contracts:

- **C10.7 (msg-bound).** Eight frontend TCs asserted the words of a refusal or notice whose code the frontend plan routes to a control without a `text: <source>` binding. In the plan these codes are routed as "shows RULE-CAT-0nn's text", "text RULE-CAT-0nn" or "with RULE-CAT-0nn message", not as a `text:` row:

  | Code | RULE | Plan row that routes it | Frontend TC |
  |---|---|---|---|
  | CAT-201-EXISTING-VARIANTS-KEPT | RULE-CAT-012 | F2 ITEM-SAVE step 5 | TC-CAT-130 |
  | CAT-404-BARCODE-NOT-FOUND | RULE-CAT-047 | F2 BARCODE-LOOKUP-QUERY | TC-CAT-143 |
  | CAT-422-SCALE-LABEL-UNREADABLE | RULE-CAT-035 | F2 BARCODE-LOOKUP-QUERY | TC-CAT-144 |
  | CAT-200-PRIMARY-IMAGE-REPLACED | RULE-CAT-039 | F2 ITEM-SAVE step 8 | TC-CAT-145 |
  | CAT-400-SEARCH-TERM-TOO-SHORT | RULE-CAT-046 | F2 CATALOGUE-SEARCH-QUERY | TC-CAT-150 |
  | CAT-409-CATEGORY-IN-USE | RULE-CAT-025 | F2 CATEGORY-DEACTIVATE | TC-CAT-157 |
  | CAT-200-IMPORT-ROW-REJECTED | RULE-CAT-045 | F2 IMPORT-SUBMIT | TC-CAT-165 |
  | CAT-200-VARIANTS-IN-USE | RULE-CAT-010 | F2 TURN-OFF-CHECK-QUERY | TC-CAT-171 |

- **C10.8 (tc-data).** Six TCs used the AC values CAT_UOM `BAG` (AC-CAT-122, 123, 124; TC-CAT-120, 121, 122, and a mention in TC-CAT-138) and CAT_EINVOICE_UNIT `H87` (AC-CAT-126; TC-CAT-063, 175). The lookups block lists both as `values` that the section does not seed. The engine requires a `Host data` line that creates such a value through the call the key's owner publishes, by verb and path. The key's owner is CAT, and `api-spec-cat.yaml` publishes no add-value operation. The given mdl maintenance that ADR-CAT-052 §3 named has no verb and path in any input, so it cannot be named without inventing an endpoint.

The P4 lane may not edit the frontend plan or the API document, and questions are forbidden. Both points are decided non-breaking, and the run continues.

## Decision

1. **Unbound refusal texts (a P3.2 plan gap).** The eight frontend TCs in the table no longer assert the words. Each one asserts:
   - that the code is routed to the control the plan names (notice, banner, inline hint, result row or check panel);
   - that the control displays the envelope's `messageAr` / `messageEn`, or `ImportRowRejection.messageAr` / `messageEn`, unchanged. For RULE-CAT-046 no request is sent, so the TC asserts only that the plan's hint is shown and that no request is made;
   - the rest of the AC's observable outcome, unchanged from round 1.

   The words of each of these rules are asserted, copied from the SRS in ar and en, by the backend TC on the same AC: TC-CAT-009 (RULE-CAT-012), 046 (047), 051 (035), 054 (039), 064 (046), 034 (025), 084 (045) and 010 (010). AC coverage is unchanged (129 / 129 over both plans). A later P3.2 delta that adds a `text: RULE-CAT-0nn message` binding to these eight rows lets the frontend TCs assert the words again. That change is MODIFIED on the affected TCs, not a new sequence.

2. **Unseeded AC lookup values.** No named add-value call exists for `BAG` or `H87`, so each case uses a seeded value that keeps the AC's Given true. No host value is moved into a CAT seed, and no endpoint is invented:
   - AC-CAT-126 ("PIECE already has an active e-invoice unit; a second one is refused"): the second unit is the seeded active CAT_EINVOICE_UNIT value `BX` in place of `H87` (TC-CAT-063, TC-CAT-175). The refusal comes from RULE-CAT-043 either way. A seeded value also keeps RULE-CAT-006 from refusing the request before the duplicate check.
   - AC-CAT-122, 123, 124 ("a unit of measure with no active Unit e-invoice code"): the unit is the seeded active CAT_UOM value `DOZEN`. TC-CAT-120 and 121 first deactivate its seeded code `DOZEN → DZN` (D-12) through API-CAT-037, so DOZEN has no active Unit e-invoice code, which is the Given of REQ-CAT-120. TC-CAT-122 uses the seeded `DOZEN → DZN` as "the active Unit e-invoice code" and an Item unit `DOZEN`. The AC's e-invoice values `EA` and `BX` are seeded and kept.
   - TC-CAT-138 drops its "e.g. BAG" mention. Its precondition (the tenant's CAT_UOM holds only the 8 seeded values) is unchanged.

   Each TC's `Host data` line now reads `none`, and its `Test data` line says which AC value was replaced and why. The other host values (`BRAND_068`, `OLDBRAND`, `BRAND1_137 … BRAND6_137`, absent `XYZ`) are unchanged. Every TC still runs in a tenant of its own (ADR-CAT-052 §4), and no value is demanded in opposite states.

## Consequences

- No AC, rule, message, endpoint or field is invented, and no TC id is renumbered. The TCs edited are TC-CAT-063, 120, 121, 122 (backend) and TC-CAT-130, 138, 143, 144, 145, 150, 157, 165, 171, 175 (frontend). All of them remain ADDED in this version.
- The frontend plan holds a recorded gap: eight routed codes without a `text:` binding (point 1). The executor implements the routing as the plan states it.
- The ACs that name `BAG` and `H87` are covered with seeded values. A later delta that publishes an add-value operation for CAT_UOM / CAT_EINVOICE_UNIT (or adds `BAG` / `H87` to `seeded`) lets these TCs use the AC values again. That change is MODIFIED on the affected TCs.
- The next free ADR is ADR-CAT-054.

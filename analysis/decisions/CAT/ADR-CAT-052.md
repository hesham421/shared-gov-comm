# ADR-CAT-052 — P4 test plans: track split, package binding, host data and recorded plan gaps

| Item | Value |
|---|---|
| Module | CAT |
| Version | v2 |
| Stage | P4 — Test Plan (lane test-gen) |
| Status | ACCEPTED (non-breaking) |
| Date | 2026-10-28 |
| Raised by | claude:opus (test-gen lane) |
| Applies to | `analysis/modules/CAT/v2/P4/backend-test-plan-cat.md`, `analysis/modules/CAT/v2/P4/frontend-test-plan-cat.md` |

## Context

The P4 engine derives one `TC-*` per `AC-*` and has each TC name the one split unit it is acceptance for. For the CAT v2 inputs, five derivation points are not mechanical:
- which track (backend, frontend or both) carries the TC of an AC;
- which unit a TC names when the plan places the behaviour of its REQ outside the SUB that holds the exercised endpoint;
- how a TC creates a lookup value that is not seeded, when the key's owner (CAT) publishes no add-value call;
- how an AC is asserted when the API document does not expose the field its Then names;
- how the three `UXD-*` that the frontend plan cites are tested, given that their owner is the given erp-core and not a business module.

Questions are forbidden in this lane. Every point below is decided non-breaking, and the run continues.

## Decision

1. **Track split.** A backend TC is derived for every AC whose Then is observable at an `API-CAT-*` operation (124 of 129 ACs). A frontend TC is derived for every AC whose Then is observable on a screen and whose refusal text, when asserted, is bound by a `text:` row of the frontend plan (52 ACs). Five ACs are observable only on a screen, so they get a frontend TC only: AC-CAT-014, 015, 027, 075, 076. The permission AC (AC-CAT-129) gets a TC on both tracks (engine §3). Coverage is counted over the union of the two plans: 129 of 129 ACs.
2. **Package binding.**
   - Backend default: the `SVC-API-*` SUB that holds the first API the TC exercises (`SVC-API-CRUD`, `SVC-API-SEARCH` or `SVC-API-INT`).
   - Backend exception: when the AC's REQ appears in the `traces=` of another unit and that unit's text states the behaviour the AC asserts, the TC names that unit:
     - `CORE` for AC-CAT-008 and 009 (catalogue-change events, outbox), AC-CAT-011, 012 and 013 (capability flags), AC-CAT-030 (English display fallback), AC-CAT-033 and 034 (number series) and AC-CAT-078 (a deactivated lookup value is kept on a record);
     - `DATA-DOM-MASTER` for AC-CAT-051 (generation rules RULE-CAT-012/013/014/020, placed under ENT-CAT-002/005);
     - `DATA-DOM-LOOKUP` for AC-CAT-096 (an inactive layout stops decoding) and AC-CAT-124 (a deactivated Unit e-invoice code gives an empty e-invoice unit);
     - `SEC-BE` for AC-CAT-129 (empty result instead of 403).
   - Backend units that no TC names: `DOC`, `ALIGN-BE` and `INT-C`. `INT-C` carries no block, because the XM register is empty. `DOC` and `ALIGN-BE` implement no AC of their own; they are exercised indirectly by every TC.
   - Frontend: `F2-SCR-CAT-00n` when the TC asserts a read, a mutation or the routing of a server code; `F3-SCR-CAT-00n` when it asserts a client validator or form behaviour; `F4-SCR-CAT-00n` when it asserts a route, visibility, composition, a flow or a `UXD-*` field; `SEC-FE` for the permission TC.
   - Frontend units that no TC names: `F1-SCR-CAT-001 … 006` (generated types only, no observable behaviour) and `ALIGN-FE` (alignment self-check).
   - If the profile does not flag these phases `no_tests`, the C10.11 findings for them are expected, and this decision records them.
3. **Host data.** The lookups block names CAT as the owner of every `CAT_*` key, but its values are held in the given `erp-core:modules.mdl`, and `api-spec-cat.yaml` publishes no add-value operation. A TC that needs a value the section does not seed (CAT_BRAND values, CAT_UOM `BAG`, CAT_EINVOICE_UNIT `H87`) has a `Host data` line that creates the value through the given mdl lookup-value maintenance (`com.erp.mdl.crossmodule.MdlLookupApi`, add value to key). The executor binds that given call. No host value is moved into a CAT seed.
4. **Catalogue isolation.** Every TC runs in a tenant of its own. A TC that asserts a closed option set has an explicit precondition that its tenant's key holds only the values the TC names: AC-CAT-075 (CAT_BRAND), AC-CAT-076 (CAT_UOM, only the 8 seeded values). The host values that other TCs need (for example `BAG`, `BRAND_068`, `OLDBRAND`) therefore never share a catalogue with the TC that leaves them out. No two TCs demand opposite states of one value.
5. **Fields the API document does not expose.** The following are asserted as the plan binds them, and the gap is recorded for a later P3.1 delta. In each case the AC's observable outcome is still asserted.
   - AC-CAT-018 and AC-CAT-021 name a Variant's `updatedAt`. `VariantSummary` and `VariantDetail` expose no audit field. The TCs assert that the Variant's `version` is unchanged. QR-CAT-014 moves `VERSION` together with `UPDATED_AT` on every Variant update.
   - AC-CAT-007 names "the existing Item's code as the next action". AC-CAT-106 says "the 2 Variants are listed". `LocalizedError` binds only `code`, `messageAr` and `messageEn`, so the TCs assert the envelope. The executor asserts the next-action code and the listed Variants once the document binds a field for them. On the frontend, the plan derives both locally: the next action is a search by nameAr, and the lines are pre-flagged `no-barcode`. The frontend TCs assert that.
6. **Fault injection.** AC-CAT-009 (commit failure), AC-CAT-013 (flag read without value) and AC-CAT-036 (number series error) need a failure of a given seam. The TCs state it as a precondition on the given's seam: the outbox / persistence commit, `SettingsApi`, or `NumberSeriesApi` made to fail. They do not specify a tool (`profile.stack.testing` is agnostic).
7. **UXD integration.** UXD-CAT-001 (lookup values), UXD-CAT-002 (capability flags) and UXD-CAT-003 (image files) are foreign-owned fields that SCR-CAT-001 renders. Their owner is the given erp-core. The frontend plan's `INT-UXD` phase is populated with the two cases of engine §5 per UXD (rendered, and empty or owner failure), grouped under the owner `erp-core`. That is 6 TCs. The backend `INT-XM` phase is absent, because no `XM-*` exists.

## Notes added later

- **Counts superseded (gate-analysis round 3, G6).** The counts in point 1 ("124 of 129 ACs", "52 ACs", "129 of 129") are those of the first P4 run. They are superseded: after the second gate-analysis revise the backend track covered 146 ACs and both tracks together 152; after the third revise (AC-CAT-153 … 160) the backend track covers 154 of 160 ACs and both tracks together 160. The COVERAGE sections of the two test plans are the authoritative counts.
- **Point 5, the next-action code of AC-CAT-007, is closed (gate-analysis round 3, G12).** The RULE-CAT-003 message now carries `{code}`, filled from QR-CAT-002, so the envelope's messageAr / messageEn name the existing Item's code. TC-CAT-003 and TC-CAT-126 assert it directly, and the frontend jumps to `/cat/items?text=<code>&isActiveFl=true`. AC-CAT-007, TC-CAT-003 and TC-CAT-126 are MODIFIED, not renumbered.

## Consequences

- No AC, rule, message, endpoint or field is invented. Every TC cites its AC, its REQ and the API or SCR it exercises.
- The analyze report may list C10.11 findings for the units named in point 2. They are expected and recorded here.
- A later P3.1 delta that adds the next-action code and the listed Variants to the error envelope, or the audit fields to the Variant views, turns the assertions of point 5 into direct ones. That change is MODIFIED on the affected TCs, not a new sequence.

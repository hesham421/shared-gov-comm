<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# Frontend test plan — Catalog / الكتالوج (CAT) — v2

Module : CAT · Version : v2 · Stage : P4 · Track : frontend · Plan : test · Profile : commerce
Lane   : test-gen · questions forbidden · TC stream `TC-CAT-{seq:3}` shared with the backend test plan (backend: TC-CAT-001 … TC-CAT-124, TC-CAT-183 … TC-CAT-193, TC-CAT-197 … TC-CAT-207, TC-CAT-211 … TC-CAT-219, TC-CAT-221 … TC-CAT-231, TC-CAT-233 … TC-CAT-235; this file: TC-CAT-125 … TC-CAT-182, TC-CAT-194 … TC-CAT-196, TC-CAT-208 … TC-CAT-210, TC-CAT-220 and TC-CAT-232)

## Header

### Sources (generated current state)
| Input | File | Version | Use |
|---|---|---|---|
| srs | `_state/current-srs.md` | CAT v2 (P1) | derivation source: AC-CAT-*, RULE-CAT-* messages, lookups block with labels (A6) |
| registry-srs | `_state/current-registry-srs.md` | CAT v2 (P1) | ID ranges |
| registry-db | `_state/current-registry-db.md` | CAT v2 (P2) | no XM |
| backend-execution-plan | `_state/current-backend-execution-plan.md` | CAT v2 (P3.1) | the Error Catalog codes the screens route |
| frontend-execution-plan | `_state/current-frontend-execution-plan.md` | CAT v2 (P3.2) | SCR-CAT-001 … 006, routes, F2 code routing (`text:` bindings), F3 validators, F4 composition, UXD-CAT-001 … 003, split units (Package) |
| api-spec | `_state/current-api-spec.yaml` (`api-spec-cat.yaml`, OpenAPI 3.1.0, 2.0.0) | CAT v2 (P3.1) | the document the frontend mock server serves; shapes read by `x-api-id` |
| dependency-graph | `_state/current-dependency-graph.md` | CAT v2 | CAT tier 1 |

### Delta
The current state holds no earlier test plan, so every TC is ADDED. The frontend part of the TC stream continues from TC-CAT-124, the backend plan's last TC. Within v2, the third gate-analysis revise ADDED TC-CAT-210 and MODIFIED TC-CAT-126 (the jump goes to the existing Item's code — G12) and TC-CAT-209 (the inactive Entry keeps its three repairs — G2, ADR-CAT-068). The sixth gate-analysis revise ADDED TC-CAT-220 (reactivation refused under RULE-CAT-003, with its next action — G2, AC-CAT-162). The seventh gate-analysis revise MODIFIED TC-CAT-139 and TC-CAT-177 (a host brand whose labels differ from its code is deactivated, and the screen shows its raw code — G1, ADR-CAT-073), TC-CAT-195 (the Category label comes with the Item read, also for a user who cannot read the Category tree — G5, ADR-CAT-075), and re-pointed TC-CAT-210 and TC-CAT-220 to REQ-CAT-145 … 147 and REQ-CAT-144 (G7); it ADDED no TC. The eighth gate-analysis revise MODIFIED TC-CAT-146 (the 7 MB JPG is refused by the FILE category `CAT_ITEM_IMAGE` at upload and shown in the FILE envelope; the screen checks only the 20-image count — G1, ADR-CAT-076; moved from F3-SCR-CAT-001 to F2-SCR-CAT-001, where the upload refusal is routed) and TC-CAT-166 (its client check stays; the server's type / size check waits on the core); it ADDED no TC. The ninth gate-analysis revise ADDED and MODIFIED no TC here: its new ACs (AC-CAT-165, Category change events; AC-CAT-166, an import row refused for a scale prefix) are observable at the API only and are covered on the backend track (TC-CAT-224, TC-CAT-226). The tenth gate-analysis revise ADDED TC-CAT-232 (a flagged label line gets its internal barcode on the label screen, then prints — G1, AC-CAT-171, ADR-CAT-078), MODIFIED TC-CAT-170 (each flagged line also offers "Generate internal barcode") and re-stamped the COVERAGE rows from registry-srs (AC 171 — the ninth revise had left them at 164 — G7). The eleventh gate-analysis revise ADDED no TC here (its new ACs are asserted at the API: TC-CAT-233 … 235) and MODIFIED TC-CAT-167 (the barcode shown is the label barcode, a GTIN or INTERNAL barcode in the base unit — G1, ADR-CAT-079), TC-CAT-170 (the RULE-CAT-037 message names the base unit) and TC-CAT-179 (each Bundle line shows the unit of its quantity — G2, AC-CAT-174, ADR-CAT-080); the COVERAGE rows are re-stamped from registry-srs (AC 174). No id is renumbered or REMOVED.

### Framework
`profile.stack.testing` is **agnostic**. Each block is the whole contract. This plan names no framework, runner, selector strategy or file layout.

### Open ADRs
- ADR-CAT-052 (this stage) is ACCEPTED and non-breaking. It covers the track split, frontend package binding, host data, option-set isolation, fields the API document does not expose, and UXD integration.
- ADR-CAT-053 (this stage, analyze round 2) is ACCEPTED and non-breaking. It records the eight server codes the frontend plan routes to a control without binding their words (`text:`): TC-CAT-130, 143, 144, 145, 150, 157, 165 and 171 assert the routing and the verbatim display of the envelope's messageAr / messageEn, not the words, which the backend TCs assert. It also replaces the unseeded AC value H87 by a seeded value in TC-CAT-175.
- The TCs also cite ADR-CAT-043, 048, 049, 050 and 051, and, from the gate-analysis revise, ADR-CAT-056 (files through the given FILE endpoints), ADR-CAT-060 (unit used by barcodes) and ADR-CAT-061 (read facades). From the gate-analysis round 3 they cite ADR-CAT-068 (three repairs stay allowed on an inactive Item). From the gate-analysis round 2 they cite ADR-CAT-063 (inactive parent refuses child writes) and ADR-CAT-065 (CAT lookup keys served through API-CAT-052). From the gate-analysis round 7 they cite ADR-CAT-073 (an inactive lookup value renders as its raw code — TC-CAT-139, 177) and ADR-CAT-075 (the Item reads carry the Category label — TC-CAT-195). From round 8 they cite ADR-CAT-076 (image type and size held by the FILE category `CAT_ITEM_IMAGE` — TC-CAT-146; the import's server-side type / size check waits on the core — TC-CAT-166), from round 10 ADR-CAT-078 (the barcode label screen generates the missing internal barcode — TC-CAT-232), and from round 11 ADR-CAT-079 (a label shows only a GTIN or INTERNAL barcode in the base unit — TC-CAT-167, 170) and ADR-CAT-080 (a Bundle component quantity is in the component Item's base unit — TC-CAT-179). None of them is BLOCKED.
- Host values of CAT_BRAND are created by the given mdl call `POST /api/v1/mdl/lookup-types/{id}/values` and deactivated by `DELETE /api/v1/mdl/lookup-values/{id}`, where `{id}` is the CAT_BRAND type found by `POST /api/v1/mdl/lookup-types/search` (SRS A6). Each Host data line names the call it uses.

### Shared precondition F0 (applies to every TC; each TC adds only what it states)
- The CAT frontend build is served in the platform shell, with the bound reads answered by the mock server of `api-spec-cat.yaml`, or by the delivered backend.
- The user is a preset Owner holding every `PERM_CAT_*` permission.
- The platform app-config reports `variants` and `bundles` on.
- CAT's lookup read `API-CAT-052` (`GET /api/v1/cat/lookups/{key}`, over the given mdl values through `OwnedLookups`) serves the seeded CAT_* values with their SRS A6 labels.
- The TC runs in a tenant of its own (ADR-CAT-052 §4).
- Every asserted message is the RULE message of the SRS in ar and en, as the plan routes it (`text: RULE-CAT-0nn message`). It is checked once with the UI language in ar and once in en (language ALL). Where the plan routes a code to a control without binding its words, the TC asserts only the routing and the unchanged display of the envelope's messageAr / messageEn; the wording is asserted on the backend track (ADR-CAT-053 §1).
- "No request" means the mock server records no call to the named operation.





## TC TRACEABILITY INDEX

### AC → TC (and REQ → TC) — this track
| AC | REQ | TC | | AC | REQ | TC |
|---|---|---|---|---|---|---|
| AC-CAT-003 | REQ-CAT-003 | TC-CAT-125 | | AC-CAT-071 | REQ-CAT-069 | TC-CAT-155 |
| AC-CAT-005 | REQ-CAT-005 | TC-CAT-176 | | AC-CAT-072 | REQ-CAT-070 | TC-CAT-156 |
| AC-CAT-007 | REQ-CAT-007 | TC-CAT-126 | | AC-CAT-073 | REQ-CAT-071 | TC-CAT-157 |
| AC-CAT-013 | REQ-CAT-013 | TC-CAT-127 (+ UXD TC-CAT-180) | | AC-CAT-075 | REQ-CAT-073 | TC-CAT-137 (+ UXD TC-CAT-177) |
| AC-CAT-014 | REQ-CAT-014 | TC-CAT-128 (+ UXD TC-CAT-179) | | AC-CAT-076 | REQ-CAT-074 | TC-CAT-138 (+ UXD TC-CAT-177) |
| AC-CAT-015 | REQ-CAT-015 | TC-CAT-129 | | AC-CAT-078 | REQ-CAT-076 | TC-CAT-139 (+ UXD TC-CAT-178) |
| AC-CAT-016 | REQ-CAT-016 | TC-CAT-158 | | AC-CAT-082 | REQ-CAT-080 | TC-CAT-140 |
| AC-CAT-020 | REQ-CAT-019 | TC-CAT-130 | | AC-CAT-088 | REQ-CAT-086 | TC-CAT-141 |
| AC-CAT-023 | REQ-CAT-022 | TC-CAT-171 | | AC-CAT-090 | REQ-CAT-088 | TC-CAT-142 |
| AC-CAT-025 | REQ-CAT-024 | TC-CAT-131 | | AC-CAT-092 | REQ-CAT-090 | TC-CAT-143 |
| AC-CAT-027 | REQ-CAT-026 | TC-CAT-132 | | AC-CAT-094 | REQ-CAT-092 | TC-CAT-172 |
| AC-CAT-028 | REQ-CAT-027 | TC-CAT-133 | | AC-CAT-097 | REQ-CAT-095 | TC-CAT-173 |
| AC-CAT-031 | REQ-CAT-030 | TC-CAT-134 | | AC-CAT-100 | REQ-CAT-098 | TC-CAT-174 |
| AC-CAT-032 | REQ-CAT-031 | TC-CAT-135 | | AC-CAT-102 | REQ-CAT-100 | TC-CAT-144 |
| AC-CAT-037 | REQ-CAT-036 | TC-CAT-164 | | AC-CAT-103 | REQ-CAT-101 | TC-CAT-167 |
| AC-CAT-040 | REQ-CAT-039 | TC-CAT-165 | | AC-CAT-104 | REQ-CAT-102 | TC-CAT-168 |
| AC-CAT-041 | REQ-CAT-040 | TC-CAT-166 | | AC-CAT-105 | REQ-CAT-103 | TC-CAT-169 |
| AC-CAT-044 | REQ-CAT-042 | TC-CAT-159 | | AC-CAT-106 | REQ-CAT-104 | TC-CAT-170 |
| AC-CAT-047 | REQ-CAT-045 | TC-CAT-160 | | AC-CAT-107 | REQ-CAT-105 | UXD TC-CAT-181, 182 (module TC on the backend: TC-CAT-113) |
| AC-CAT-050 | REQ-CAT-048 | TC-CAT-161 | | AC-CAT-108 | REQ-CAT-106 | TC-CAT-145 |
| AC-CAT-054 | REQ-CAT-052 | TC-CAT-136 | | AC-CAT-111 | REQ-CAT-109 | UXD TC-CAT-181 (module TC on the backend: TC-CAT-115) |
| AC-CAT-056 | REQ-CAT-054 | TC-CAT-162 | | AC-CAT-112 | REQ-CAT-110 | TC-CAT-146 |
| AC-CAT-057 | REQ-CAT-055 | TC-CAT-163 | | AC-CAT-118 | REQ-CAT-116 | TC-CAT-147 |
| AC-CAT-066 | REQ-CAT-064 | TC-CAT-152 | | AC-CAT-125 | REQ-CAT-123 | TC-CAT-148 |
| AC-CAT-069 | REQ-CAT-067 | TC-CAT-153 | | AC-CAT-126 | REQ-CAT-124 | TC-CAT-175 |
| AC-CAT-070 | REQ-CAT-068 | TC-CAT-154 | | AC-CAT-127 | REQ-CAT-125 | TC-CAT-149 |
| | | | | AC-CAT-128 | REQ-CAT-126 | TC-CAT-150 |
| | | | | AC-CAT-129 | REQ-CAT-127 | TC-CAT-151 |
| AC-CAT-130 | REQ-CAT-128 | TC-CAT-196 (backend: TC-CAT-183) | | AC-CAT-138 | REQ-CAT-131 | TC-CAT-194, 208 |
| AC-CAT-142 | REQ-CAT-066 | TC-CAT-195 | | AC-CAT-144 | REQ-CAT-135 | TC-CAT-209 (backend: TC-CAT-198) |
| AC-CAT-154 | REQ-CAT-145 | TC-CAT-210 (backend: TC-CAT-185, 212) | | AC-CAT-155 | REQ-CAT-146 | TC-CAT-210 (backend: TC-CAT-186, 212) |
| AC-CAT-156 | REQ-CAT-147 | TC-CAT-210 (backend: TC-CAT-187, 212) | | AC-CAT-162 | REQ-CAT-144 | TC-CAT-220 (backend: TC-CAT-219) |
| AC-CAT-171 | REQ-CAT-148 | TC-CAT-232 (backend: TC-CAT-231) | | AC-CAT-174 | REQ-CAT-058 | UXD TC-CAT-179 (module TC on the backend: TC-CAT-235) |

TC-CAT-176 (lifecycle flow) also passes through REQ-CAT-024, REQ-CAT-003 and REQ-CAT-004. Every other AC-CAT-* is covered on the backend track: see the AC → TC table of `backend-test-plan-cat.md`.

### SCR → TC (TC-CAT-…)
| SCR | TCs |
|---|---|
| SCR-CAT-001 Items | 125 – 151, 176, 177 – 182, 194 – 196, 208 – 210, 220 |
| SCR-CAT-002 Categories | 152 – 157 |
| SCR-CAT-003 Variant attributes | 128 (menu entry), 158 – 163 |
| SCR-CAT-004 Item import | 164 – 166 |
| SCR-CAT-005 Barcode labels | 167 – 170, 232 |
| SCR-CAT-006 Catalogue settings | 171 – 175 |

### RULE / code → TC (TC-CAT-…) — refusals and notices routed on screen (msg-bound)
A row marked "routed, words unbound" is a code the frontend plan routes to a control without a `text:` binding. Its TC asserts the routing and the unchanged display of the envelope's messageAr / messageEn, and the wording is asserted by the backend TC named (ADR-CAT-053 §1). Every other row asserts the words, bound by a `text:` row of the plan.

| RULE | code | TCs | where the plan routes it |
|---|---|---|---|
| RULE-CAT-001 | CAT-422-NAME-AR-INVALID | 133, 134 | F3 `ItemForm` nameAr · F2 ITEM-SAVE |
| RULE-CAT-002 | CAT-422-NAME-EN-TOO-LONG | 135 | F3 `ItemForm` nameEn · F2 ITEM-SAVE |
| RULE-CAT-003 | CAT-409-ITEM-NAME-DUPLICATE | 126 | F2 ITEM-SAVE (inline nameAr + action to the code the message names) |
| RULE-CAT-008 | CAT-422-CAPABILITY-OFF | 158 | F2 ATTRIBUTE-SCREEN-INIT / F4 route state (the attribute reads are not flag-bound) |
| RULE-CAT-009 | CAT-500-CAPABILITY-UNREADABLE | 127, 180 | F2 ITEM-SAVE banner · F2 CAPABILITY-FLAGS-INIT |
| RULE-CAT-010 | CAT-200-VARIANTS-IN-USE | 171 | F2 TURN-OFF-CHECK-QUERY — routed, words unbound (wording: TC-CAT-010) |
| RULE-CAT-012 | CAT-201-EXISTING-VARIANTS-KEPT | 130 | F2 ITEM-SAVE step 5 — routed, words unbound (wording: TC-CAT-009) |
| RULE-CAT-014 | CAT-422-VARIANT-LIMIT-EXCEEDED | 136 | F3 Variant generation validator |
| RULE-CAT-016 | CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE | 163 (160 passes) | F2 ATTRIBUTE-SAVE · F3 value lines |
| RULE-CAT-017 | CAT-409-ATTRIBUTE-VALUE-IN-USE | 162 | F2 ATTRIBUTE-SAVE |
| RULE-CAT-023 | CAT-422-CATEGORY-PARENT-INVALID | 155 | F3 `CategoryForm` parent · F2 CATEGORY-SAVE |
| RULE-CAT-024 | CAT-409-CATEGORY-DUPLICATE | 156 | F2 CATEGORY-SAVE |
| RULE-CAT-025 | CAT-409-CATEGORY-IN-USE | 157 | F2 CATEGORY-DEACTIVATE — routed, words unbound (wording: TC-CAT-034) |
| RULE-CAT-027 | CAT-422-ITEM-UNIT-INVALID | 140 | F3 Item unit line · F2 ITEM-SAVE |
| RULE-CAT-030 | CAT-422-GTIN-INVALID | 142 | F3 barcode GTIN line · F2 ITEM-SAVE |
| RULE-CAT-034 | CAT-422-SCALE-LAYOUT-INVALID | 174 | F3 layout drawer · F2 LAYOUT-SAVE |
| RULE-CAT-035 | CAT-422-SCALE-LABEL-UNREADABLE | 144 | F2 BARCODE-LOOKUP-QUERY — routed, words unbound (wording: TC-CAT-051) |
| RULE-CAT-036 | CAT-422-LABEL-COPIES-OUT-OF-RANGE | 169 | F3 labels · F2 LABEL-PRINT |
| RULE-CAT-037 | CAT-422-LABEL-VARIANT-NO-BARCODE | 170, 232 (the flag repaired on the line — ADR-CAT-078) | F3 labels · F2 LABEL-PRINT · F2 LABEL-INTERNAL-BARCODE |
| RULE-CAT-038 | CAT-422-IMAGE-NOT-ACCEPTED | 146 (the 20-image count; the file part is the FILE category's refusal, shown in the FILE envelope — ADR-CAT-076) | F3 image line (count) · F2 ITEM-SAVE (step 8 upload refusal and the count code) |
| RULE-CAT-039 | CAT-200-PRIMARY-IMAGE-REPLACED | 145 | F2 ITEM-SAVE step 8 — routed, words unbound (wording: TC-CAT-054) |
| RULE-CAT-041 | CAT-422-PUBLISH-BLOCKED | 147 (176 state) | F2 ITEM-STATE-COMMAND |
| RULE-CAT-042 | CAT-422-EINVOICE-CODE-INVALID | 148 | F3 `ItemForm` e-invoice pair · F2 ITEM-SAVE |
| RULE-CAT-043 | CAT-409-UNIT-EINVOICE-DUPLICATE | 175 | F3 unit code drawer · F2 UNIT-CODE-SAVE |
| RULE-CAT-044 | CAT-422-IMPORT-FILE-REJECTED | 166 | F3 `ImportForm` · F2 IMPORT-SUBMIT |
| RULE-CAT-045 | CAT-200-IMPORT-ROW-REJECTED | 165 | F2 IMPORT-SUBMIT (row messages) — routed, words unbound (wording: TC-CAT-084) |
| RULE-CAT-046 | CAT-400-SEARCH-TERM-TOO-SHORT | 150 | F2 CATALOGUE-SEARCH-QUERY — routed, words unbound (wording: TC-CAT-064) |
| RULE-CAT-047 | CAT-404-BARCODE-NOT-FOUND | 143 | F2 BARCODE-LOOKUP-QUERY — routed, words unbound (wording: TC-CAT-046) |
| RULE-CAT-048 | CAT-200-DEACTIVATED | 176 | F2 ITEM-STATE-COMMAND |
| RULE-CAT-051 | CAT-409-ITEM-UNIT-HAS-BARCODES | 196 | F3 Item unit (deactivate) · F2 ITEM-SAVE (`text: RULE-CAT-051 message`) |
| RULE-CAT-053 | CAT-422-PARENT-INACTIVE | 209 | F2 ITEM-SAVE (form banner, `text: RULE-CAT-053 message`) · F3 inactive-Item mode |
| RULE-CAT-050, 026, 019 | CAT-422-ITEM-NO-ACTIVE-VARIANT, CAT-422-CATEGORY-INACTIVE, CAT-422-BUNDLE-COMPONENT-INVALID | 210 | F2 ITEM-STATE-COMMAND (command banners with the repair actions of ADR-CAT-068) |
| RULE-CAT-003 (reactivation) | CAT-409-ITEM-NAME-DUPLICATE | 220 | F2 ITEM-STATE-COMMAND (command banner, `text: RULE-CAT-003 message`, action "Open the existing item") |

The other rules are asserted on the backend track. No case here asserts the words of a refusal or notice that the frontend plan leaves unbound; those eight codes are recorded as a plan gap in ADR-CAT-053 §1, for a later P3.2 delta that adds their `text:` bindings.

### UXD → TC
| UXD | owner (source module) | rendered | empty / owner failure |
|---|---|---|---|
| UXD-CAT-001 lookup values | erp-core (given mdl), read through API-CAT-052 | TC-CAT-177 | TC-CAT-178 |
| UXD-CAT-002 capability flags | erp-core (given cu / app-config) | TC-CAT-179 | TC-CAT-180 |
| UXD-CAT-003 image files | erp-core (given file) | TC-CAT-181 | TC-CAT-182 |

### Package → TC (TC-CAT-…)
| Package (frontend plan unit) | # | TCs |
|---|---|---|
| F2-SCR-CAT-001 | 18 | 125, 126, 127, 130, 131, 137, 138, 139, 141, 143, 144, 145, 146, 147, 149, 150, 195, 220 |
| F3-SCR-CAT-001 | 11 | 132, 133, 134, 135, 136, 140, 142, 148, 196, 209, 210 |
| F4-SCR-CAT-001 | 9 | 128, 129, 176, 177, 178, 179, 180, 181, 182 |
| F2-SCR-CAT-002 | 4 | 152, 154, 156, 157 |
| F3-SCR-CAT-002 | 1 | 155 |
| F4-SCR-CAT-002 | 1 | 153 |
| F2-SCR-CAT-003 | 3 | 159, 162, 163 |
| F3-SCR-CAT-003 | 1 | 160 |
| F4-SCR-CAT-003 | 2 | 158, 161 |
| F2-SCR-CAT-004 | 1 | 164 |
| F3-SCR-CAT-004 | 1 | 166 |
| F4-SCR-CAT-004 | 1 | 165 |
| F2-SCR-CAT-005 | 2 | 167, 232 |
| F3-SCR-CAT-005 | 2 | 169, 170 |
| F4-SCR-CAT-005 | 1 | 168 |
| F2-SCR-CAT-006 | 2 | 171, 172 |
| F3-SCR-CAT-006 | 2 | 174, 175 |
| F4-SCR-CAT-006 | 1 | 173 |
| SEC-FE | 3 | 151, 194, 208 |
| F1-SCR-CAT-001 … 006 · ALIGN-FE | 0 | no TC — F1 is generated types with no observable behaviour; ALIGN-FE is the alignment self-check (ADR-CAT-052 §2) |

## COVERAGE

| Measure | Result |
|---|---|
| AC covered (this track) | 61 / 174 by module TCs (re-stamped from registry-srs at the eleventh gate-analysis revise: AC 174; AC-CAT-174 is also asserted here by the UXD case TC-CAT-179). Of those, AC-CAT-014, 015, 027, 075, 138 and 142 are covered on this track only (AC-CAT-076 also has its server read on the backend, TC-CAT-207). |
| AC covered (both plans) | 174 / 174 ✓, no gap. The other 113 ACs are covered by the backend track only; see `backend-test-plan-cat.md` (ADR-CAT-052 §1). |
| REQ covered (this track) | 62 REQs by module TCs (plus REQ-CAT-012, 105 and 109 through UXD TCs) |
| REQ covered (both plans) | 148 / 148 ✓ |
| SCR covered | 6 / 6 ✓ (SCR-CAT-001 … 006) |
| UXD covered | 3 / 3 ✓. Each UXD has its rendered and its failure case (6 TCs in `INT-UXD`). |
| Refusal texts | every wording asserted here is bound by a `text:` row of the frontend plan (msg-bound) ✓. Eight routed codes have no binding (RULE-CAT-010, 012, 025, 035, 039, 045, 046, 047): their TCs assert routing and unchanged display only, and the gap is recorded in ADR-CAT-053 §1. |
| Track size | 66 TCs for 61 ACs and 3 UXDs (ratio < 2, within the guard) |
| Host data | CAT_BRAND BRAND1_137 … BRAND5_137 (active) and BRAND6_137 (inactive) for TC-CAT-137; CAT_BRAND OLDBRAND (labelled «علامة قديمة» / "Old brand"; active, then inactive) for TC-CAT-139; CAT_BRAND RETIRED_177 (labelled «علامة متوقفة» / "Retired brand"; active, then inactive) for TC-CAT-177 — each created by `POST /api/v1/mdl/lookup-types/{id}/values` and, where inactive, deactivated by `DELETE /api/v1/mdl/lookup-values/{id}`. The AC value CAT_EINVOICE_UNIT H87 (AC-CAT-126) is not seeded; TC-CAT-175 stays on seeded data with the value BX in its place (ADR-CAT-053 §2). TC-CAT-137 and TC-CAT-138 assert closed option sets, so they run in a tenant that holds only their own values (ADR-CAT-052 §4). No value is demanded in opposite states. |

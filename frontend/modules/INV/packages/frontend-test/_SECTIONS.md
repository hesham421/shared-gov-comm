<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND TEST PLAN — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Profile : commerce   Stage : P4 (Test Plan) · pass 2 · round 1 · gate-analysis revise 5
Track  : frontend · plan test · lane test-gen · prepared by claude:opus
Sources: _state/current-srs.md (INV v1 — REQ 141 · AC 160 · SCR-REQ 12)
         _state/current-frontend-execution-plan.md (P3.2 — SCR-INV-001 … 012, UXD-INV-001 … 011, F1 … F4, SEC-FE)
         _state/current-api-spec (api-spec-inv.yaml — the document the mock server serves)
         _state/current-backend-execution-plan.md (Error Catalog texts) · _state/current-registry-srs.md
Owns   : TC-INV-173 … TC-INV-241, TC-INV-262 … TC-INV-263, TC-INV-285 and TC-INV-294 (this file) — the backend cases are
         in backend-test-plan-inv.md (TC-INV-001 … 172, 242 … 261, 264 … 284, 286 … 293, 295); TC-INV-262, 263 were added
         at gate-analysis revise 1, TC-INV-285 at gate-analysis revise 2 (G2), TC-INV-294 at gate-analysis revise 5 (G6)
ADRs   : raised here ADR-INV-037 (track placement and package), ADR-INV-038 (asserted texts and codes),
         ADR-INV-039 (acceptance of F3-SCR-INV-012 and F4-SCR-INV-011 — repair round 1) — ACCEPTED, non-breaking · cited from P3.2: ADR-INV-033 (reads outside the document), ADR-INV-034 (count commits per
         line), ADR-INV-035 (browser reads the sheet — barcode only in v1), ADR-INV-036 (warehouse label for a
         Seller/Cashier read from the row — closed at gate-analysis revise 1; TC-INV-262 asserts it) · cited from
         gate-analysis revise 2: ADR-INV-046 (a line refusal names its line in `details`; TC-INV-285 asserts it),
         ADR-INV-047 (OPENING_STOCK not offered on the adjustment screen), ADR-INV-033 as amended (INV lookup values
         through API-INV-052)
══════════════════════════════════════════════════════════════════

## How to read this plan

- **Framework.** `profile.stack.testing` is agnostic. Each TC block below is the whole contract. The consumer
  repository chooses its tool and turns each block into one test.
- **Server.** The screens run against a mock server that serves `api-spec-inv.yaml`, including the INV lookup values
  (API-INV-052). The CAT and session fixtures come from ADR-INV-033. "The server answers X" in Preconditions sets the mock's answer for that call.
- **Texts.** A refusal is asserted by the text the frontend plan binds to its code ("text: RULE-INV-nnn message
  (SRS)" = the SRS A5 message, ar and en). `Expected` writes it as `ar «…» · en «…»`, copied from the SRS with its
  placeholders filled. Each text is asserted in both languages, by switching the UI language. A screen label (a
  chip, a status, a lookup name) is written `«…» / "…"` and is not a refusal text.
- **Routes.** Each TC cites the screen (`SCR-INV-*`) and its route. Drawers are search params (`entry`, `line`,
  `move`, `variant`).
- **Catalogue data.** Every lookup value a case names is seeded by INV. No case needs host data.





## TC TRACEABILITY INDEX

Generated from the TC blocks above (and, for the AC column of the other track, from backend-test-plan-inv.md).

### AC → TC

| AC | TC (backend) | TC (frontend) |
|---|---|---|
| AC-INV-001 | TC-INV-047 | — |
| AC-INV-002 | TC-INV-048 | — |
| AC-INV-003 | TC-INV-049 | — |
| AC-INV-004 | TC-INV-001 | TC-INV-173 |
| AC-INV-005 | TC-INV-050, TC-INV-269, TC-INV-273 | — |
| AC-INV-006 | TC-INV-051, TC-INV-276 | — |
| AC-INV-007 | TC-INV-002 | TC-INV-174 |
| AC-INV-008 | TC-INV-003, TC-INV-252 | TC-INV-175 |
| AC-INV-009 | TC-INV-052 | — |
| AC-INV-010 | TC-INV-053, TC-INV-248 | — |
| AC-INV-011 | TC-INV-004 | TC-INV-176 |
| AC-INV-012 | TC-INV-054 | — |
| AC-INV-013 | TC-INV-055 | TC-INV-177 |
| AC-INV-014 | — | TC-INV-178 |
| AC-INV-015 | — | TC-INV-179 |
| AC-INV-016 | TC-INV-056 | — |
| AC-INV-017 | TC-INV-005 | — |
| AC-INV-018 | TC-INV-057 | TC-INV-213 |
| AC-INV-019 | TC-INV-058 | — |
| AC-INV-020 | TC-INV-006, TC-INV-274 | — |
| AC-INV-021 | TC-INV-007, TC-INV-274 | — |
| AC-INV-022 | TC-INV-059 | — |
| AC-INV-023 | TC-INV-060 | — |
| AC-INV-024 | TC-INV-061 | — |
| AC-INV-025 | TC-INV-062 | — |
| AC-INV-026 | TC-INV-008 | TC-INV-180 |
| AC-INV-027 | TC-INV-063, TC-INV-273 | — |
| AC-INV-028 | TC-INV-064 | — |
| AC-INV-029 | TC-INV-009 | TC-INV-181 |
| AC-INV-030 | TC-INV-010 | TC-INV-182 |
| AC-INV-031 | TC-INV-065 | — |
| AC-INV-032 | TC-INV-066, TC-INV-272, TC-INV-282 | — |
| AC-INV-033 | TC-INV-067 | — |
| AC-INV-034 | — | TC-INV-183 |
| AC-INV-035 | — | TC-INV-184 |
| AC-INV-036 | TC-INV-068, TC-INV-272 | — |
| AC-INV-037 | TC-INV-069 | TC-INV-214 |
| AC-INV-038 | TC-INV-070 | — |
| AC-INV-039 | TC-INV-071 | — |
| AC-INV-040 | TC-INV-072, TC-INV-244 | — |
| AC-INV-041 | TC-INV-073 | — |
| AC-INV-042 | TC-INV-074, TC-INV-251 | — |
| AC-INV-043 | TC-INV-075 | — |
| AC-INV-044 | TC-INV-076, TC-INV-259 | TC-INV-218, TC-INV-219, TC-INV-220, TC-INV-221, TC-INV-262, TC-INV-294 |
| AC-INV-045 | TC-INV-077 | — |
| AC-INV-046 | TC-INV-078 | — |
| AC-INV-047 | TC-INV-079 | — |
| AC-INV-048 | TC-INV-080 | — |
| AC-INV-049 | TC-INV-011 | — |
| AC-INV-050 | TC-INV-081 | — |
| AC-INV-051 | TC-INV-082, TC-INV-293 | — |
| AC-INV-052 | TC-INV-083, TC-INV-255 | — |
| AC-INV-053 | TC-INV-012 | — |
| AC-INV-054 | TC-INV-084 | — |
| AC-INV-055 | TC-INV-085 | — |
| AC-INV-056 | TC-INV-086, TC-INV-267 | — |
| AC-INV-057 | TC-INV-087 | — |
| AC-INV-058 | TC-INV-013 | — |
| AC-INV-059 | TC-INV-088 | — |
| AC-INV-060 | TC-INV-014 | — |
| AC-INV-061 | TC-INV-015 | — |
| AC-INV-062 | TC-INV-089, TC-INV-260 | TC-INV-263 |
| AC-INV-063 | TC-INV-090 | — |
| AC-INV-064 | TC-INV-091, TC-INV-245, TC-INV-278 | — |
| AC-INV-065 | TC-INV-092 | — |
| AC-INV-066 | TC-INV-093 | — |
| AC-INV-067 | TC-INV-094 | — |
| AC-INV-068 | TC-INV-095 | — |
| AC-INV-069 | TC-INV-096 | TC-INV-238, TC-INV-239, TC-INV-240 |
| AC-INV-070 | TC-INV-016 | — |
| AC-INV-071 | TC-INV-017 | TC-INV-241 |
| AC-INV-072 | TC-INV-097 | — |
| AC-INV-073 | TC-INV-018, TC-INV-266 | — |
| AC-INV-074 | TC-INV-098 | — |
| AC-INV-075 | TC-INV-019 | — |
| AC-INV-076 | TC-INV-099, TC-INV-257 | TC-INV-215 |
| AC-INV-077 | TC-INV-100 | — |
| AC-INV-078 | TC-INV-020 | — |
| AC-INV-079 | TC-INV-021 | TC-INV-185 |
| AC-INV-080 | TC-INV-101 | — |
| AC-INV-081 | TC-INV-275 | TC-INV-186 |
| AC-INV-082 | TC-INV-102 | TC-INV-224, TC-INV-225 |
| AC-INV-083 | TC-INV-103, TC-INV-292 | TC-INV-187 |
| AC-INV-084 | TC-INV-104 | — |
| AC-INV-085 | TC-INV-105, TC-INV-280 | TC-INV-226, TC-INV-227 |
| AC-INV-086 | TC-INV-106, TC-INV-270 | — |
| AC-INV-087 | TC-INV-107, TC-INV-246, TC-INV-253, TC-INV-264 | TC-INV-285 |
| AC-INV-088 | TC-INV-022 | TC-INV-188 |
| AC-INV-089 | TC-INV-108, TC-INV-247 | — |
| AC-INV-090 | TC-INV-023 | TC-INV-189 |
| AC-INV-091 | TC-INV-024 | TC-INV-190 |
| AC-INV-092 | TC-INV-109 | — |
| AC-INV-093 | TC-INV-025, TC-INV-254 | TC-INV-191 |
| AC-INV-094 | TC-INV-026 | — |
| AC-INV-095 | TC-INV-110 | — |
| AC-INV-096 | TC-INV-111 | — |
| AC-INV-097 | TC-INV-112 | TC-INV-228, TC-INV-229 |
| AC-INV-098 | TC-INV-027 | TC-INV-192 |
| AC-INV-099 | TC-INV-113 | — |
| AC-INV-100 | — | TC-INV-216 |
| AC-INV-101 | TC-INV-028 | TC-INV-193 |
| AC-INV-102 | TC-INV-114, TC-INV-258, TC-INV-265, TC-INV-289, TC-INV-291 | — |
| AC-INV-103 | TC-INV-029 | TC-INV-194 |
| AC-INV-104 | TC-INV-115 | — |
| AC-INV-105 | TC-INV-116 | — |
| AC-INV-106 | TC-INV-030 | TC-INV-195 |
| AC-INV-107 | TC-INV-117 | TC-INV-230, TC-INV-231 |
| AC-INV-108 | TC-INV-118, TC-INV-256 | — |
| AC-INV-109 | TC-INV-031 | TC-INV-196 |
| AC-INV-110 | TC-INV-119 | — |
| AC-INV-111 | TC-INV-032 | TC-INV-197 |
| AC-INV-112 | TC-INV-120 | — |
| AC-INV-113 | TC-INV-033, TC-INV-281, TC-INV-284 | TC-INV-198 |
| AC-INV-114 | TC-INV-121 | — |
| AC-INV-115 | TC-INV-034 | — |
| AC-INV-116 | TC-INV-122 | — |
| AC-INV-117 | TC-INV-123 | — |
| AC-INV-118 | TC-INV-035 | — |
| AC-INV-119 | TC-INV-124, TC-INV-249 | — |
| AC-INV-120 | TC-INV-036 | — |
| AC-INV-121 | TC-INV-125, TC-INV-271, TC-INV-279 | — |
| AC-INV-122 | TC-INV-126 | TC-INV-232, TC-INV-233 |
| AC-INV-123 | TC-INV-127 | — |
| AC-INV-124 | TC-INV-128 | — |
| AC-INV-125 | TC-INV-129 | — |
| AC-INV-126 | TC-INV-037, TC-INV-279 | — |
| AC-INV-127 | TC-INV-038 | — |
| AC-INV-128 | TC-INV-130 | TC-INV-217 |
| AC-INV-129 | TC-INV-131 | TC-INV-199 |
| AC-INV-130 | TC-INV-132 | TC-INV-200 |
| AC-INV-131 | TC-INV-133, TC-INV-283 | — |
| AC-INV-132 | TC-INV-134 | TC-INV-201 |
| AC-INV-133 | TC-INV-039 | TC-INV-202 |
| AC-INV-134 | TC-INV-135 | — |
| AC-INV-135 | TC-INV-136 | TC-INV-203, TC-INV-222, TC-INV-223 |
| AC-INV-136 | TC-INV-040, TC-INV-268 | — |
| AC-INV-137 | TC-INV-137 | — |
| AC-INV-138 | TC-INV-138 | TC-INV-234, TC-INV-235 |
| AC-INV-139 | TC-INV-041 | TC-INV-204 |
| AC-INV-140 | TC-INV-139 | TC-INV-236, TC-INV-237 |
| AC-INV-141 | TC-INV-042 | TC-INV-205 |
| AC-INV-142 | TC-INV-043, TC-INV-250 | TC-INV-206 |
| AC-INV-143 | TC-INV-044 | TC-INV-207 |
| AC-INV-144 | TC-INV-045 | — |
| AC-INV-145 | TC-INV-140 | — |
| AC-INV-146 | TC-INV-141 | — |
| AC-INV-147 | TC-INV-046 | — |
| AC-INV-148 | TC-INV-142, TC-INV-242, TC-INV-243 | — |
| AC-INV-149 | TC-INV-143 | — |
| AC-INV-150 | — | TC-INV-208 |
| AC-INV-151 | — | TC-INV-209 |
| AC-INV-152 | TC-INV-144 | TC-INV-210 |
| AC-INV-153 | TC-INV-145, TC-INV-261 | TC-INV-211 |
| AC-INV-154 | TC-INV-146, TC-INV-277 | TC-INV-212 |
| AC-INV-155 | TC-INV-286 | — |
| AC-INV-156 | TC-INV-286 | — |
| AC-INV-157 | TC-INV-287 | — |
| AC-INV-158 | TC-INV-288 | — |
| AC-INV-159 | TC-INV-290 | — |
| AC-INV-160 | TC-INV-295 | — |

### REQ → TC

| REQ | TC |
|---|---|
| REQ-INV-003 | TC-INV-173 |
| REQ-INV-006 | TC-INV-174, TC-INV-175 |
| REQ-INV-008 | TC-INV-176 |
| REQ-INV-010 | TC-INV-177 |
| REQ-INV-011 | TC-INV-178 |
| REQ-INV-012 | TC-INV-179 |
| REQ-INV-015 | TC-INV-213 |
| REQ-INV-022 | TC-INV-180 |
| REQ-INV-025 | TC-INV-181, TC-INV-182 |
| REQ-INV-029 | TC-INV-183 |
| REQ-INV-030 | TC-INV-184 |
| REQ-INV-032 | TC-INV-214 |
| REQ-INV-039 | TC-INV-218, TC-INV-219, TC-INV-220, TC-INV-221, TC-INV-262, TC-INV-294 |
| REQ-INV-056 | TC-INV-263 |
| REQ-INV-063 | TC-INV-238, TC-INV-239, TC-INV-240 |
| REQ-INV-065 | TC-INV-241 |
| REQ-INV-070 | TC-INV-215 |
| REQ-INV-072 | TC-INV-185 |
| REQ-INV-074 | TC-INV-186 |
| REQ-INV-075 | TC-INV-224, TC-INV-225 |
| REQ-INV-076 | TC-INV-187 |
| REQ-INV-078 | TC-INV-226, TC-INV-227 |
| REQ-INV-080 | TC-INV-188, TC-INV-285 |
| REQ-INV-082 | TC-INV-189 |
| REQ-INV-083 | TC-INV-190 |
| REQ-INV-084 | TC-INV-191 |
| REQ-INV-088 | TC-INV-192, TC-INV-228, TC-INV-229 |
| REQ-INV-090 | TC-INV-216 |
| REQ-INV-091 | TC-INV-193 |
| REQ-INV-093 | TC-INV-194 |
| REQ-INV-096 | TC-INV-195 |
| REQ-INV-097 | TC-INV-230, TC-INV-231 |
| REQ-INV-098 | TC-INV-196 |
| REQ-INV-099 | TC-INV-197 |
| REQ-INV-101 | TC-INV-198 |
| REQ-INV-109 | TC-INV-232, TC-INV-233 |
| REQ-INV-114 | TC-INV-217 |
| REQ-INV-115 | TC-INV-199 |
| REQ-INV-116 | TC-INV-200 |
| REQ-INV-118 | TC-INV-201 |
| REQ-INV-119 | TC-INV-202 |
| REQ-INV-121 | TC-INV-203, TC-INV-222, TC-INV-223 |
| REQ-INV-124 | TC-INV-204, TC-INV-234, TC-INV-235 |
| REQ-INV-125 | TC-INV-205, TC-INV-236, TC-INV-237 |
| REQ-INV-126 | TC-INV-206 |
| REQ-INV-127 | TC-INV-207 |
| REQ-INV-134 | TC-INV-208 |
| REQ-INV-135 | TC-INV-209 |
| REQ-INV-136 | TC-INV-210 |
| REQ-INV-137 | TC-INV-211 |
| REQ-INV-138 | TC-INV-212 |

### SCR → TC

| SCR | TC |
|---|---|
| SCR-INV-001 | TC-INV-173, TC-INV-174, TC-INV-175, TC-INV-176, TC-INV-183, TC-INV-184, TC-INV-213, TC-INV-214 |
| SCR-INV-002 | TC-INV-180, TC-INV-181, TC-INV-182, TC-INV-214 |
| SCR-INV-003 | TC-INV-179, TC-INV-203, TC-INV-209, TC-INV-210, TC-INV-211, TC-INV-213, TC-INV-218, TC-INV-219, TC-INV-220, TC-INV-221, TC-INV-222, TC-INV-223, TC-INV-262, TC-INV-294 |
| SCR-INV-004 | TC-INV-187, TC-INV-210, TC-INV-213, TC-INV-224, TC-INV-225, TC-INV-263 |
| SCR-INV-005 | TC-INV-178, TC-INV-188, TC-INV-189, TC-INV-190, TC-INV-191, TC-INV-210, TC-INV-213, TC-INV-226, TC-INV-227, TC-INV-263, TC-INV-285 |
| SCR-INV-006 | TC-INV-192, TC-INV-193, TC-INV-194, TC-INV-195, TC-INV-210, TC-INV-216, TC-INV-228, TC-INV-229 |
| SCR-INV-007 | TC-INV-177, TC-INV-196, TC-INV-197, TC-INV-198, TC-INV-208, TC-INV-210, TC-INV-212, TC-INV-213, TC-INV-215, TC-INV-217, TC-INV-230, TC-INV-231 |
| SCR-INV-008 | TC-INV-199, TC-INV-200, TC-INV-210, TC-INV-217, TC-INV-232, TC-INV-233 |
| SCR-INV-009 | TC-INV-204, TC-INV-205, TC-INV-206, TC-INV-207, TC-INV-234, TC-INV-235, TC-INV-236, TC-INV-237 |
| SCR-INV-010 | TC-INV-185, TC-INV-186, TC-INV-215 |
| SCR-INV-011 | TC-INV-201, TC-INV-202, TC-INV-241 |
| SCR-INV-012 | TC-INV-215, TC-INV-238, TC-INV-239, TC-INV-240 |

### RULE / code → TC

| RULE / code | TC |
|---|---|
| RULE-INV-002 | TC-INV-173 |
| RULE-INV-003 | TC-INV-174 |
| RULE-INV-004 | TC-INV-175 |
| RULE-INV-006 | TC-INV-176 |
| RULE-INV-008 | TC-INV-177 |
| RULE-INV-011 | TC-INV-180 |
| RULE-INV-012 | TC-INV-181 |
| RULE-INV-013 | TC-INV-182 |
| RULE-INV-016 | TC-INV-178, TC-INV-189 |
| RULE-INV-018 | TC-INV-285 |
| RULE-INV-019 | TC-INV-190 |
| RULE-INV-026 | TC-INV-186, TC-INV-215 |
| RULE-INV-028 | TC-INV-185 |
| RULE-INV-029 | TC-INV-195 |
| RULE-INV-031 | TC-INV-188, TC-INV-194 |
| RULE-INV-032 | TC-INV-193, TC-INV-229 |
| RULE-INV-033 | TC-INV-198 |
| RULE-INV-034 | TC-INV-197 |
| RULE-INV-037 | TC-INV-191 |
| RULE-INV-038 | TC-INV-199 |
| RULE-INV-039 | TC-INV-217 |
| RULE-INV-041 | TC-INV-205 |
| RULE-INV-042 | TC-INV-206 |
| RULE-INV-043 | TC-INV-207 |
| RULE-INV-045 | TC-INV-204 |
| RULE-INV-047 | TC-INV-192 |
| RULE-INV-048 | TC-INV-196 |
| INV-403 | TC-INV-212 |
| INV-409-BRANCH-DEFAULT | TC-INV-181 |
| INV-409-BRANCH-IN-USE | TC-INV-182 |
| INV-409-BRANCH-NAME-TAKEN | TC-INV-180 |
| INV-409-DOCUMENT-CLOSED | TC-INV-195 |
| INV-409-DUAL-QUANTITY-IN-USE | TC-INV-207 |
| INV-409-LOT-TRACKING-IN-USE | TC-INV-206 |
| INV-409-NEGATIVE-STOCK | TC-INV-190 |
| INV-409-WAREHOUSE-DEFAULT | TC-INV-174 |
| INV-409-WAREHOUSE-DEFAULT-INACTIVE | TC-INV-176 |
| INV-409-WAREHOUSE-IN-USE | TC-INV-175 |
| INV-409-WAREHOUSE-NAME-TAKEN | TC-INV-173 |
| INV-422-BARCODE-UNKNOWN | TC-INV-193, TC-INV-229 |
| INV-422-BUNDLE-SETTINGS | TC-INV-204 |
| INV-422-COUNTED-NEGATIVE | TC-INV-192 |
| INV-422-DUPLICATE-LINE | TC-INV-196 |
| INV-422-HORIZON-RANGE | TC-INV-202 |
| INV-422-LOAD-ROW-VARIANT | TC-INV-185 |
| INV-422-LOAD-ROWS-INVALID | TC-INV-185 |
| INV-422-LOT-REQUIRED | TC-INV-191 |
| INV-422-MEASURE-UNIT-REQUIRED | TC-INV-205 |
| INV-422-NO-LINES | TC-INV-188, TC-INV-194 |
| INV-422-OPENING-COST-REQUIRED | TC-INV-186 |
| INV-422-REASON-REQUIRED | TC-INV-198 |
| INV-422-TRANSFER-WAREHOUSES | TC-INV-189 |
| INV-422-VARIANT-INACTIVE | TC-INV-285 |
| INV-422-ZERO-CHANGE | TC-INV-197 |

### UXD → TC

| UXD | TC |
|---|---|
| UXD-INV-001 | TC-INV-218, TC-INV-219 |
| UXD-INV-002 | TC-INV-220, TC-INV-221 |
| UXD-INV-003 | TC-INV-222, TC-INV-223 |
| UXD-INV-004 | TC-INV-224, TC-INV-225 |
| UXD-INV-005 | TC-INV-226, TC-INV-227 |
| UXD-INV-006 | TC-INV-228, TC-INV-229 |
| UXD-INV-007 | TC-INV-230, TC-INV-231 |
| UXD-INV-008 | TC-INV-232, TC-INV-233 |
| UXD-INV-009 | TC-INV-234, TC-INV-235 |
| UXD-INV-010 | TC-INV-236, TC-INV-237 |
| UXD-INV-011 | TC-INV-238, TC-INV-239 |

### Package → TC

| Package | TC |
|---|---|
| F2-SCR-INV-001 | TC-INV-174, TC-INV-175, TC-INV-176 |
| F2-SCR-INV-002 | TC-INV-181, TC-INV-182 |
| F2-SCR-INV-003 | TC-INV-219, TC-INV-221, TC-INV-223 |
| F2-SCR-INV-004 | TC-INV-225 |
| F2-SCR-INV-005 | TC-INV-188, TC-INV-190, TC-INV-227, TC-INV-285 |
| F2-SCR-INV-006 | TC-INV-193, TC-INV-194, TC-INV-195, TC-INV-229 |
| F2-SCR-INV-007 | TC-INV-197, TC-INV-231 |
| F2-SCR-INV-008 | TC-INV-200, TC-INV-233 |
| F2-SCR-INV-009 | TC-INV-204, TC-INV-206, TC-INV-207, TC-INV-235, TC-INV-237 |
| F2-SCR-INV-010 | TC-INV-185 |
| F2-SCR-INV-011 | TC-INV-201 |
| F2-SCR-INV-012 | TC-INV-239 |
| F3-SCR-INV-001 | TC-INV-173 |
| F3-SCR-INV-002 | TC-INV-180 |
| F3-SCR-INV-003 | TC-INV-218 |
| F3-SCR-INV-004 | TC-INV-224 |
| F3-SCR-INV-005 | TC-INV-189, TC-INV-191 |
| F3-SCR-INV-006 | TC-INV-192 |
| F3-SCR-INV-007 | TC-INV-196, TC-INV-198 |
| F3-SCR-INV-008 | TC-INV-232 |
| F3-SCR-INV-009 | TC-INV-205 |
| F3-SCR-INV-010 | TC-INV-186 |
| F3-SCR-INV-011 | TC-INV-202 |
| F3-SCR-INV-012 | TC-INV-238 |
| F4-SCR-INV-001 | TC-INV-183, TC-INV-184 |
| F4-SCR-INV-002 | TC-INV-214 |
| F4-SCR-INV-003 | TC-INV-179, TC-INV-203, TC-INV-209, TC-INV-220, TC-INV-222, TC-INV-262, TC-INV-294 |
| F4-SCR-INV-004 | TC-INV-187 |
| F4-SCR-INV-005 | TC-INV-178, TC-INV-226, TC-INV-263 |
| F4-SCR-INV-006 | TC-INV-216, TC-INV-228 |
| F4-SCR-INV-007 | TC-INV-177, TC-INV-208, TC-INV-217, TC-INV-230 |
| F4-SCR-INV-008 | TC-INV-199 |
| F4-SCR-INV-009 | TC-INV-234, TC-INV-236 |
| F4-SCR-INV-010 | TC-INV-215 |
| F4-SCR-INV-011 | TC-INV-241 |
| F4-SCR-INV-012 | TC-INV-240 |
| SEC-FE | TC-INV-210, TC-INV-211, TC-INV-212, TC-INV-213 |

## COVERAGE

| Measure | Result |
|---|---|
| AC covered (both tracks) | ✓ 160/160 · gaps: none |
| AC covered on this track | 57/160 (AC-INV-160 of gate-analysis revise 5 is backend case TC-INV-295; the rest on the other track; 50 ACs on both; AC-INV-155 … 158 of gate-analysis revise 3 are backend cases TC-INV-286 … 288, AC-INV-159 of revise 4 is backend case TC-INV-290) |
| REQ covered (both tracks) | ✓ 141/141 · gaps: none |
| REQ covered on this track | 51/141 |
| SCR covered | ✓ 12/12 · gaps: none |
| UXD covered | ✓ 11/11 (rendered + empty/failure each) · gaps: none |
| Split units with no TC | 13 of 50: F1-SCR-INV-001, F1-SCR-INV-002, F1-SCR-INV-003, F1-SCR-INV-004, F1-SCR-INV-005, F1-SCR-INV-006, F1-SCR-INV-007, F1-SCR-INV-008, F1-SCR-INV-009, F1-SCR-INV-010, F1-SCR-INV-011, F1-SCR-INV-012, ALIGN-FE · no AC, XM or UXD derives a case for them (ADR-INV-037); F3-SCR-INV-012 and F4-SCR-INV-011 carry TC-INV-238 and TC-INV-241 (ADR-INV-039) |
| Line refusals (gate-analysis revise 2) | TC-INV-285: a post refused for line 2 of 3 places the text on that line through `details[0].lineRef` (ADR-INV-046) |
| Pair figures on lot rows (gate-analysis revise 5) | TC-INV-294: reserved and available, figures of the Variant × warehouse pair, are shown once on the pair's first lot row and labelled per warehouse |
| Over-engineering guard | 73 TCs for 57 ACs + 11 UXD (ratio 1.07 ≤ 2) |

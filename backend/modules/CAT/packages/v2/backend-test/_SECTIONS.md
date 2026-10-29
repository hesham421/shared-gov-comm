<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# Backend test plan — Catalog / الكتالوج (CAT) — v2

Module : CAT · Version : v2 · Stage : P4 · Track : backend · Plan : test · Profile : commerce
Lane   : test-gen · questions forbidden · TC stream `TC-CAT-{seq:3}` shared with the frontend test plan (this file: TC-CAT-001 … TC-CAT-124, TC-CAT-183 … TC-CAT-193, TC-CAT-197 … TC-CAT-207, TC-CAT-211 … TC-CAT-219, TC-CAT-221 … TC-CAT-231, TC-CAT-233 … TC-CAT-235; frontend: TC-CAT-125 … TC-CAT-182, TC-CAT-194 … TC-CAT-196, TC-CAT-208 … TC-CAT-210, TC-CAT-220 and TC-CAT-232)

## Header

### Sources (generated current state)
| Input | File | Version | Use |
|---|---|---|---|
| srs | `_state/current-srs.md` | CAT v2 (P1) | derivation source: AC-CAT-001 … 171 (Given / When / Then), RULE-CAT-001 … 053 messages, lookups block |
| registry-srs | `_state/current-registry-srs.md` | CAT v2 (P1) | ID ranges (REQ 148 · AC 171 · RULE 53) |
| registry-db | `_state/current-registry-db.md` | CAT v2 (P2) | XM register `records: []` → no integration TC |
| backend-execution-plan | `_state/current-backend-execution-plan.md` | CAT v2 (P3.1) | API-CAT-001 … 056, Error Catalog codes, split units (Package) |
| frontend-execution-plan | `_state/current-frontend-execution-plan.md` | CAT v2 (P3.2) | cross-check of the track split (ADR-CAT-052 §1) |
| api-spec | `_state/current-api-spec.yaml` (`api-spec-cat.yaml`, OpenAPI 3.1.0, 2.0.0) | CAT v2 (P3.1) | shape of every operation a TC asserts on — read by `x-api-id` |
| dependency-graph | `_state/current-dependency-graph.md` | CAT v2 | CAT is tier 1, no outbound edge |

### Delta
The current state holds no earlier test plan, so every TC is ADDED. The TC stream starts at 001. Within v2, the third gate-analysis revise ADDED TC-CAT-211 … TC-CAT-216 and MODIFIED TC-CAT-003, TC-CAT-020 and TC-CAT-203 (the RULE-CAT-003 message names the existing Item's code — G12) and TC-CAT-185, TC-CAT-186 and TC-CAT-187 (each now repairs the Item and reactivates it — G3, ADR-CAT-068). The fourth gate-analysis revise ADDED TC-CAT-217 (an import past its 120-second limit is rolled back whole — G4, REQ-CAT-139, ADR-CAT-071) and MODIFIED TC-CAT-188 (the GTIN of a deactivated Item stays active and refuses another Variant — G6). The fifth gate-analysis revise ADDED TC-CAT-218 (an image file held in the private storage is refused, and one public file may back two images — G1) and MODIFIED TC-CAT-184 (a second row: an empty `selections`, or an attribute with no value, answers CAT-400-REQUEST-INVALID — G3). The sixth gate-analysis revise ADDED TC-CAT-219 (reactivation refused when another active Item took the name — G2, AC-CAT-162), TC-CAT-221 and TC-CAT-222 (the default Variant moves on deactivation and on generation — G3, ADR-CAT-072) and MODIFIED TC-CAT-048 (scans 2001230012342, the code of the precondition's SCALE barcode — G8), TC-CAT-199 (the read of the deactivated Variant returns no active barcode — G1), TC-CAT-185 and TC-CAT-200 (each also asserts the one active default Variant — G3). The seventh gate-analysis revise MODIFIED TC-CAT-083 (empty baseUomCode / taxClassCode cells take PIECE / STANDARD, a filled cell is kept — G6), TC-CAT-184 (row 3: four attributes answer CAT-400-REQUEST-INVALID — G2), TC-CAT-217 (fault point after the first 500 rows are saved, and an undelayed run that measures the 60-second target — G3, ADR-CAT-074) and re-pointed TC-CAT-185, 186, 187, 212, 219, 221 and 222 to the REQs split from REQ-CAT-025, 130 and 135 (REQ-CAT-140 … 147 — G7); it ADDED no TC. The eighth gate-analysis revise ADDED TC-CAT-223 (a request that contradicts itself or names a record outside its parent answers CAT-400-REQUEST-INVALID and writes nothing — G2) and MODIFIED TC-CAT-056 and TC-CAT-218 (the image type, size and storage are refused by the FILE category `CAT_ITEM_IMAGE` at upload; CAT answers CAT-404-NOT-FOUND for a reference with no public URL and RULE-CAT-038 for a 21st image — G1, ADR-CAT-076). The ninth gate-analysis revise ADDED TC-CAT-224 (every committed Category change publishes one catalogue-change event — G1, AC-CAT-165, ADR-CAT-077), TC-CAT-225 (the Arabic normalisation keeps Arabic-Indic digits and still folds letter variants — G2) and TC-CAT-226 (an import row whose GTIN begins with an active scale prefix is rejected — G3, AC-CAT-166); it MODIFIED no TC. The tenth gate-analysis revise ADDED TC-CAT-227 … TC-CAT-230 (the parallel-request cases of four guards the backend plan states: a barcode add and a scale-layout create, an Item unit deactivation and a barcode add, a Variant generation and a value deactivation, an Item save and its Category's deactivation — G2, AC-CAT-167 … 170) and TC-CAT-231 (warehouse staff generate the missing internal barcode from the label screen — G1, AC-CAT-171, ADR-CAT-078), and MODIFIED TC-CAT-027 and TC-CAT-039 (boundary rows on the Bundle quantity and the conversion factor — G3) and TC-CAT-223 (rows 11 … 15: a PUT on an inactive Item unit, removed image, inactive Category, attribute or value answers CAT-400-REQUEST-INVALID, and the removed image does not clear the active primary — G6). The eleventh gate-analysis revise ADDED TC-CAT-233 (a Variant whose only barcode is a SCALE code, or a carton barcode, gets no label — G1, AC-CAT-172, ADR-CAT-079), TC-CAT-234 (a piece label prints the base-unit barcode, not an earlier carton barcode — G1, AC-CAT-173) and TC-CAT-235 (every Bundle line returns the base unit of its quantity — G2, AC-CAT-174, ADR-CAT-080), and MODIFIED TC-CAT-053 and TC-CAT-231 (the RULE-CAT-037 message names the base unit), TC-CAT-111 and TC-CAT-112 (the label barcode is a GTIN or INTERNAL barcode in the base unit) and TC-CAT-095 (each line also returns componentBaseUomCode). No id is renumbered or REMOVED.

### Framework
`profile.stack.testing` is **agnostic**. Each block below is the whole contract: the consumer repo chooses its tool and turns each TC into a test. This plan names no framework, annotation or file layout.

### Open ADRs
- ADR-CAT-052 (this stage) is ACCEPTED and non-breaking. It covers the track split, the package binding, the host-data creation surface, catalogue isolation, fields the API document does not expose, and fault injection.
- ADR-CAT-053 (this stage, analyze round 2) is ACCEPTED and non-breaking. It replaces the unseeded AC values BAG and H87 by seeded values in TC-CAT-063, 120, 121, 122, so that those cases stay on seeded data, and records the frontend plan's unbound refusal texts.
- The TCs also cite ADR-CAT-041, 044, 045, 047 and 048 (P1/P3.1), and, from the gate-analysis revise, ADR-CAT-056 (files through the given FILE endpoints), ADR-CAT-058 (tenant provisioning), ADR-CAT-059 (reactivation invariants), ADR-CAT-060 (unit used by barcodes) and ADR-CAT-062 (Arabic normalisation). From the gate-analysis round 2 they cite ADR-CAT-063 (inactive parent refuses child writes; a Variant's barcodes follow it), ADR-CAT-064 (layout keeps its scale code length) and ADR-CAT-065 (CAT lookup keys served through API-CAT-052). From the gate-analysis round 3 they cite ADR-CAT-047 as amended (Category tree lock on deactivation), ADR-CAT-068 (three repairs stay allowed on an inactive Item) and ADR-CAT-069 (reactivation of Categories, Variant attributes and values). From the gate-analysis round 6 they cite ADR-CAT-072 (the default Variant moves), and from round 7 ADR-CAT-074 (the item import saves entities; its 60-second figure is a measured target). From round 8 they cite ADR-CAT-076 (image type, size and public storage enforced by the FILE category `CAT_ITEM_IMAGE`; the import's byte read waits on the core), and from round 9 ADR-CAT-077 (Category changes publish catalogue-change events — TC-CAT-224). From round 10 they cite ADR-CAT-078 (the barcode label screen generates the missing internal barcode — TC-CAT-231), and from round 11 ADR-CAT-079 (a label shows only an active GTIN or INTERNAL barcode in the base unit — TC-CAT-233, 234) and ADR-CAT-080 (a Bundle component quantity is in the component Item's base unit — TC-CAT-235). None of them is BLOCKED.
- Core dependency (ADR-CAT-076): TC-CAT-017 … 020, 083, 084, 217 and 226 exercise API-CAT-049, whose byte read and type / size check depend on `CORE-GAP-file-document-metadata-read.md`. They stand as written and run once the core publishes that read; until then they are reported as blocked by the core gap, not as failures.
- Host values of an open lookup key (CAT_BRAND) are created and deactivated through the given mdl HTTP surface: the CAT_BRAND type is found by `POST /api/v1/mdl/lookup-types/search`, a value is added by `POST /api/v1/mdl/lookup-types/{id}/values` and deactivated by `DELETE /api/v1/mdl/lookup-values/{id}` (SRS A6). Each Host data line names the call it uses.

### Shared precondition S0 (applies to every TC; each TC adds only what it states)
- The caller is a user of preset role Owner holding every `PERM_CAT_*` permission, with a valid bearer token.
- The capability flags `variants` and `bundles` are on and readable through the given `SettingsApi` (FeatureFlags).
- The CAT bootstrap is present: the 10 lookup keys with their seeded values, the series CAT_ITEM / CAT_VARIANT / CAT_INTERNAL_BARCODE, and the 8 seeded Unit e-invoice codes (D-12). Every test tenant receives them at its provisioning: the given copies the lookups and series, and `CatTenantProvisioningContributor` copies the Unit e-invoice codes (ADR-CAT-058). TC-CAT-193 proves this for a newly provisioned tenant.
- An uploaded file (an image for API-CAT-018, an xlsx for API-CAT-049) is first uploaded through the given erp-core FILE endpoint: images to the FILE category `CAT_ITEM_IMAGE` (allowPublic, PNG / JPEG / WEBP, at most 5 MB — registered by CAT for every tenant, ADR-CAT-076), import files to the private storage. The TC then sends the returned `fileDocumentRef` in the JSON body of the CAT call. "POST the file" below means these two steps (ADR-CAT-056).
- The TC runs in a tenant of its own, and no data is shared between TCs (ADR-CAT-052 §4).
- Error bodies are read as `LocalizedException → {code, messageAr, messageEn}`. Success notices are read from `ApiResponse<T>` `code` / `messageAr` / `messageEn` (ADR-CAT-048).
- "Owner" below is that user. Every message is asserted in ar and en, copied from the SRS.



## PHASE INT-XM — not present

CAT has no `XM-*` edge. The registry-db XM register is `records: []`, SRS A8 has `consumes: []`, and the backend plan's last phase `INT-C` carries no block. The integration phase is therefore absent from this plan, not written as an empty block. Inbound use of CAT by other modules (INV, PRC, PUR, SLS, SFR, TAX, INS — dependency graph) is those modules' own edges, and they test them.

## TC TRACEABILITY INDEX

### AC → TC (and REQ → TC)
Read REQ → TC from the REQ column. REQ-CAT-016 → TC-CAT-006, 007. REQ-CAT-041 → TC-CAT-019, 020, 226. REQ-CAT-007 → TC-CAT-003, 203, 225. REQ-CAT-008 → TC-CAT-069, 224. REQ-CAT-086 → TC-CAT-105, 188. REQ-CAT-101 → TC-CAT-111, 189. REQ-CAT-135 → TC-CAT-186, 198, 212. REQ-CAT-141 → TC-CAT-185, 222. REQ-CAT-145 → TC-CAT-185, 212. REQ-CAT-146 → TC-CAT-186, 212. REQ-CAT-147 → TC-CAT-187, 212. REQ-CAT-038 → TC-CAT-083 (also the empty-cell defaults). Since the seventh gate-analysis revise, REQ-CAT-130 → TC-CAT-185, REQ-CAT-142 → TC-CAT-186, REQ-CAT-143 → TC-CAT-187, REQ-CAT-144 → TC-CAT-219, REQ-CAT-140 → TC-CAT-221 and REQ-CAT-025 → TC-CAT-077 (G7). REQ-CAT-136 → TC-CAT-213, 214. REQ-CAT-005 → TC-CAT-001, 188 (188 also asserts that the barcodes stay active). REQ-CAT-110 → TC-CAT-056, 218. Since the tenth gate-analysis revise, REQ-CAT-091 → TC-CAT-047, 227; REQ-CAT-128 → TC-CAT-183, 228; REQ-CAT-054 → TC-CAT-025, 229; REQ-CAT-072 → TC-CAT-035, 230; REQ-CAT-148 → TC-CAT-231. Since the eleventh gate-analysis revise, REQ-CAT-104 → TC-CAT-053, 233; REQ-CAT-102 → TC-CAT-112, 234; REQ-CAT-058 → TC-CAT-095, 235. Every other REQ maps to one TC.

| AC | REQ | TC |
|---|---|---|
| AC-CAT-001 | REQ-CAT-001 | TC-CAT-065 |
| AC-CAT-002 | REQ-CAT-002 | TC-CAT-066 |
| AC-CAT-003 | REQ-CAT-003 | TC-CAT-067 |
| AC-CAT-004 | REQ-CAT-004 | TC-CAT-068 |
| AC-CAT-005 | REQ-CAT-005 | TC-CAT-001 |
| AC-CAT-006 | REQ-CAT-006 | TC-CAT-002 |
| AC-CAT-007 | REQ-CAT-007 | TC-CAT-003, 225 (normalisation) |
| AC-CAT-008 | REQ-CAT-008 | TC-CAT-069 |
| AC-CAT-009 | REQ-CAT-009 | TC-CAT-004 |
| AC-CAT-010 | REQ-CAT-010 | TC-CAT-070 |
| AC-CAT-011 | REQ-CAT-011 | TC-CAT-071 |
| AC-CAT-012 | REQ-CAT-012 | TC-CAT-072 |
| AC-CAT-013 | REQ-CAT-013 | TC-CAT-005 |
| AC-CAT-014 | REQ-CAT-014 | frontend only — TC-CAT-128 |
| AC-CAT-015 | REQ-CAT-015 | frontend only — TC-CAT-129 |
| AC-CAT-016 | REQ-CAT-016 | TC-CAT-006 |
| AC-CAT-017 | REQ-CAT-016 | TC-CAT-007 |
| AC-CAT-018 | REQ-CAT-017 | TC-CAT-073 |
| AC-CAT-019 | REQ-CAT-018 | TC-CAT-008 |
| AC-CAT-020 | REQ-CAT-019 | TC-CAT-009 |
| AC-CAT-021 | REQ-CAT-020 | TC-CAT-074 |
| AC-CAT-022 | REQ-CAT-021 | TC-CAT-075 |
| AC-CAT-023 | REQ-CAT-022 | TC-CAT-010 |
| AC-CAT-024 | REQ-CAT-023 | TC-CAT-011 |
| AC-CAT-025 | REQ-CAT-024 | TC-CAT-076 |
| AC-CAT-026 | REQ-CAT-025 | TC-CAT-077 |
| AC-CAT-027 | REQ-CAT-026 | frontend only — TC-CAT-132 |
| AC-CAT-028 | REQ-CAT-027 | TC-CAT-012 |
| AC-CAT-029 | REQ-CAT-028 | TC-CAT-078 |
| AC-CAT-030 | REQ-CAT-029 | TC-CAT-079 |
| AC-CAT-031 | REQ-CAT-030 | TC-CAT-013 |
| AC-CAT-032 | REQ-CAT-031 | TC-CAT-014 |
| AC-CAT-033 | REQ-CAT-032 | TC-CAT-080 |
| AC-CAT-034 | REQ-CAT-033 | TC-CAT-081 |
| AC-CAT-035 | REQ-CAT-034 | TC-CAT-015 |
| AC-CAT-036 | REQ-CAT-035 | TC-CAT-016 |
| AC-CAT-037 | REQ-CAT-036 | TC-CAT-082 |
| AC-CAT-038 | REQ-CAT-037 | TC-CAT-017 |
| AC-CAT-039 | REQ-CAT-038 | TC-CAT-083 |
| AC-CAT-040 | REQ-CAT-039 | TC-CAT-084 |
| AC-CAT-041 | REQ-CAT-040 | TC-CAT-018 |
| AC-CAT-042 | REQ-CAT-041 | TC-CAT-019 |
| AC-CAT-043 | REQ-CAT-041 | TC-CAT-020 |
| AC-CAT-044 | REQ-CAT-042 | TC-CAT-085 |
| AC-CAT-045 | REQ-CAT-043 | TC-CAT-086, 223 (update of an inactive attribute) |
| AC-CAT-046 | REQ-CAT-044 | TC-CAT-087 |
| AC-CAT-047 | REQ-CAT-045 | TC-CAT-088 |
| AC-CAT-048 | REQ-CAT-046 | TC-CAT-089, 223 (update of an inactive value) |
| AC-CAT-049 | REQ-CAT-047 | TC-CAT-090 |
| AC-CAT-050 | REQ-CAT-048 | TC-CAT-091 |
| AC-CAT-051 | REQ-CAT-049 | TC-CAT-092, 223 (malformed request) |
| AC-CAT-052 | REQ-CAT-050 | TC-CAT-021 |
| AC-CAT-053 | REQ-CAT-051 | TC-CAT-022 |
| AC-CAT-054 | REQ-CAT-052 | TC-CAT-023 |
| AC-CAT-055 | REQ-CAT-053 | TC-CAT-024 |
| AC-CAT-056 | REQ-CAT-054 | TC-CAT-025 |
| AC-CAT-057 | REQ-CAT-055 | TC-CAT-026 |
| AC-CAT-058 | REQ-CAT-056 | TC-CAT-093, 223 (malformed request) |
| AC-CAT-059 | REQ-CAT-057 | TC-CAT-094, 223 (malformed request) |
| AC-CAT-060 | REQ-CAT-058 | TC-CAT-095 |
| AC-CAT-061 | REQ-CAT-059 | TC-CAT-027 |
| AC-CAT-062 | REQ-CAT-060 | TC-CAT-028 |
| AC-CAT-063 | REQ-CAT-061 | TC-CAT-029 |
| AC-CAT-064 | REQ-CAT-062 | TC-CAT-030 |
| AC-CAT-065 | REQ-CAT-063 | TC-CAT-031 |
| AC-CAT-066 | REQ-CAT-064 | TC-CAT-096 |
| AC-CAT-067 | REQ-CAT-065 | TC-CAT-097, 223 (update of an inactive Category) |
| AC-CAT-068 | REQ-CAT-066 | TC-CAT-098 |
| AC-CAT-069 | REQ-CAT-067 | TC-CAT-099 |
| AC-CAT-070 | REQ-CAT-068 | TC-CAT-100 |
| AC-CAT-071 | REQ-CAT-069 | TC-CAT-032 |
| AC-CAT-072 | REQ-CAT-070 | TC-CAT-033 |
| AC-CAT-073 | REQ-CAT-071 | TC-CAT-034 |
| AC-CAT-074 | REQ-CAT-072 | TC-CAT-035 |
| AC-CAT-075 | REQ-CAT-073 | frontend only — TC-CAT-137 |
| AC-CAT-076 | REQ-CAT-074 | TC-CAT-207 (frontend half: TC-CAT-138) |
| AC-CAT-077 | REQ-CAT-075 | TC-CAT-036 |
| AC-CAT-078 | REQ-CAT-076 | TC-CAT-037 (frontend half: TC-CAT-139) |
| AC-CAT-079 | REQ-CAT-077 | TC-CAT-101 |
| AC-CAT-080 | REQ-CAT-078 | TC-CAT-102, 223 (update of an inactive Item unit) |
| AC-CAT-081 | REQ-CAT-079 | TC-CAT-038 |
| AC-CAT-082 | REQ-CAT-080 | TC-CAT-039 |
| AC-CAT-083 | REQ-CAT-081 | TC-CAT-040 |
| AC-CAT-084 | REQ-CAT-082 | TC-CAT-041 |
| AC-CAT-085 | REQ-CAT-083 | TC-CAT-103, 223 (malformed request) |
| AC-CAT-086 | REQ-CAT-084 | TC-CAT-104 |
| AC-CAT-087 | REQ-CAT-085 | TC-CAT-042 |
| AC-CAT-088 | REQ-CAT-086 | TC-CAT-105 |
| AC-CAT-089 | REQ-CAT-087 | TC-CAT-043 |
| AC-CAT-090 | REQ-CAT-088 | TC-CAT-044 |
| AC-CAT-091 | REQ-CAT-089 | TC-CAT-045 |
| AC-CAT-092 | REQ-CAT-090 | TC-CAT-046 |
| AC-CAT-093 | REQ-CAT-091 | TC-CAT-047 |
| AC-CAT-094 | REQ-CAT-092 | TC-CAT-106 |
| AC-CAT-095 | REQ-CAT-093 | TC-CAT-107 |
| AC-CAT-096 | REQ-CAT-094 | TC-CAT-048 |
| AC-CAT-097 | REQ-CAT-095 | TC-CAT-108 |
| AC-CAT-098 | REQ-CAT-096 | TC-CAT-109 |
| AC-CAT-099 | REQ-CAT-097 | TC-CAT-110 |
| AC-CAT-100 | REQ-CAT-098 | TC-CAT-049 |
| AC-CAT-101 | REQ-CAT-099 | TC-CAT-050 |
| AC-CAT-102 | REQ-CAT-100 | TC-CAT-051 |
| AC-CAT-103 | REQ-CAT-101 | TC-CAT-111 |
| AC-CAT-104 | REQ-CAT-102 | TC-CAT-112 |
| AC-CAT-105 | REQ-CAT-103 | TC-CAT-052 |
| AC-CAT-106 | REQ-CAT-104 | TC-CAT-053 |
| AC-CAT-107 | REQ-CAT-105 | TC-CAT-113 |
| AC-CAT-108 | REQ-CAT-106 | TC-CAT-054, 223 (update of a removed image) |
| AC-CAT-109 | REQ-CAT-107 | TC-CAT-114 |
| AC-CAT-110 | REQ-CAT-108 | TC-CAT-055 |
| AC-CAT-111 | REQ-CAT-109 | TC-CAT-115 |
| AC-CAT-112 | REQ-CAT-110 | TC-CAT-056, 218 |
| AC-CAT-113 | REQ-CAT-111 | TC-CAT-057 |
| AC-CAT-114 | REQ-CAT-112 | TC-CAT-058 |
| AC-CAT-115 | REQ-CAT-113 | TC-CAT-059 |
| AC-CAT-116 | REQ-CAT-114 | TC-CAT-060 |
| AC-CAT-117 | REQ-CAT-115 | TC-CAT-116 |
| AC-CAT-118 | REQ-CAT-116 | TC-CAT-061 |
| AC-CAT-119 | REQ-CAT-117 | TC-CAT-117 |
| AC-CAT-120 | REQ-CAT-118 | TC-CAT-118 |
| AC-CAT-121 | REQ-CAT-119 | TC-CAT-119 |
| AC-CAT-122 | REQ-CAT-120 | TC-CAT-120 |
| AC-CAT-123 | REQ-CAT-121 | TC-CAT-121 |
| AC-CAT-124 | REQ-CAT-122 | TC-CAT-122 |
| AC-CAT-125 | REQ-CAT-123 | TC-CAT-062 |
| AC-CAT-126 | REQ-CAT-124 | TC-CAT-063 |
| AC-CAT-127 | REQ-CAT-125 | TC-CAT-123 |
| AC-CAT-128 | REQ-CAT-126 | TC-CAT-064 |
| AC-CAT-129 | REQ-CAT-127 | TC-CAT-124 |
| AC-CAT-130 | REQ-CAT-128 | TC-CAT-183 (frontend routing: TC-CAT-196) |
| AC-CAT-131 | REQ-CAT-129 | TC-CAT-184 |
| AC-CAT-132 | REQ-CAT-130 | TC-CAT-185 |
| AC-CAT-133 | REQ-CAT-142 | TC-CAT-186 |
| AC-CAT-134 | REQ-CAT-143 | TC-CAT-187 |
| AC-CAT-135 | REQ-CAT-086 | TC-CAT-188 |
| AC-CAT-136 | REQ-CAT-101 | TC-CAT-189 |
| AC-CAT-137 | REQ-CAT-131 | TC-CAT-190 |
| AC-CAT-138 | REQ-CAT-131 | frontend only — TC-CAT-194 |
| AC-CAT-139 | REQ-CAT-132 | TC-CAT-191 |
| AC-CAT-140 | REQ-CAT-133 | TC-CAT-192 |
| AC-CAT-141 | REQ-CAT-134 | TC-CAT-193 |
| AC-CAT-142 | REQ-CAT-066 | frontend only — TC-CAT-195 |
| AC-CAT-143 | REQ-CAT-016 | TC-CAT-197 |
| AC-CAT-144 | REQ-CAT-135 | TC-CAT-198 |
| AC-CAT-145 | REQ-CAT-050 | TC-CAT-199 |
| AC-CAT-146 | REQ-CAT-053 | TC-CAT-200 |
| AC-CAT-147 | REQ-CAT-052 | TC-CAT-201 |
| AC-CAT-148 | REQ-CAT-106 | TC-CAT-202 |
| AC-CAT-149 | REQ-CAT-007 | TC-CAT-203 |
| AC-CAT-150 | REQ-CAT-087 | TC-CAT-204 |
| AC-CAT-151 | REQ-CAT-062 | TC-CAT-205 |
| AC-CAT-152 | REQ-CAT-093 | TC-CAT-206 |
| AC-CAT-153 | REQ-CAT-071 | TC-CAT-211 |
| AC-CAT-154 | REQ-CAT-145 | TC-CAT-185, 212 (frontend: TC-CAT-210) |
| AC-CAT-155 | REQ-CAT-146 | TC-CAT-186, 212 (frontend: TC-CAT-210) |
| AC-CAT-156 | REQ-CAT-147 | TC-CAT-187, 212 (frontend: TC-CAT-210) |
| AC-CAT-157 | REQ-CAT-136 | TC-CAT-213 |
| AC-CAT-158 | REQ-CAT-136 | TC-CAT-214 |
| AC-CAT-159 | REQ-CAT-137 | TC-CAT-215 |
| AC-CAT-160 | REQ-CAT-138 | TC-CAT-216 |
| AC-CAT-161 | REQ-CAT-139 | TC-CAT-217 |
| AC-CAT-162 | REQ-CAT-144 | TC-CAT-219 (frontend: TC-CAT-220) |
| AC-CAT-163 | REQ-CAT-140 | TC-CAT-221 |
| AC-CAT-164 | REQ-CAT-141 | TC-CAT-222 |
| AC-CAT-165 | REQ-CAT-008 | TC-CAT-224 |
| AC-CAT-166 | REQ-CAT-041 | TC-CAT-226 |
| AC-CAT-167 | REQ-CAT-091 | TC-CAT-227 |
| AC-CAT-168 | REQ-CAT-128 | TC-CAT-228 |
| AC-CAT-169 | REQ-CAT-054 | TC-CAT-229 |
| AC-CAT-170 | REQ-CAT-072 | TC-CAT-230 |
| AC-CAT-171 | REQ-CAT-148 | TC-CAT-231 (frontend: TC-CAT-232) |
| AC-CAT-172 | REQ-CAT-104 | TC-CAT-233 |
| AC-CAT-173 | REQ-CAT-102 | TC-CAT-234 |
| AC-CAT-174 | REQ-CAT-058 | TC-CAT-235 |

### API → TC (TC-CAT-…)
| API | TCs |
|---|---|
| API-CAT-001 | 003, 005, 007, 012, 013, 014, 016, 027, 028, 035, 036, 071, 074, 076, 077, 078, 080, 081, 093, 190, 193, 203, 219, 223, 225, 230, 235 |
| API-CAT-002 | 001, 007, 008, 009, 015, 021, 022, 023, 027, 029, 031, 037, 038, 039, 041, 054, 056, 057, 061, 062, 065, 066, 068, 070, 073, 077, 078, 079, 083, 092, 094, 095, 097, 113, 114, 117, 184, 185, 186, 187, 188, 190, 191, 192, 197, 198, 200, 201, 202, 205, 212, 219, 221, 222, 223, 229, 235 |
| API-CAT-003 | 004, 015, 031, 041, 062, 068, 069, 070, 117, 186, 190, 191, 192, 212 |
| API-CAT-004 | 001, 188, 190, 197 |
| API-CAT-005 | 002, 059, 060, 061, 185, 186, 187, 212, 219, 222 |
| API-CAT-006 | 006, 008, 009, 022, 023, 029, 092, 184, 185, 201, 212, 222, 223, 229 |
| API-CAT-007 | 015, 021, 024, 030, 048, 074, 086, 089, 118, 119, 199, 205, 206, 221, 222, 227, 228, 231, 234 |
| API-CAT-008 | 015, 118, 212 |
| API-CAT-009 | 021, 024, 030, 199, 200, 205, 212, 221 |
| API-CAT-010 | 039, 101, 198, 212 |
| API-CAT-011 | 038, 040, 102, 122, 183, 223, 228 |
| API-CAT-012 | 040, 102, 212, 223 |
| API-CAT-013 | 038, 183, 212, 228 |
| API-CAT-014 | 043, 044, 045, 047, 050, 103, 109, 188, 199, 204, 212, 223, 227, 228 |
| API-CAT-015 | 104, 212, 223, 231 |
| API-CAT-016 | 042, 212 |
| API-CAT-017 | 007, 027, 094, 187, 205, 212, 223, 235 |
| API-CAT-018 | 055, 056, 113, 212, 218 |
| API-CAT-019 | 054, 058, 202, 212, 223 |
| API-CAT-020 | 057, 114, 212 |
| API-CAT-021 | 033, 096, 211, 213, 224 |
| API-CAT-022 | 032, 034, 098, 211, 214, 223, 230 |
| API-CAT-023 | 032, 097, 223, 224 |
| API-CAT-024 | 034, 098, 211, 213, 224, 230 |
| API-CAT-025 | 006, 026, 072, 085, 215 |
| API-CAT-026 | 025, 086, 088, 216, 223, 229 |
| API-CAT-027 | 086, 223 |
| API-CAT-028 | 087, 215, 216 |
| API-CAT-029 | 088 |
| API-CAT-030 | 089, 223 |
| API-CAT-031 | 025, 090, 229 |
| API-CAT-032 | 049, 106, 217, 226, 227 |
| API-CAT-033 | 107, 206 |
| API-CAT-034 | 048 |
| API-CAT-035 | 063, 120 |
| API-CAT-036 | 121 |
| API-CAT-037 | 122 |
| API-CAT-038 | 016, 018, 065, 067, 083, 203, 217, 225, 226, 230 |
| API-CAT-039 | 033, 098, 099, 211, 213 |
| API-CAT-040 | 100 |
| API-CAT-041 | 026, 087, 090, 091, 215 |
| API-CAT-042 | 111, 189, 217, 233, 234 |
| API-CAT-043 | 042, 046, 048, 051, 105, 110, 188, 199, 204, 206, 217 |
| API-CAT-044 | 115 |
| API-CAT-045 | 049, 063, 107, 108, 121, 193, 227 |
| API-CAT-046 | 116 |
| API-CAT-047 | 060, 064, 123, 124 |
| API-CAT-048 | 082 |
| API-CAT-049 | 017, 018, 019, 020, 083, 084, 217, 226 |
| API-CAT-050 | 052, 053, 112, 231, 233, 234 |
| API-CAT-051 | 010, 011, 075 |
| API-CAT-052 | 207 |
| API-CAT-053 | 213, 214, 224 |
| API-CAT-054 | 215, 216 |
| API-CAT-055 | 216 |
| API-CAT-056 | 231 |

### RULE / code → TC (TC-CAT-…)
| RULE | catalog code | TCs (violation / notice / state) |
|---|---|---|
| RULE-CAT-001 | CAT-422-NAME-AR-INVALID | 012, 013 (refusal) · 017 (import row) · 078 (passes) |
| RULE-CAT-002 | CAT-422-NAME-EN-TOO-LONG | 014 |
| RULE-CAT-003 | CAT-409-ITEM-NAME-DUPLICATE | 003, 203 (parallel creates), 219 (reactivation), 225 (normalisation: letter variants collide, Arabic-Indic digits kept) · 020 (import row reason) |
| RULE-CAT-004 | CAT-422-CODE-SYSTEM-ASSIGNED | 015 (Item and Variant) · 080, 081 (assignment) |
| RULE-CAT-005 | CAT-500-CODE-NOT-ISSUED | 016 · 231 (the label screen's internal barcode draws its number) |
| RULE-CAT-006 | CAT-422-LOOKUP-VALUE-INACTIVE | 036 · 019 (import row) · 037 (kept value) · 120 |
| RULE-CAT-007 | CAT-422-BASE-UOM-FIXED | 041 |
| RULE-CAT-008 | CAT-422-CAPABILITY-OFF | 006 (variants: API-CAT-025, 006), 007 (bundles: API-CAT-001, 017) · 197 (not raised on a read or a deactivation) |
| RULE-CAT-009 | CAT-500-CAPABILITY-UNREADABLE | 005 |
| RULE-CAT-010 | CAT-200-VARIANTS-IN-USE | 010 · 075 (allowed) |
| RULE-CAT-011 | CAT-200-BUNDLES-IN-USE | 011 |
| RULE-CAT-012 | CAT-201-EXISTING-VARIANTS-KEPT | 008, 009 · 092 |
| RULE-CAT-013 | CAT-409-VARIANT-COMBINATION-DUPLICATE | 022 · 092 |
| RULE-CAT-014 | CAT-422-VARIANT-LIMIT-EXCEEDED | 023, 201 (parallel generations) |
| RULE-CAT-015 | CAT-409-LAST-ACTIVE-VARIANT | 024, 200 (parallel deactivations) |
| RULE-CAT-016 | CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE | 026, 215 (reactivation) · 216 |
| RULE-CAT-017 | CAT-409-ATTRIBUTE-VALUE-IN-USE | 025, 229 (parallel with a generation, branch a) · 087, 090 (0 uses) |
| RULE-CAT-018 | CAT-422-BUNDLE-COMPONENTS-REQUIRED | 027 (row 1; rows 2 … 6 the quantity bounds) · 093 |
| RULE-CAT-019 | CAT-422-BUNDLE-COMPONENT-INVALID | 028, 187 (reactivation, then repaired), 205 (parallel, branch a) · 093, 212 |
| RULE-CAT-020 | CAT-422-BUNDLE-SINGLE-VARIANT | 029 |
| RULE-CAT-021 | CAT-409-VARIANT-IN-ACTIVE-BUNDLE | 030, 205 (parallel, branch b) |
| RULE-CAT-022 | CAT-422-ITEM-KIND-FIXED | 031 |
| RULE-CAT-023 | CAT-422-CATEGORY-PARENT-INVALID | 032, 214 (reactivation), 211 (parallel, branch b) |
| RULE-CAT-024 | CAT-409-CATEGORY-DUPLICATE | 033, 213 (reactivation) |
| RULE-CAT-025 | CAT-409-CATEGORY-IN-USE | 034, 211 (parallel, branch a), 230 (parallel with an Item create, branch a) |
| RULE-CAT-026 | CAT-422-CATEGORY-INACTIVE | 035, 186 (reactivation, then repaired), 230 (parallel with the deactivation, branch b) · 212 (categoryFk-only repair) |
| RULE-CAT-027 | CAT-422-ITEM-UNIT-INVALID | 039 (row 1; rows 2 … 5 the factor bounds) · 101 |
| RULE-CAT-028 | CAT-422-CONVERSION-FACTOR-FIXED | 040 |
| RULE-CAT-029 | CAT-409-BARCODE-DUPLICATE | 043, 188 (value still held by the barcode of a deactivated Item), 204 (parallel adds) · 199 (value released by a Variant deactivation) |
| RULE-CAT-030 | CAT-422-GTIN-INVALID | 044 |
| RULE-CAT-031 | CAT-422-BARCODE-UNIT-INVALID | 045, 228 (parallel with the unit's deactivation, branch a) |
| RULE-CAT-032 | CAT-409-BARCODE-SCALE-PREFIX | 047, 227 (parallel barcode add and layout create) · 226 (import row reason) |
| RULE-CAT-033 | CAT-422-SCALE-CODE-LENGTH | 050 · 109 |
| RULE-CAT-034 | CAT-422-SCALE-LAYOUT-INVALID | 049 · 106, 107, 206 (update carries no scaleCodeLength) |
| RULE-CAT-035 | CAT-422-SCALE-LABEL-UNREADABLE | 051 (undefined code; wrong check digit) · 110, 206 |
| RULE-CAT-036 | CAT-422-LABEL-COPIES-OUT-OF-RANGE | 052 · 112 |
| RULE-CAT-037 | CAT-422-LABEL-VARIANT-NO-BARCODE | 053, 231 (then repaired on the label screen — ADR-CAT-078), 233 (only a SCALE code, or only a carton barcode — ADR-CAT-079) · 112, 234 (the base-unit barcode is printed) |
| RULE-CAT-038 | CAT-422-IMAGE-NOT-ACCEPTED | 218 (the 21st active image; a reference with no public URL answers CAT-404-NOT-FOUND) · 113, 218 (one public file backs two images) · 056 (the file part — type, size, public storage — refused by the FILE category `CAT_ITEM_IMAGE` at upload, ADR-CAT-076) |
| RULE-CAT-039 | CAT-200-PRIMARY-IMAGE-REPLACED | 054, 202 (parallel) · 055, 057 (first image, promotion) |
| RULE-CAT-040 | CAT-422-MEDIA-VARIANT-MISMATCH | 058 |
| RULE-CAT-041 | CAT-422-PUBLISH-BLOCKED | 061 · 001, 002, 059, 060 (state) |
| RULE-CAT-042 | CAT-422-EINVOICE-CODE-INVALID | 062 · 117, 118 |
| RULE-CAT-043 | CAT-409-UNIT-EINVOICE-DUPLICATE | 063 · 120 |
| RULE-CAT-044 | CAT-422-IMPORT-FILE-REJECTED | 018 |
| RULE-CAT-045 | CAT-200-IMPORT-ROW-REJECTED | 017, 019, 020, 084, 226 |
| RULE-CAT-046 | CAT-400-SEARCH-TERM-TOO-SHORT | 064 |
| RULE-CAT-047 | CAT-404-BARCODE-NOT-FOUND | 046, 188 (Item deactivated) · 042, 048 |
| RULE-CAT-048 | CAT-200-DEACTIVATED | 001, 038, 042, 098, 197, 221, 224 · 021, 199 (barcodes follow the Variant) |
| RULE-CAT-049 | CAT-500-CHANGE-NOT-SAVED | 004, 217 (import past its time limit, rolled back whole) · 059, 069, 224 (event after commit; 224 for every Category change — ADR-CAT-077) |
| RULE-CAT-050 | CAT-422-ITEM-NO-ACTIVE-VARIANT | 185 (then repaired by generation) |
| RULE-CAT-051 | CAT-409-ITEM-UNIT-HAS-BARCODES | 183, 228 (parallel with a barcode add, branch b) |
| RULE-CAT-052 | CAT-422-VARIANT-ATTRIBUTE-MISSING | 184 |
| RULE-CAT-053 | CAT-422-PARENT-INACTIVE | 198, 212 (every endpoint that applies it), 186 (a change other than categoryFk) · 185, 187, 212 (the three repairs are not refused — ADR-CAT-068) |
| PLATFORM-STD | CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-404-LOOKUP-KEY-UNKNOWN | 207 (404 for a key CAT does not own and 403 without a CAT permission on API-CAT-052 — ADR-CAT-065), 190 (403 on API-CAT-001, 003, 004 — AC-CAT-137), 191 (404 across tenants — AC-CAT-139), 192 (409 on a stale version — AC-CAT-140). TC-CAT-124 asserts that the catalogue search does *not* answer 403. CAT-400-REQUEST-INVALID has no AC of its own; it is asserted by TC-CAT-184 rows 2 and 3 (empty or oversized generation selections), TC-CAT-216 (value under an inactive attribute) TC-CAT-223 (a request that contradicts itself or names a record outside its parent — ten cases on API-CAT-001, 006, 014, 015, 017 — and a PUT on an inactive record — five cases on API-CAT-012, 019, 023, 027, 030; none answering CAT-500), TC-CAT-027 and TC-CAT-039 (a quantity or a conversion factor outside the document's bounds) and TC-CAT-229 (a generation naming a value deactivated meanwhile). CAT-403-FORBIDDEN is also asserted by TC-CAT-231 (API-CAT-015 without PERM_CAT_ITEMS_CREATE, API-CAT-056 without PERM_CAT_BARCODE_LABELS_CREATE). CAT-404-NOT-FOUND is also asserted by TC-CAT-218 (an image reference with no public URL — ADR-CAT-076). 401 and 500-SERVER-ERROR have no AC of their own and no TC. 413 and 415 left the catalog with the multipart surface (ADR-CAT-056) |

### XM → TC
none — no `XM-*` exists (registry-db `records: []`).

### Package → TC (TC-CAT-…)
| Package (backend plan unit) | # | TCs |
|---|---|---|
| CORE | 11 | 004, 005, 037, 069, 071, 072, 079, 080, 081, 191, 193 |
| DATA-DOM-MASTER | 1 | 092 |
| DATA-DOM-LOOKUP | 2 | 048, 122 |
| SVC-API-CRUD | 118 | 001, 002, 003, 006, 007, 008, 009, 012, 013, 014, 015, 016, 021, 022, 023, 024, 025, 026, 027, 028, 029, 030, 031, 032, 033, 034, 035, 036, 038, 039, 040, 041, 042, 043, 044, 045, 047, 049, 050, 054, 055, 056, 057, 058, 059, 060, 061, 062, 063, 065, 066, 068, 070, 073, 074, 076, 077, 078, 085, 086, 087, 088, 089, 090, 093, 094, 095, 096, 097, 098, 101, 102, 103, 104, 106, 107, 109, 113, 114, 117, 118, 119, 120, 121, 183, 184, 185, 186, 187, 192, 197, 198, 199, 200, 201, 202, 203, 204, 205, 206, 211, 212, 213, 214, 215, 216, 218, 219, 221, 222, 223, 224, 225, 227, 228, 229, 230, 235 |
| SVC-API-SEARCH | 17 | 046, 051, 064, 067, 091, 099, 100, 105, 108, 110, 111, 115, 116, 123, 188, 189, 207 |
| SVC-API-INT | 18 | 010, 011, 017, 018, 019, 020, 052, 053, 075, 082, 083, 084, 112, 217, 226, 231, 233, 234 |
| SEC-BE | 2 | 124, 190 |
| DOC · ALIGN-BE · INT-C | 0 | no TC — `INT-C` has no block (no XM); `DOC` and `ALIGN-BE` implement no AC of their own (ADR-CAT-052 §2) |

## COVERAGE

| Measure | Result |
|---|---|
| AC covered (this track) | 168 / 174 ✓. The 6 others are observable on a screen only and are covered on the frontend track: AC-CAT-014 → TC-CAT-128, AC-CAT-015 → TC-CAT-129, AC-CAT-027 → TC-CAT-132, AC-CAT-075 → TC-CAT-137, AC-CAT-138 → TC-CAT-194, AC-CAT-142 → TC-CAT-195 (ADR-CAT-052 §1). AC-CAT-076 has its server read here (TC-CAT-207) and its field on the frontend (TC-CAT-138). |
| AC covered (both plans) | 174 / 174 ✓, no gap |
| REQ covered (this track) | 144 / 148 ✓. REQ-CAT-014, 015, 026 and 073 are covered on the frontend track (TC-CAT-128, 129, 132, 137). |
| REQ covered (both plans) | 148 / 148 ✓ (REQ-CAT-148, added at the tenth gate-analysis revise, by TC-CAT-231 and TC-CAT-232; REQ-CAT-140 … 147 were split from REQ-CAT-025, 130 and 135 at the seventh gate-analysis revise and are covered here by the TCs of their re-pointed ACs) |
| API covered | 56 / 56 ✓. This includes API-CAT-011 and API-CAT-046, which no CAT screen calls (ADR-CAT-051), and API-CAT-052 (TC-CAT-207); API-CAT-053 … 055 by TC-CAT-213 … 216; API-CAT-056 by TC-CAT-231. |
| RULE covered | 53 / 53 ✓, each with its catalog code where the rule raises one |
| Concurrency guards | 12 / 12 — stale version (TC-CAT-192) and the eleven parallel-request cases: RULE-CAT-015 (TC-CAT-200), 014 (201), 039 (202), 003 (203), 029 (204), 021 / 019 (205), 025 / 023 (211, the Category tree lock), 032 (227, the scale-prefix lock QR-CAT-074), 051 / 031 (228, the Item lock of ADR-CAT-060), 017 (229, the QR-CAT-031 FOR SHARE read), 025 / 026 (230, the QR-CAT-032 FOR SHARE read), each showing exactly one request succeeding, naming the refusal code, the guard and its owning unit |
| XM edges covered | 0 / 0 — no edge, so the `INT-XM` phase is absent |
| Track size | 169 TCs for 168 ACs (TC-CAT-223 covers the malformed counterparts of nine ACs; TC-CAT-225 is a second case of AC-CAT-007) (ratio 1.0, within the ~2× guard; TC-CAT-185 … 187 and 212 each cover more than one AC). No boundary twin is added; every BOUNDARY-class TC is the AC's own case. |
| Host data | CAT_BRAND BRAND_068 (TC-CAT-068, created active by `POST /api/v1/mdl/lookup-types/{id}/values`), CAT_BRAND OLDBRAND (TC-CAT-037, created by the same call, then made inactive by `DELETE /api/v1/mdl/lookup-values/{id}`); CAT_BRAND XYZ absent (TC-CAT-019, 036: never created). The AC values CAT_UOM BAG (AC-CAT-122 … 124) and CAT_EINVOICE_UNIT H87 (AC-CAT-126) are not seeded; TC-CAT-120, 121, 122 stay on seeded data with the unit DOZEN and TC-CAT-063 with the e-invoice unit BX in their place (ADR-CAT-053 §2). No value is demanded in opposite states, and every TC runs in its own tenant (ADR-CAT-052 §3, §4). |

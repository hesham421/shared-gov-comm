# CHANGE MANIFEST — CAT v2 (baseline v1)

```yaml name=change-manifest
change_set: CS-CAT-001
type: ADDITIVE
summary: 'given erp-core upgraded 1.5.0 → 1.6.0: re-run from `P0` (the first stage
  that matches candidates against the given) through `P1`, `P1.5`, `P2`, `P0` (the
  stages the C-GIVEN-1, C-GIVEN-2, C-GIVEN-3 clauses read); `domain-profile` is platform-level
  — re-run it by hand when the given''s module set changed'
artifacts:
  project-registry:
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  platform-summary:          # owns no IDs; written in full (no prior platform-summary in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  module-registry:           # file module-registry-cat.md; owns no IDs (indexes the POL-CAT ids of business-policies); written in full (no prior module registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  business-policies:         # file business-policies-cat.md
    ADDED: [POL-CAT-001, POL-CAT-002, POL-CAT-003, POL-CAT-004, POL-CAT-005, POL-CAT-006, POL-CAT-007, POL-CAT-008, POL-CAT-009]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  prd:                       # file P0_5/prd-cat.md; written in full (no prior PRD in the current state); US-CAT stream starts at 001, next free 023
    ADDED: [US-CAT-001, US-CAT-002, US-CAT-003, US-CAT-004, US-CAT-005, US-CAT-006, US-CAT-007, US-CAT-008, US-CAT-009, US-CAT-010, US-CAT-011, US-CAT-012, US-CAT-013, US-CAT-014, US-CAT-015, US-CAT-016, US-CAT-017, US-CAT-018, US-CAT-019, US-CAT-020, US-CAT-021, US-CAT-022]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  srs:                       # file P1/srs-cat.md; written in full (no prior SRS in the current state); streams start at 001 — next free REQ 128, AC 130, ENT 013, RULE 050, SCR-REQ 007
    ADDED: [REQ-CAT-001, REQ-CAT-002, REQ-CAT-003, REQ-CAT-004, REQ-CAT-005, REQ-CAT-006, REQ-CAT-007, REQ-CAT-008, REQ-CAT-009, REQ-CAT-010, REQ-CAT-011, REQ-CAT-012, REQ-CAT-013, REQ-CAT-014, REQ-CAT-015, REQ-CAT-016, REQ-CAT-017, REQ-CAT-018, REQ-CAT-019, REQ-CAT-020, REQ-CAT-021, REQ-CAT-022, REQ-CAT-023, REQ-CAT-024, REQ-CAT-025, REQ-CAT-026, REQ-CAT-027, REQ-CAT-028, REQ-CAT-029, REQ-CAT-030, REQ-CAT-031, REQ-CAT-032, REQ-CAT-033, REQ-CAT-034, REQ-CAT-035, REQ-CAT-036, REQ-CAT-037, REQ-CAT-038, REQ-CAT-039, REQ-CAT-040, REQ-CAT-041, REQ-CAT-042, REQ-CAT-043, REQ-CAT-044, REQ-CAT-045, REQ-CAT-046, REQ-CAT-047, REQ-CAT-048, REQ-CAT-049, REQ-CAT-050, REQ-CAT-051, REQ-CAT-052, REQ-CAT-053, REQ-CAT-054, REQ-CAT-055, REQ-CAT-056, REQ-CAT-057, REQ-CAT-058, REQ-CAT-059, REQ-CAT-060, REQ-CAT-061, REQ-CAT-062, REQ-CAT-063, REQ-CAT-064, REQ-CAT-065, REQ-CAT-066, REQ-CAT-067, REQ-CAT-068, REQ-CAT-069, REQ-CAT-070, REQ-CAT-071, REQ-CAT-072, REQ-CAT-073, REQ-CAT-074, REQ-CAT-075, REQ-CAT-076, REQ-CAT-077, REQ-CAT-078, REQ-CAT-079, REQ-CAT-080, REQ-CAT-081, REQ-CAT-082, REQ-CAT-083, REQ-CAT-084, REQ-CAT-085, REQ-CAT-086, REQ-CAT-087, REQ-CAT-088, REQ-CAT-089, REQ-CAT-090, REQ-CAT-091, REQ-CAT-092, REQ-CAT-093, REQ-CAT-094, REQ-CAT-095, REQ-CAT-096, REQ-CAT-097, REQ-CAT-098, REQ-CAT-099, REQ-CAT-100, REQ-CAT-101, REQ-CAT-102, REQ-CAT-103, REQ-CAT-104, REQ-CAT-105, REQ-CAT-106, REQ-CAT-107, REQ-CAT-108, REQ-CAT-109, REQ-CAT-110, REQ-CAT-111, REQ-CAT-112, REQ-CAT-113, REQ-CAT-114, REQ-CAT-115, REQ-CAT-116, REQ-CAT-117, REQ-CAT-118, REQ-CAT-119, REQ-CAT-120, REQ-CAT-121, REQ-CAT-122, REQ-CAT-123, REQ-CAT-124, REQ-CAT-125, REQ-CAT-126, REQ-CAT-127,
      AC-CAT-001, AC-CAT-002, AC-CAT-003, AC-CAT-004, AC-CAT-005, AC-CAT-006, AC-CAT-007, AC-CAT-008, AC-CAT-009, AC-CAT-010, AC-CAT-011, AC-CAT-012, AC-CAT-013, AC-CAT-014, AC-CAT-015, AC-CAT-016, AC-CAT-017, AC-CAT-018, AC-CAT-019, AC-CAT-020, AC-CAT-021, AC-CAT-022, AC-CAT-023, AC-CAT-024, AC-CAT-025, AC-CAT-026, AC-CAT-027, AC-CAT-028, AC-CAT-029, AC-CAT-030, AC-CAT-031, AC-CAT-032, AC-CAT-033, AC-CAT-034, AC-CAT-035, AC-CAT-036, AC-CAT-037, AC-CAT-038, AC-CAT-039, AC-CAT-040, AC-CAT-041, AC-CAT-042, AC-CAT-043, AC-CAT-044, AC-CAT-045, AC-CAT-046, AC-CAT-047, AC-CAT-048, AC-CAT-049, AC-CAT-050, AC-CAT-051, AC-CAT-052, AC-CAT-053, AC-CAT-054, AC-CAT-055, AC-CAT-056, AC-CAT-057, AC-CAT-058, AC-CAT-059, AC-CAT-060, AC-CAT-061, AC-CAT-062, AC-CAT-063, AC-CAT-064, AC-CAT-065, AC-CAT-066, AC-CAT-067, AC-CAT-068, AC-CAT-069, AC-CAT-070, AC-CAT-071, AC-CAT-072, AC-CAT-073, AC-CAT-074, AC-CAT-075, AC-CAT-076, AC-CAT-077, AC-CAT-078, AC-CAT-079, AC-CAT-080, AC-CAT-081, AC-CAT-082, AC-CAT-083, AC-CAT-084, AC-CAT-085, AC-CAT-086, AC-CAT-087, AC-CAT-088, AC-CAT-089, AC-CAT-090, AC-CAT-091, AC-CAT-092, AC-CAT-093, AC-CAT-094, AC-CAT-095, AC-CAT-096, AC-CAT-097, AC-CAT-098, AC-CAT-099, AC-CAT-100, AC-CAT-101, AC-CAT-102, AC-CAT-103, AC-CAT-104, AC-CAT-105, AC-CAT-106, AC-CAT-107, AC-CAT-108, AC-CAT-109, AC-CAT-110, AC-CAT-111, AC-CAT-112, AC-CAT-113, AC-CAT-114, AC-CAT-115, AC-CAT-116, AC-CAT-117, AC-CAT-118, AC-CAT-119, AC-CAT-120, AC-CAT-121, AC-CAT-122, AC-CAT-123, AC-CAT-124, AC-CAT-125, AC-CAT-126, AC-CAT-127, AC-CAT-128, AC-CAT-129,
      ENT-CAT-001, ENT-CAT-002, ENT-CAT-003, ENT-CAT-004, ENT-CAT-005, ENT-CAT-006, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010, ENT-CAT-011, ENT-CAT-012,
      RULE-CAT-001, RULE-CAT-002, RULE-CAT-003, RULE-CAT-004, RULE-CAT-005, RULE-CAT-006, RULE-CAT-007, RULE-CAT-008, RULE-CAT-009, RULE-CAT-010, RULE-CAT-011, RULE-CAT-012, RULE-CAT-013, RULE-CAT-014, RULE-CAT-015, RULE-CAT-016, RULE-CAT-017, RULE-CAT-018, RULE-CAT-019, RULE-CAT-020, RULE-CAT-021, RULE-CAT-022, RULE-CAT-023, RULE-CAT-024, RULE-CAT-025, RULE-CAT-026, RULE-CAT-027, RULE-CAT-028, RULE-CAT-029, RULE-CAT-030, RULE-CAT-031, RULE-CAT-032, RULE-CAT-033, RULE-CAT-034, RULE-CAT-035, RULE-CAT-036, RULE-CAT-037, RULE-CAT-038, RULE-CAT-039, RULE-CAT-040, RULE-CAT-041, RULE-CAT-042, RULE-CAT-043, RULE-CAT-044, RULE-CAT-045, RULE-CAT-046, RULE-CAT-047, RULE-CAT-048, RULE-CAT-049,
      SCR-REQ-CAT-001, SCR-REQ-CAT-002, SCR-REQ-CAT-003, SCR-REQ-CAT-004, SCR-REQ-CAT-005, SCR-REQ-CAT-006]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  registry-srs:              # file P1/registry-srs-cat.md; owns no IDs (indexes the srs ids); written in full (no prior SRS registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  contract:                  # file P1_5/contract-cat.md; written in full (no prior contract in the current state); CON stream starts at 001, next free 019
    ADDED: [CON-CAT-001, CON-CAT-002, CON-CAT-003, CON-CAT-004, CON-CAT-005, CON-CAT-006, CON-CAT-007, CON-CAT-008, CON-CAT-009, CON-CAT-010, CON-CAT-011, CON-CAT-012, CON-CAT-013, CON-CAT-014, CON-CAT-015, CON-CAT-016, CON-CAT-017, CON-CAT-018]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
```

P0 delta note: the given's module set changed (1.6.0 adds `gl`), so the domain-profile hand re-run named in the summary applies (OI-02).

P0.5 delta note: 22 user stories were added, covering 9 of 9 POL-CAT policies. No story is deferred. The erp-core 1.6.0 change (`gl`) does not touch the catalogue boundary, so no story depends on the given upgrade.

P1 delta note: the SRS is written in full, because the current state holds no earlier SRS. It adds REQ 127, AC 129, ENT 12, RULE 49 and SCR-REQ 6, covering 22 of 22 stories. It records ADR-CAT-038 … ADR-CAT-043 (all ACCEPTED, none BLOCKED). The `module-dependencies` block is `consumes: []`. The given uses go through erp-core mdl, cu, sequence, file, events, report and sec; no use goes through the new `gl` module.

P1.5 delta note: the contract is written in full, because the current state holds no earlier contract. It adds CON 18:
- 6 entity items, one for each SHARED ENT (ENT-CAT-001, 002, 006, 007, 008, 009), each with a BIGINT identifier.
- 11 operation items (signatures only).
- 1 catalogue-change event item.

Nothing is MODIFIED or REMOVED, so no consumer edge is affected. The contract repeats the SRS `given-usage` rows verbatim. No question was raised and no ADR was needed; the next free ADR is ADR-CAT-044.

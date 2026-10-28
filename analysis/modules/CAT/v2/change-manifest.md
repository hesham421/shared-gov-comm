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
  db-script:                 # file P2/db-script-cat.md; written in full as a create-all migration (no prior db-script in the current state); DBF stream starts at 001, next free 162; no XM (consumes: []), XM next free 001
    ADDED: [DBF-CAT-001, DBF-CAT-002, DBF-CAT-003, DBF-CAT-004, DBF-CAT-005, DBF-CAT-006, DBF-CAT-007, DBF-CAT-008, DBF-CAT-009, DBF-CAT-010,
      DBF-CAT-011, DBF-CAT-012, DBF-CAT-013, DBF-CAT-014, DBF-CAT-015, DBF-CAT-016, DBF-CAT-017, DBF-CAT-018, DBF-CAT-019, DBF-CAT-020,
      DBF-CAT-021, DBF-CAT-022, DBF-CAT-023, DBF-CAT-024, DBF-CAT-025, DBF-CAT-026, DBF-CAT-027, DBF-CAT-028, DBF-CAT-029, DBF-CAT-030,
      DBF-CAT-031, DBF-CAT-032, DBF-CAT-033, DBF-CAT-034, DBF-CAT-035, DBF-CAT-036, DBF-CAT-037, DBF-CAT-038, DBF-CAT-039, DBF-CAT-040,
      DBF-CAT-041, DBF-CAT-042, DBF-CAT-043, DBF-CAT-044, DBF-CAT-045, DBF-CAT-046, DBF-CAT-047, DBF-CAT-048, DBF-CAT-049, DBF-CAT-050,
      DBF-CAT-051, DBF-CAT-052, DBF-CAT-053, DBF-CAT-054, DBF-CAT-055, DBF-CAT-056, DBF-CAT-057, DBF-CAT-058, DBF-CAT-059, DBF-CAT-060,
      DBF-CAT-061, DBF-CAT-062, DBF-CAT-063, DBF-CAT-064, DBF-CAT-065, DBF-CAT-066, DBF-CAT-067, DBF-CAT-068, DBF-CAT-069, DBF-CAT-070,
      DBF-CAT-071, DBF-CAT-072, DBF-CAT-073, DBF-CAT-074, DBF-CAT-075, DBF-CAT-076, DBF-CAT-077, DBF-CAT-078, DBF-CAT-079, DBF-CAT-080,
      DBF-CAT-081, DBF-CAT-082, DBF-CAT-083, DBF-CAT-084, DBF-CAT-085, DBF-CAT-086, DBF-CAT-087, DBF-CAT-088, DBF-CAT-089, DBF-CAT-090,
      DBF-CAT-091, DBF-CAT-092, DBF-CAT-093, DBF-CAT-094, DBF-CAT-095, DBF-CAT-096, DBF-CAT-097, DBF-CAT-098, DBF-CAT-099, DBF-CAT-100,
      DBF-CAT-101, DBF-CAT-102, DBF-CAT-103, DBF-CAT-104, DBF-CAT-105, DBF-CAT-106, DBF-CAT-107, DBF-CAT-108, DBF-CAT-109, DBF-CAT-110,
      DBF-CAT-111, DBF-CAT-112, DBF-CAT-113, DBF-CAT-114, DBF-CAT-115, DBF-CAT-116, DBF-CAT-117, DBF-CAT-118, DBF-CAT-119, DBF-CAT-120,
      DBF-CAT-121, DBF-CAT-122, DBF-CAT-123, DBF-CAT-124, DBF-CAT-125, DBF-CAT-126, DBF-CAT-127, DBF-CAT-128, DBF-CAT-129, DBF-CAT-130,
      DBF-CAT-131, DBF-CAT-132, DBF-CAT-133, DBF-CAT-134, DBF-CAT-135, DBF-CAT-136, DBF-CAT-137, DBF-CAT-138, DBF-CAT-139, DBF-CAT-140,
      DBF-CAT-141, DBF-CAT-142, DBF-CAT-143, DBF-CAT-144, DBF-CAT-145, DBF-CAT-146, DBF-CAT-147, DBF-CAT-148, DBF-CAT-149, DBF-CAT-150,
      DBF-CAT-151, DBF-CAT-152, DBF-CAT-153, DBF-CAT-154, DBF-CAT-155, DBF-CAT-156, DBF-CAT-157, DBF-CAT-158, DBF-CAT-159, DBF-CAT-160,
      DBF-CAT-161]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  registry-db:               # file P2/registry-db-cat.md; owns no IDs (indexes the db-script DBF ranges and the empty XM register); written in full (no prior DB registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  backend-execution-plan:    # file P3_1/backend-execution-plan-cat.md; written in full (no prior backend plan in the current state); API and QR streams start at 001 — next free API 052, QR 071; no XM placed or minted (register empty)
    ADDED: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010,
      API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020,
      API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030,
      API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040,
      API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-047, API-CAT-048, API-CAT-049, API-CAT-050,
      API-CAT-051,
      QR-CAT-001, QR-CAT-002, QR-CAT-003, QR-CAT-004, QR-CAT-005, QR-CAT-006, QR-CAT-007, QR-CAT-008, QR-CAT-009, QR-CAT-010,
      QR-CAT-011, QR-CAT-012, QR-CAT-013, QR-CAT-014, QR-CAT-015, QR-CAT-016, QR-CAT-017, QR-CAT-018, QR-CAT-019, QR-CAT-020,
      QR-CAT-021, QR-CAT-022, QR-CAT-023, QR-CAT-024, QR-CAT-025, QR-CAT-026, QR-CAT-027, QR-CAT-028, QR-CAT-029, QR-CAT-030,
      QR-CAT-031, QR-CAT-032, QR-CAT-033, QR-CAT-034, QR-CAT-035, QR-CAT-036, QR-CAT-037, QR-CAT-038, QR-CAT-039, QR-CAT-040,
      QR-CAT-041, QR-CAT-042, QR-CAT-043, QR-CAT-044, QR-CAT-045, QR-CAT-046, QR-CAT-047, QR-CAT-048, QR-CAT-049, QR-CAT-050,
      QR-CAT-051, QR-CAT-052, QR-CAT-053, QR-CAT-054, QR-CAT-055, QR-CAT-056, QR-CAT-057, QR-CAT-058, QR-CAT-059, QR-CAT-060,
      QR-CAT-061, QR-CAT-062, QR-CAT-063, QR-CAT-064, QR-CAT-065, QR-CAT-066, QR-CAT-067, QR-CAT-068, QR-CAT-069, QR-CAT-070]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  registry-exec-be:          # file P3_1/registry-exec-be-cat.md; owns no IDs (indexes the API / QR ranges of the backend plan); written in full (no prior registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  api-spec:                  # file P3_1/api-spec-cat.yaml; owns no IDs (generated from the backend plan — fold: replace, re-emitted whole); 51 operations = 51 API blocks
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  flow-diagram:              # file P3_2/flow-diagram-cat.md; owns no IDs (flows carry no atom); written in full (no prior flow diagram in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  ui-ux-spec:                # file P3_2/ui-ux-spec-cat.md; written in full (no prior ui-ux-spec in the current state); SCR and UXD streams start at 001 — next free SCR 007, UXD 004
    ADDED: [SCR-CAT-001, SCR-CAT-002, SCR-CAT-003, SCR-CAT-004, SCR-CAT-005, SCR-CAT-006,
      UXD-CAT-001, UXD-CAT-002, UXD-CAT-003]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  frontend-execution-plan:   # file P3_2/frontend-execution-plan-cat.md; owns no IDs (cites SCR/UXD of the spec and API of api-spec-cat.yaml); written in full (no prior frontend plan in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  registry-exec-fe:          # file P3_2/registry-exec-fe-cat.md; owns no IDs (indexes the SCR / UXD ranges); written in full (no prior registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  backend-test-plan:         # file P4/backend-test-plan-cat.md; written in full (no prior test plan in the current state); TC stream starts at 001 (shared with the frontend test plan) — 124 TCs, no INT-XM (no XM)
    ADDED: [TC-CAT-001, TC-CAT-002, TC-CAT-003, TC-CAT-004, TC-CAT-005, TC-CAT-006, TC-CAT-007, TC-CAT-008, TC-CAT-009, TC-CAT-010,
      TC-CAT-011, TC-CAT-012, TC-CAT-013, TC-CAT-014, TC-CAT-015, TC-CAT-016, TC-CAT-017, TC-CAT-018, TC-CAT-019, TC-CAT-020,
      TC-CAT-021, TC-CAT-022, TC-CAT-023, TC-CAT-024, TC-CAT-025, TC-CAT-026, TC-CAT-027, TC-CAT-028, TC-CAT-029, TC-CAT-030,
      TC-CAT-031, TC-CAT-032, TC-CAT-033, TC-CAT-034, TC-CAT-035, TC-CAT-036, TC-CAT-037, TC-CAT-038, TC-CAT-039, TC-CAT-040,
      TC-CAT-041, TC-CAT-042, TC-CAT-043, TC-CAT-044, TC-CAT-045, TC-CAT-046, TC-CAT-047, TC-CAT-048, TC-CAT-049, TC-CAT-050,
      TC-CAT-051, TC-CAT-052, TC-CAT-053, TC-CAT-054, TC-CAT-055, TC-CAT-056, TC-CAT-057, TC-CAT-058, TC-CAT-059, TC-CAT-060,
      TC-CAT-061, TC-CAT-062, TC-CAT-063, TC-CAT-064, TC-CAT-065, TC-CAT-066, TC-CAT-067, TC-CAT-068, TC-CAT-069, TC-CAT-070,
      TC-CAT-071, TC-CAT-072, TC-CAT-073, TC-CAT-074, TC-CAT-075, TC-CAT-076, TC-CAT-077, TC-CAT-078, TC-CAT-079, TC-CAT-080,
      TC-CAT-081, TC-CAT-082, TC-CAT-083, TC-CAT-084, TC-CAT-085, TC-CAT-086, TC-CAT-087, TC-CAT-088, TC-CAT-089, TC-CAT-090,
      TC-CAT-091, TC-CAT-092, TC-CAT-093, TC-CAT-094, TC-CAT-095, TC-CAT-096, TC-CAT-097, TC-CAT-098, TC-CAT-099, TC-CAT-100,
      TC-CAT-101, TC-CAT-102, TC-CAT-103, TC-CAT-104, TC-CAT-105, TC-CAT-106, TC-CAT-107, TC-CAT-108, TC-CAT-109, TC-CAT-110,
      TC-CAT-111, TC-CAT-112, TC-CAT-113, TC-CAT-114, TC-CAT-115, TC-CAT-116, TC-CAT-117, TC-CAT-118, TC-CAT-119, TC-CAT-120,
      TC-CAT-121, TC-CAT-122, TC-CAT-123, TC-CAT-124]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  frontend-test-plan:        # file P4/frontend-test-plan-cat.md; written in full (no prior test plan in the current state); continues the shared TC stream — 58 TCs (52 module, 6 INT-UXD); next free TC 183
    ADDED: [TC-CAT-125, TC-CAT-126, TC-CAT-127, TC-CAT-128, TC-CAT-129, TC-CAT-130,
      TC-CAT-131, TC-CAT-132, TC-CAT-133, TC-CAT-134, TC-CAT-135, TC-CAT-136, TC-CAT-137, TC-CAT-138, TC-CAT-139, TC-CAT-140,
      TC-CAT-141, TC-CAT-142, TC-CAT-143, TC-CAT-144, TC-CAT-145, TC-CAT-146, TC-CAT-147, TC-CAT-148, TC-CAT-149, TC-CAT-150,
      TC-CAT-151, TC-CAT-152, TC-CAT-153, TC-CAT-154, TC-CAT-155, TC-CAT-156, TC-CAT-157, TC-CAT-158, TC-CAT-159, TC-CAT-160,
      TC-CAT-161, TC-CAT-162, TC-CAT-163, TC-CAT-164, TC-CAT-165, TC-CAT-166, TC-CAT-167, TC-CAT-168, TC-CAT-169, TC-CAT-170,
      TC-CAT-171, TC-CAT-172, TC-CAT-173, TC-CAT-174, TC-CAT-175, TC-CAT-176, TC-CAT-177, TC-CAT-178, TC-CAT-179, TC-CAT-180,
      TC-CAT-181, TC-CAT-182]
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

P2 delta note: the db-script is written as a create-all migration, because the current state holds no earlier db-script. It adds 12 tables (one per ENT) and DBF 161 (001 … 161):
- every PK uses the identity clause, as a BIGINT, which matches the contract's identifier promise;
- 13 intra-module FKs;
- every UNIQUE is composite with TENANT_ID.

The XM register is empty (`consumes: []`), so `gov.py graph` gets no edge from CAT. The given tables (mdl lookup tables, CORE_NUMBER_SERIES, FILE_DOCUMENT, CORE_TENANT) are never re-created. The 10 CAT lookup keys (34 values), the 3 number series and the 8 Unit e-invoice codes are seeded for the PLATFORM tenant. Decisions are recorded as DEFAULTs D-P2-01 … D-P2-18. No question was raised and no ADR was needed; the next free ADR is unchanged.

P3.1 delta note: the backend execution plan is written in full, because the current state holds no earlier plan. It adds API 51 (001 … 051) and QR 70 (001 … 070) in the profile's seven phases; INT-C, the last phase, carries no block because the XM register is empty and no edge was minted. Every CON-CAT item (001 … 018) is honoured. The Error Catalog has 57 codes (49 RULE rows, 8 PLATFORM-STD). The API document `api-spec-cat.yaml` (OpenAPI 3.1.0) is derived from the plan with one operation per API block. ADR-CAT-044 … ADR-CAT-048 are recorded (all ACCEPTED, non-breaking, none BLOCKED); the next free ADR is ADR-CAT-049. No question was raised.

P3.2 delta note: the frontend design and the frontend execution plan are written in full, because the current state holds no earlier frontend artifact. The ui-ux-spec adds SCR 6 (001 … 006, one per SCR-REQ) and UXD 3 (001 … 003). The three UXDs are lookup values, capability flags and image files; all three are served by the given erp-core, and none of them is an XM. The flow diagram holds 18 flows covering 22 of 22 stories. The plan binds to `api-spec-cat.yaml` (the mock): 126 REQ → API bindings, 49 runtime codes → RULE, and 49 of 51 operations used. In the profile's six frontend phases it has 24 per-screen SUBs and one `screen-hooks` block per screen. ADR-CAT-049 … ADR-CAT-051 are recorded (all ACCEPTED, non-breaking, none BLOCKED); the next free ADR is ADR-CAT-052. No question was raised.

P4 delta note: both test plans are written in full, because the current state holds no earlier test plan. The TC stream starts at 001 and is shared by the two plans: TC-CAT-001 … 124 are backend and TC-CAT-125 … 182 are frontend.
- Coverage over the union of the two plans is AC 129 / 129, REQ 127 / 127, API 51 / 51, RULE 49 / 49, SCR 6 / 6 and UXD 3 / 3.
- The backend `INT-XM` phase is absent, because there is no XM. The frontend `INT-UXD` phase holds 6 TCs, for the three UXDs owned by the given erp-core.
- ADR-CAT-052 is recorded (ACCEPTED, non-breaking). It covers the track split, the package binding, the host-data creation surface, catalogue isolation, the fields `api-spec-cat.yaml` does not expose (next-action code, listed Variants, Variant `updatedAt`) and fault injection.
- Round 2 (after `gov.py analyze` refused C10.7 and C10.8): ADR-CAT-053 is recorded (ACCEPTED, non-breaking). Eight frontend TCs (TC-CAT-130, 143, 144, 145, 150, 157, 165, 171) now assert the routing and unchanged display of codes whose words the frontend plan does not bind; their wording stays asserted on the backend track. The unseeded AC values CAT_UOM `BAG` and CAT_EINVOICE_UNIT `H87`, for which no named add-value call exists, are replaced by seeded values in TC-CAT-063, 120, 121, 122 and 175 (TC-CAT-138 drops its `BAG` mention). Every TC id keeps its number and stays ADDED in this version. Coverage is unchanged.
- The next free ADR is ADR-CAT-054. No question was raised.

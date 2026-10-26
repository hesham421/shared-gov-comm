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
```

P0 delta note: the given's module set changed (1.6.0 adds `gl`), so the domain-profile hand re-run named in the summary applies (OI-02).

P0.5 delta note: 22 user stories were added, covering 9 of 9 POL-CAT policies. No story is deferred. The erp-core 1.6.0 change (`gl`) does not touch the catalogue boundary, so no story depends on the given upgrade.

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
```

P0 delta note: the given's module set changed (1.6.0 adds `gl`), so the domain-profile hand re-run named in the summary applies (OI-02).

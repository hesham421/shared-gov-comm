<!-- source: PHASE:INT-XM — preamble before the first SUB -->
<!-- traces: REQ-INV-035, REQ-INV-039, REQ-INV-040, REQ-INV-042, REQ-INV-043, REQ-INV-049, REQ-INV-050, REQ-INV-051, REQ-INV-052, REQ-INV-053, REQ-INV-054, REQ-INV-059, REQ-INV-063, REQ-INV-070, REQ-INV-072, REQ-INV-075, REQ-INV-078, REQ-INV-080, REQ-INV-088, REQ-INV-091, REQ-INV-092, REQ-INV-097, REQ-INV-106, REQ-INV-109, REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-129, XM-INV-001, XM-INV-002, XM-INV-003, XM-INV-004, XM-INV-005, XM-INV-006, XM-INV-007, XM-INV-008, XM-INV-009, XM-INV-010, XM-INV-011, XM-INV-012, XM-INV-013, XM-INV-014, XM-INV-015 -->
## INT-XM — integration test cases (this module's edges)

Source: the 15 `XM-*` blocks of INT-C in the backend plan (cross-checked against registry-db: 11 HARD-FK, 4
SOFT-READ, all to CAT). Each block's `requires` is CAT:DELIVERED. A HARD-FK yields an EXISTS case and a
MISSING case. In both, the block's migration has run, so its constraint exists, and its pre-check query (orphan
count) returns 0. A SOFT-READ yields one GRACEFUL-DEGRADATION case. Each case exercises INV's own surface; the
CAT entity is cited by id only. When `requires` is not met, the block's `if_not_met` path applies (skip-block,
recorded in execution-state.json → deferred_xm) and its cases are not run.

<!-- source: PHASE:INT-UXD — preamble before the first SUB -->
<!-- traces: AC-INV-044, AC-INV-069, AC-INV-082, AC-INV-085, AC-INV-097, AC-INV-107, AC-INV-122, AC-INV-135, AC-INV-138, AC-INV-140, REQ-INV-039, REQ-INV-063, REQ-INV-075, REQ-INV-078, REQ-INV-088, REQ-INV-097, REQ-INV-109, REQ-INV-121, REQ-INV-124, REQ-INV-125, UXD-INV-001, UXD-INV-002, UXD-INV-003, UXD-INV-004, UXD-INV-005, UXD-INV-006, UXD-INV-007, UXD-INV-008, UXD-INV-009, UXD-INV-010, UXD-INV-011 -->
## INT-UXD — integration test cases (foreign fields this module displays)

Source: the 11 `UXD-*` the frontend plan cites (F1 … F4 blocks of SCR-INV-003 … 009, 012). Every foreign field
is owned by CAT and read through the CAT lookups the plan declares (ADR-INV-033). Each UXD yields a "rendered"
case and an "empty / failure" case. The empty or failure state is the one the plan's hook row declares; no copy
is invented.

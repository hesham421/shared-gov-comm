<!-- source: PHASE:INT-C — the phase around its blocks -->
<!-- traces: REQ-INV-035, REQ-INV-039, REQ-INV-040, REQ-INV-042, REQ-INV-043, REQ-INV-049, REQ-INV-050, REQ-INV-051, REQ-INV-052, REQ-INV-053, REQ-INV-054, REQ-INV-059, REQ-INV-063, REQ-INV-070, REQ-INV-072, REQ-INV-075, REQ-INV-078, REQ-INV-080, REQ-INV-088, REQ-INV-091, REQ-INV-092, REQ-INV-097, REQ-INV-106, REQ-INV-109, REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-129 -->
## INT-C — integration (one block per edge)

The one place this plan speaks about another module. Every edge targets CAT (the catalogue module, DAG #1 —
downward from INV, DAG #3). Access is in-process (conventions.module_interface: in_process): CAT's published
crossmodule API is an injected bean — there is no HTTP client, base path, timeout or network error path.
Foreign keys reference the target's primary-key column only (ADR-INV-023); the executor checks the target table
and key column against CAT's delivered migration before running a patch. Each block is one package
(`integration/XM-INV-0nn`); the adapters live in `com.commerce.inv.integration.cat`. They inject CAT's published
crossmodule bean by its type, as CAT delivers it: CAT v2 still declares it under `com.erp.cat.crossmodule`, and
the rename to `com.commerce.cat.crossmodule` is the open platform finding of business-policies-pty PLATFORM
FINDINGS row 1 (cited in business-policies-inv PLATFORM FINDINGS row 3) — when CAT applies it, only the adapters'
import lines change. The CAT contract items INV relies on are CON-CAT-001, 002, 005, 006, 007, 008, 010 and 016
(ADR-INV-040).

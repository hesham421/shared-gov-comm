# ADR-CAT-074 — The item import saves entities; no JDBC batching

Status : ACCEPTED (non-breaking) · Stage : P3.1 / P4 (gate-analysis round 7, G3) · Module : CAT v2 · Date : 2026-10-28 · Amends : ADR-CAT-071 §1 and §4

## Context
ADR-CAT-071 §4 and API-CAT-049 inserted each batch of 500 import rows "with JDBC batching". Every CAT primary key is `GENERATED ALWAYS AS IDENTITY`, and Hibernate does not batch inserts for identity keys. The statement was therefore either not batched, or it was a `JdbcTemplate` / native statement. No QR catalogued such a statement, none named `TENANT_ID`, and none carried `@TenantUnscoped` (checklist C3). ADR-CAT-047 counts exactly six native statements. The 60 s budget of ADR-CAT-071 §1 and TC-CAT-217's fault point ("first batch inserted") both assumed the batching.

Options:
- (a) save entities through the catalogued queries, keeping the savepoints and the replay;
- (b) add a seventh catalogued native statement that names `TENANT_ID` and carries `@TenantUnscoped(reason, adr = "ADR-CAT-047")`.

## Decision
Option (a):
1. **Entity saves.** The write pass saves each row's Item, default Variant and GTIN barcode as entities, through QR-CAT-003, QR-CAT-011 and QR-CAT-049, under the given tenant filter. Each is one INSERT. Nothing is sent as a JDBC batch, and no native statement is added: ADR-CAT-047 still counts six.
2. **Batches of 500 rows stay** as units of work. A batch's codes are drawn before its rows are saved (ADR-CAT-071 §5). The batch is flushed at its end, under one savepoint. On a unique violation, the batch rolls back to its savepoint and is replayed row by row, each row with its own savepoint, reusing the drawn codes.
3. **Budget.** ADR-CAT-071 §1's 60 s for 5,000 valid rows becomes a **target to be measured** on the reference deployment by TC-CAT-217's run without the injected delay. It is reported, not assumed. The hard limit stays: `@Transactional(timeout = 120)` and the 120 s request timeout, with the whole-import rollback of ADR-CAT-071 §3.
4. **Test fault point.** TC-CAT-217 delays the write pass "after the first 500 rows are saved".

## Consequences
- Backend plan: API-CAT-049 Orchestration, Repository and Concurrency lines; PHASE CORE "Core gaps" row of the number series.
- SRS: REQ-CAT-139 rationale (the 60 s figure is a measured target).
- Tests: TC-CAT-217 (fault point, and a measured run).
- ADR-CAT-071: amended note.

## Traces
REQ-CAT-038 · REQ-CAT-139 · AC-CAT-161 · API-CAT-049 · QR-CAT-003 · QR-CAT-011 · QR-CAT-049 · TC-CAT-217 · ADR-CAT-047 · ADR-CAT-071

# ADR-PTY-022 — Lookup seed rows written against the given mdl tables

Status  : ACCEPTED (non-breaking) — column list and TENANT_ID = 1 revised by ADR-PTY-035 · Stage : P2 · Module : PTY v1 · Date : 2026-10-29
Filed   : verbatim from the db-script §5.2 (analysis/modules/PTY/P2/db-script-pty.md) by the P3.1 repair, ADR-PTY-030

## Context
PTY owns five lookup keys (SRS A6); their values live in the given `mdl` tables MDL_LOOKUP_TYPE and MDL_LOOKUP_VALUE, which this script must seed and must not re-create (engine §4.2). The given's internal column list is not among this stage's inputs.

## Decision
BLOCK 8 seeds both tables with the column list CODE, NAME_AR, NAME_EN, IS_ACTIVE_FL (type) and LOOKUP_TYPE_CODE, CODE, NAME_AR, NAME_EN, SORT_ORDER, IS_ACTIVE_FL (value) — the platform naming convention for a coded, bilingual, deactivatable lookup — inserted idempotently with `WHERE NOT EXISTS`. The values and labels are verbatim from SRS A6.

## Consequences
The executor of the backend plan (P3.1) checks this column list against the given's installed `mdl` DDL when it turns BLOCK 8 into the module's Flyway migration (V1000+) and aligns the column names if the given's differ; the seeded keys and values are fixed by the SRS and do not change. New tenants receive the values through the given's provisioning copy of `mdl` seeds.

## Traces
SRS A6 (CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT, CREDIT_OVERRIDE_STATUS); DBF-PTY-014, 034, 054, 063; REQ-PTY-007, REQ-PTY-038, REQ-PTY-040, REQ-PTY-086, REQ-PTY-091.

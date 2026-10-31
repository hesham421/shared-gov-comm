# ADR-INV-032 — State-change PATCH endpoints carry the caller's version as a typed body
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan · repair round 1)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : API-INV-006, API-INV-007, API-INV-014, API-INV-015, API-INV-024, API-INV-032, API-INV-038, DBF-INV-009, DBF-INV-021, DBF-INV-096, DBF-INV-124, DBF-INV-153, REQ-INV-007, REQ-INV-008, REQ-INV-026, REQ-INV-027, REQ-INV-080, REQ-INV-092, REQ-INV-099, ADR-INV-014, ADR-INV-021

## Context
`gov.py analyze` (C7.31 api-spec-valid) refused the API document: seven PATCH operations — reactivate and
set-default of a warehouse and of a branch, and post of a transfer, a stock count and a stock adjustment —
had no typed `requestBody`. The plan wrote them as "path id · no body". The SRS B5 rows list only `id` as
input. Each of these actions changes a versioned record (VERSION, ADR-INV-014). The record may have changed
since the caller read it: a DRAFT edited by another user, a default moved by another Owner.

## Decision
Each of the seven endpoints takes one shared, typed body `VersionedActionRequest {version}` (int64, required,
no other property). `version` is the VERSION of the record the action targets: DBF-INV-009 for a warehouse,
DBF-INV-021 for a branch, DBF-INV-096 for a transfer, DBF-INV-124 for a stock count, DBF-INV-153 for a stock
adjustment. For set-default it is the target's version. The service checks it after locking the row. A
mismatch answers the existing catalog row INV-409-STALE-VERSION (PLATFORM-STD, ADR-INV-021), which now lists
the seven endpoints.

This is the platform's optimistic-lock pattern, already used by every PUT of the module. A body that only
satisfies the schema (an empty object) was rejected: it would carry nothing, and the race it leaves open is
real. Posting a draft whose lines another user has just changed would move stock the poster never saw.

## Consequences
- The plan's Request, Errors and Orchestration lines and `traces=` of API-INV-006, 007, 014, 015, 024, 032
  and 038 name the version DBF. The API document derives the body, the 409 response and `x-traces` from them.
- The frontend sends the version it read with each of these actions, as it already does on save.
- No SRS meaning, RULE, REQ, table or column changes. SRS B5 lists inputs by meaning; the version is the
  platform concurrency token, not a business input.

## Status
ACCEPTED — non-breaking.

# ADR-PTY-019 — Audit user columns hold the platform principal

Status  : ACCEPTED (non-breaking) · Stage : P2 · Module : PTY v1 · Date : 2026-10-29
Filed   : verbatim from the db-script §5.2 (analysis/modules/PTY/P2/db-script-pty.md) by the P3.1 repair, ADR-PTY-030

## Context
SRS A3 lists createdBy / updatedBy as "reference (given sec User, BIGINT)", system-filled. The engine's AUDIT COLUMNS rule (from the given) states that audit user columns are filled by the platform and hold a principal string, not a numeric FK; the given's auditing fills them, and a business module never writes them.

## Decision
CREATED_BY and UPDATED_BY are `VARCHAR(100)` holding the principal the given `sec` module authenticates — the same sec User the SRS refers to, in the form the platform writes. Non-audit user references the module itself sets keep the SRS type: SEC_USER_ID (DBF-PTY-017) and DECIDED_BY (DBF-PTY-056) are `BIGINT` sec user identifiers.

## Consequences
RULE-PTY-029 ("only the requester cancels") compares PTY_CREDIT_OVERRIDE_REQUEST.CREATED_BY with the current principal; REQ-PTY-102 filters on it (indexed, IDX_PTY_CREDIT_OVERRIDE_REQUEST_CREATED_BY). User names shown in history and decisions are resolved through SecUserDirectoryApi (SRS A9). No constraint on SEC_USER either way, so no SRS behaviour changes.

## Traces
ENT-PTY-001 … ENT-PTY-005 .createdBy / .updatedBy; DBF-PTY-009, 011, 021, 023, 029, 031, 046, 048, 064, 066; REQ-PTY-096, REQ-PTY-102; RULE-PTY-029.

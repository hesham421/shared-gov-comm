# ADR-PTY-021 — Tenant scoping without a tenant column

Status  : SUPERSEDED by ADR-PTY-035 (gate-analysis revise, G2; was ACCEPTED, non-breaking) · Stage : P2 · Module : PTY v1 · Date : 2026-10-29
Filed   : verbatim from the db-script §5.2 (analysis/modules/PTY/P2/db-script-pty.md) by the P3.1 repair, ADR-PTY-030

## Context
RULE-PTY-002, RULE-PTY-014 and RULE-PTY-020 are per-tenant uniqueness rules. The SRS has no tenant field; business modules are tenant-agnostic and the given `erp-core` (CORE_TENANT, TenantContext) isolates tenants (platform brief §0, D3).

## Decision
No tenant column (NO-COLUMN-INVENTION). Every uniqueness rule is declared once in the script (UQ_PTY_PARTY_EMAIL, UQ_PTY_CUSTOMER_IS_WALK_IN_FL, UQ_PTY_CUSTOMER_SEC_USER_ID, UQ_PTY_PARTY_CODE, UQ_PTY_CREDIT_OVERRIDE_REQUEST_DOC_NO, UQ_PTY_ADDRESS_PARTY_CODE) and holds per tenant under the isolation the given applies to every business table.

## Consequences
If the given isolates tenants by a discriminator column in shared tables, the given's tenancy mechanism adds that column to each PTY table and leads each UNIQUE above with it; that is the given's documented integration, applied by the executor in P3.1, not a PTY column. Nothing in PTY reads a tenant identifier.

## Traces
RULE-PTY-002, RULE-PTY-014, RULE-PTY-020; DBF-PTY-003, 007, 017, 018, 061; REQ-PTY-010, REQ-PTY-044, REQ-PTY-054.

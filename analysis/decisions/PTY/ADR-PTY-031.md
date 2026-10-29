# ADR-PTY-031 — Typed bodies for the reactivation and set-default PATCH endpoints

Status  : ACCEPTED (non-breaking) · Stage : P3.1 (repair round 1) · Module : PTY v1 · Date : 2026-10-29

## Context
SRS B5 lists three PATCH operations whose only input is the path id: reactivate Customer (`/customers/{id}/activation`, API-PTY-005), reactivate Supplier (`/suppliers/{id}/activation`, API-PTY-010) and set default Address (`/parties/{partyId}/addresses/{id}/default`, API-PTY-015). PATCH means a partial update (engine §2A.2), and `gov.py analyze` (C7.31 api-spec-valid) refuses a PATCH with no typed `requestBody` schema. The SRS paths and verbs stay as they are.

## Decision
- Each of the three PATCH endpoints takes a required JSON body that names the one field it changes, with a value fixed by the endpoint's single meaning:
  - API-PTY-005 / API-PTY-010 — `ActivationRequest { isActiveFl: true }` (DBF-PTY-020 / DBF-PTY-028). Deactivation stays the DELETE of the resource (API-PTY-004 / API-PTY-009). The field is fixed to `true` so the same state change is not reachable through two endpoints with different rules (RULE-PTY-006, RULE-PTY-010).
  - API-PTY-015 — `DefaultAddressRequest { isDefaultFl: true }` (DBF-PTY-040). A default is cleared only by marking another Address of the same type (RULE-PTY-017).
- In the API document the field is `{type: boolean, const: true}` and `additionalProperties: false`. A missing body, a missing field or the value `false` is answered with the existing PTY-400-VALIDATION row (PLATFORM-STD, ADR-PTY-024). No new catalog row is added.

## Consequences
- The body states in the request what the PATCH changes, and the generated client and mock server type it. Behaviour, permissions, orchestration and errors of the three endpoints are otherwise unchanged.
- The frontend sends `{"isActiveFl": true}` / `{"isDefaultFl": true}` with these calls.

## Traces
API-PTY-005, API-PTY-010, API-PTY-015 · REQ-PTY-015, REQ-PTY-021, REQ-PTY-035 · DBF-PTY-020, DBF-PTY-028, DBF-PTY-040 · RULE-PTY-017 · api-spec-pty.yaml (ActivationRequest, DefaultAddressRequest)

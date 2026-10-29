# ADR-PTY-016 — Credit overrun approval as a PTY Credit override request

Status  : ACCEPTED (non-breaking) · Stage : P1 · Module : PTY v1 · Date : 2026-10-29

## Context
POL-PTY-008 and US-PTY-015 / US-PTY-016: a sale above the credit limit either warns or is blocked until a user holding the credit-override permission approves it; the default is block with override. The sale document belongs to SLS, which sits below PTY in the fixed DAG, so PTY cannot reference it. The Approvals module (APR) is wave 3, while PTY and SLS are wave 1. The profile forbids a workflow engine but allows a module-specific approval flow when a story explicitly asks for it.

## Decision
- PTY owns ENT-PTY-005 Credit override request (transactional, SHARED owner): docNo from given `sequence`, the Customer, sale amount, credit limit and open exposure at request, the credit-check result, and the sale held as a text reference (`sourceDocumentRef`).
- Single-step lifecycle (A7): PENDING → APPROVED → USED; PENDING → REJECTED (reason required); PENDING → CANCELLED (requester only). An APPROVED request covers one sale of the same Customer up to its amount.
- A request is accepted only under BLOCK_WITH_OVERRIDE for a result of OVER_LIMIT, NO_CREDIT or EXPOSURE_UNAVAILABLE, never for the walk-in Customer, and never while `b2bCredit` is off. Under WARN no request is needed.
- The credit-override permission is UPDATE on page PTY_CREDIT_OVERRIDE, held by the Owner preset (D-06); Seller/Cashier holds VIEW, CREATE and DELETE (cancel own). Approvers receive an in-app notification through given `notif`.
- The credit-overrun action is a tenant setting in given `cu`, values from lookup CREDIT_OVERRUN_ACTION; no PTY entity re-creates the setting.

## Consequences
- SLS and SFR call create / read / use on the request (A8.1) and keep the request id on their sale.
- If APR later offers generic approval steps, a later version can route the PENDING state through it without changing the request record.

## Traces
US-PTY-015, US-PTY-016, US-PTY-017 · ENT-PTY-005 · REQ-PTY-086 … REQ-PTY-105, REQ-PTY-107 · RULE-PTY-023, RULE-PTY-025 … RULE-PTY-029, RULE-PTY-031 … RULE-PTY-033

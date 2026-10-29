# ADR-PTY-045 — A Credit override request is accepted only under BLOCK_WITH_OVERRIDE

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise 2 (G1) · Module : PTY v1 · Date : 2026-10-29
Revises : ADR-PTY-028 (the bullet "A request may be created under WARN" is withdrawn)

## Context
ADR-PTY-028 and API-PTY-019 allowed a Credit override request to be created while the tenant's credit-overrun action is WARN, "because no rule forbids it". Three upstream statements say otherwise, and upstream wins (GOVERNANCE-CORE §1):
- REQ-PTY-091 creates a request only "while the credit-overrun action is BLOCK_WITH_OVERRIDE";
- ADR-PTY-016 states "a request is accepted only under BLOCK_WITH_OVERRIDE";
- REQ-PTY-088 says that under WARN no request is needed.

The WARN path had no rule, no catalog code and no test case, so an approver could be asked to decide a sale that needs no decision.

## Decision
1. **New rule RULE-PTY-035 — Request only under BLOCK_WITH_OVERRIDE.** The system refuses to create a Credit override request while the tenant's credit-overrun action is not BLOCK_WITH_OVERRIDE. Message — ar: لا حاجة إلى موافقة: إعداد تجاوز الائتمان هو التنبيه فقط، ويمكن إتمام البيع دون طلب. · en: No approval is needed: the credit-overrun setting is Warn only, so the sale can be completed without a request.
2. **New requirement REQ-PTY-114 (unwanted) and AC-PTY-137** state the refusal (WARN, OVER_LIMIT → refused).
3. **Catalog row PTY-422-OVERRIDE-NOT-REQUIRED** (422, RULE-PTY-035), raised by API-PTY-019 only and listed among the 422 codes of `createCreditOverrideRequest` in api-spec-pty.yaml.
4. **Order in API-PTY-019.** The server re-runs the credit check (ADR-PTY-028), whose answer carries the overrun action. `CreditOverrideRequestDomain.create` then applies RULE-PTY-025 (WITHIN_LIMIT → PTY-422-OVERRIDE-NOT-NEEDED) and, after it, RULE-PTY-035 (action ≠ BLOCK_WITH_OVERRIDE → PTY-422-OVERRIDE-NOT-REQUIRED). The action is read through `SettingsApi` in the same request, so a setting changed to WARN takes effect at the next request.
5. **ADR-PTY-028** keeps every other bullet. Its WARN bullet now reads "withdrawn by ADR-PTY-045".

## Consequences
- The sentence "Creation is allowed under WARN too" is removed from API-PTY-019.
- Requests already PENDING when the Owner switches the action to WARN stay decidable and usable; only new creations are refused.
- A backend TC asserts the refusal (TC-PTY-191). The frontend routes the code as a form banner on the New request sub-view.
- CON-PTY-016 gains an error meaning, which is ADDITIVE: no consumer is built yet, and every consumer already raises a request only when CON-PTY-015 answered approvalRequired = yes.

## Traces
REQ-PTY-088, REQ-PTY-091, REQ-PTY-114 · AC-PTY-137 · RULE-PTY-035 · API-PTY-019 · CON-PTY-016 · ADR-PTY-016, ADR-PTY-028

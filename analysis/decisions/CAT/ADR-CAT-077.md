# ADR-CAT-077 — Category changes publish catalogue-change events

Status : ACCEPTED (non-breaking) · Stage : P1 / P1.5 / P3.1 / P3.2 / P4 (gate-analysis round 9, G1) · Module : CAT v2 · Date : 2026-10-28

## Context
- RULE-CAT-048 covers ENT-CAT-006 Category, a SHARED entity, and says that every deactivation of a record in its scope publishes a catalogue-change event with change kind DEACTIVATED.
- REQ-CAT-008, RULE-CAT-049, CON-CAT-018 and the event list of the backend plan's PHASE CORE cover only ENT-CAT-001, 002, 005, 007, 008, 009 and 010. API-CAT-021, 023, 024 and 053 (create, update / move, deactivate and reactivate a Category) record no event and do not honour CON-CAT-018.
- SFR (storefront browse) and INS (reporting) read the Category tree (SRS A3, CON-CAT-011, CON-CAT-012). Without an event they are never told that a Category was created, renamed, moved, deactivated or reactivated, so they cannot refresh a cached tree.

Options:
- (a) add ENT-CAT-006 to the event: REQ-CAT-008, RULE-CAT-049, CON-CAT-018 and PHASE CORE list it, and the four Category writes record CREATED, UPDATED, DEACTIVATED and REACTIVATED;
- (b) remove ENT-CAT-006 from the event clause of RULE-CAT-048, so a Category deactivation publishes nothing.

## Decision
Option (a). The Category is shared and read by two consumers, and the event is how CAT tells consumers about a change without reading them (REQ-CAT-008). Option (b) would leave SFR and INS polling the tree.

1. **Scope.** REQ-CAT-008, RULE-CAT-049 and CON-CAT-018 list ENT-CAT-006 among the entities whose committed changes publish one catalogue-change event.
2. **Payload.** The payload is unchanged: `{entity, recordPk, itemPk, changeKind}`. For a Category, `entity` is ENT-CAT-006, `recordPk` is the categoryPk and `itemPk` is empty, because a Category belongs to no Item.
3. **Writes.** API-CAT-021 records CREATED, API-CAT-023 records UPDATED (a rename, a move or a new sortOrder), API-CAT-024 records DEACTIVATED and API-CAT-053 records REACTIVATED, each inside the change transaction through the given outbox. Each block is marked `Honours: CON-CAT-018`, applies RULE-CAT-049, and lists CAT-500-CHANGE-NOT-SAVED among its errors and in api-spec-cat.yaml.
4. **Contract stability.** Adding a value to the entity list of CON-CAT-018 and allowing an empty itemPk for that value is ADDITIVE: no field is added, removed or retyped, and no consumer edge cites CON-CAT-018 yet (contract §6). A consumer that filters on `entity` ignores the new value.
5. **Not covered.** Variant attributes and values (ENT-CAT-003, 004), scale label layouts and Unit e-invoice codes (ENT-CAT-011, 012) are PRIVATE and still publish no event.

## Consequences
- SRS: REQ-CAT-008 (statement, entities), RULE-CAT-049 (scope, data source), AC-CAT-165 (a Category deactivation publishes one DEACTIVATED event), SCR-REQ-CAT-002 B3 / B5, traceability matrix.
- Contract: CON-CAT-018 signature and traces.
- Backend plan: PHASE CORE "Catalogue-change events", RULE-CAT-049 under ENT-CAT-001, ENT-CAT-006 domain rules, API-CAT-021, 023, 024 and 053, API registry, coverage table, error catalog row CAT-500-CHANGE-NOT-SAVED.
- api-spec: the 500 responses and `x-traces` of API-CAT-021, 023, 024 and 053.
- Frontend plan: the Categories mutations route CAT-500-CHANGE-NOT-SAVED; the REQ-CAT-008 binding lists the four operations.
- Tests: TC-CAT-224 (one event per Category create, update, deactivation and reactivation).

## Traces
REQ-CAT-008 · REQ-CAT-064 · REQ-CAT-065 · REQ-CAT-066 · REQ-CAT-136 · AC-CAT-165 · RULE-CAT-048 · RULE-CAT-049 · CON-CAT-018 · ENT-CAT-006 · API-CAT-021 · API-CAT-023 · API-CAT-024 · API-CAT-053 · TC-CAT-224

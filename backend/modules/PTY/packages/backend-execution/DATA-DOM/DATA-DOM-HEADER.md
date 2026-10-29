<!-- source: PHASE:DATA-DOM — preamble before the first SUB -->
<!-- traces: REQ-PTY-001, REQ-PTY-006, REQ-PTY-024, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-034, REQ-PTY-036, REQ-PTY-039, REQ-PTY-044, REQ-PTY-064, REQ-PTY-091, REQ-PTY-092, REQ-PTY-110, REQ-PTY-111, REQ-PTY-112, REQ-PTY-113, REQ-PTY-114 -->
## PHASE 2 — DATA-DOM

There is one block per entity. Every value is bound from the db-script (tables, columns, constraints, identity clauses) and from the SRS (rules, labels, lookups). The blocks are grouped by kind: four master entities and one transactional entity. The module owns no lookup table; its five lookup keys live in the given mdl tables.

Each block names:
- its JPA entity, which extends the given `AuditableEntity` (TENANT_ID, VERSION and the audit columns — checklist C2);
- its Domain companion, which owns every RULE of the entity (checklist C12);
- its `@Audited` declaration (checklist C16).

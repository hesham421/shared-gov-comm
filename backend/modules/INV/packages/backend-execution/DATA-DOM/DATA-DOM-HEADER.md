<!-- source: PHASE:DATA-DOM — preamble before the first SUB -->
<!-- traces: REQ-INV-001, REQ-INV-003, REQ-INV-020, REQ-INV-022, REQ-INV-035, REQ-INV-038, REQ-INV-043, REQ-INV-051, REQ-INV-059, REQ-INV-078, REQ-INV-087, REQ-INV-097, REQ-INV-106, REQ-INV-124, DBF-INV-001, DBF-INV-014, DBF-INV-026, DBF-INV-040, DBF-INV-066, DBF-INV-087, DBF-INV-101, DBF-INV-115, DBF-INV-129, DBF-INV-143, DBF-INV-158, DBF-INV-176, DBF-INV-191, DBF-INV-205 -->
## DATA-DOM — data and domain

Every entity: table and columns bound by DBF id (properties and types in §3), PK `GENERATED ALWAYS AS IDENTITY`
from db-script INV v1 (`V1002__inv_inventory.sql`), audit / tenant / version from `AuditableEntity`. Each entity
has its `<Entity>Domain` companion in package `com.commerce.inv.domain` (CORE): the DOMAIN RULES line marks a rule the companion holds with "(domain)";
"(app)" marks one the service or the engine checks because it reads another aggregate. Full RULE text: §1.3;
codes and messages: §5. DTO rule for every entity: PK, business code / docNo, audit fields,
version (except on update requests, where it is the optimistic-lock token), tenant and every system-set column
are excluded from create requests and present in every response.

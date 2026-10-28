# CORE-GAP-tenant-lookup-types — Can a tenant create lookup types, with nested values, at runtime?

Status : OPEN (asked of erp-core 1.6.0) · Raised by : CAT v2 (gate-analysis round 3, G11) · Date : 2026-10-28
Decision that depends on it : ADR-CAT-070

## The gap
erp-core checklist C5 requires every user-maintained code list to be an MDL lookup type, and F3 flags a module "types" table. In erp-core 1.6.0, a lookup type is registered by a module's migration (`MDL_LOOKUP_TYPE` with `OWNER_MODULE_CODE`), users maintain only the **values** of a type, values are flat, and a module keeps the value's code with no foreign key into mdl.

CAT's Variant attributes do not fit that model:
- the tenant creates the attributes themselves (SIZE, COLOUR, MATERIAL) at runtime — each would be a new type;
- every attribute has its own values, with codes unique per attribute;
- Variant assignments reference the (value, attribute) pair by a composite foreign key, are read FOR SHARE during generation and counted under a row lock on deactivation.

## What CAT does meanwhile (ADR-CAT-070)
CAT keeps `CAT_VAR_ATTR` and `CAT_VAR_ATTR_VALUE` as its own tables, with an admin screen (SCR-CAT-003). Every fixed CAT code list stays an mdl lookup type.

## Question to the core
1. Can a tenant user create a lookup **type** at runtime, owned by a module, and maintain its values?
2. Can a value carry a parent (another type or value), so values can be nested per type?
3. Can another module's table reference a lookup value by a stable key with referential integrity, or is the code the only reference the core allows?

## Impact when answered
If all three are answered yes, a CAT delta can move the two tables to mdl, keeping the integrity of the assignments. Otherwise the tables stay, and this gap documents the C5 / F3 deviation.

## Traces
ENT-CAT-003 · ENT-CAT-004 · ENT-CAT-005 · ADR-CAT-070

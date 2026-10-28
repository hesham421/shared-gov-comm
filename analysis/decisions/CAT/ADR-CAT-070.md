# ADR-CAT-070 — Variant attributes and their values are CAT tables, not mdl lookup types

Status : ACCEPTED (non-breaking) · Stage : P1 / P2 (gate-analysis round 3, G11) · Module : CAT v2 · Date : 2026-10-28 · Raises : CORE-GAP-tenant-lookup-types.md

## Context
erp-core checklist C5 says every user-maintained code list is an MDL lookup type, seeded in `V1000+` and read through `MdlLookupApi.readActiveValuesByKey`. Checklist F3 flags a module "types" table. CAT_VAR_ATTR and CAT_VAR_ATTR_VALUE look like that: both are user-maintained lists with code, nameAr, nameEn, sortOrder and isActiveFl. ADR-CAT-003 and ADR-CAT-018 justified them against C-GIVEN-1 only; no decision answered C5 and F3 directly.

## What mdl provides in erp-core 1.6.0
- A lookup **type** is registered by a module, by data, in its migration (`MDL_LOOKUP_TYPE` with `OWNER_MODULE_CODE`). Users maintain the **values** of a type through `/api/v1/mdl/**`. The conventions describe no runtime creation of a type by a tenant user.
- Values are flat: a value has no parent value and no parent type other than its own.
- A module stores the value's **code** and validates it at write time. It never stores the `MDL_LOOKUP_VALUE` key, and no foreign key crosses into mdl (conventions § Lookups rule 3).
- `OwnedLookups` serves a **fixed** set of keys that the module owns (checklist C6).

## Decision
Variant attributes and their values stay in CAT_VAR_ATTR and CAT_VAR_ATTR_VALUE. mdl cannot hold them for three reasons:
1. **The tenant defines the types.** An attribute (SIZE, COLOUR, MATERIAL) is created by the owner at runtime, so each attribute would be a new lookup type. mdl types are registered by a module's migration, and `OwnedLookups` serves a fixed key set. A runtime-created type has neither.
2. **Values are nested per attribute.** A value belongs to exactly one attribute, and its code is unique per attribute (UQ_CAT_VAR_ATTR_VALUE_CODE). One flat mdl type for all values would lose that scope; one mdl type per attribute needs point 1.
3. **Assignments need foreign-key integrity.** CAT_VAR_ATTR_ASSIGN references the pair (value, attribute) through the composite FK `FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE`, which guarantees that the value belongs to the attribute (RULE-CAT-013). Generation reads the chosen values FOR SHARE and deactivation counts their usage under a row lock (RULE-CAT-017). A stored mdl code allows none of these.

The fixed CAT code lists (item kind, publish state, brand, unit, tax class, e-invoice types, barcode type, scale value type, change kind) stay mdl lookup types, as C5 requires (SRS A6, ADR-CAT-065). Only the two tenant-defined, nested lists are CAT tables.

## Core gap
Because the reason is a missing core capability, it is raised as `CORE-GAP-tenant-lookup-types.md` (checklist F9). If the core later offers tenant-created lookup types with nested values and a key another table may reference, a CAT delta can move the two tables to mdl, provided the assignment integrity of point 3 is kept.

## Consequences
- No schema change. SRS ENT-CAT-003 and ENT-CAT-004 cite this ADR.
- The CAT admin screen for the two lists (SCR-CAT-003) is the one deviation from "do not rebuild a lookup admin screen", recorded here.

## Traces
ENT-CAT-003 · ENT-CAT-004 · ENT-CAT-005 · RULE-CAT-013 · RULE-CAT-016 · RULE-CAT-017 · ADR-CAT-003 · ADR-CAT-018 · CORE-GAP-tenant-lookup-types

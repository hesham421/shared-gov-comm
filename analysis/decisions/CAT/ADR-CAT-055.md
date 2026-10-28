# ADR-CAT-055 — The given tenant table is a permitted FK reference, not an XM

Status : ACCEPTED (non-breaking) · Stage : P2 — Database (gate-analysis revise, G1) · Module : CAT v2 · Date : 2026-10-28
Supersedes : ADR-CAT-054

## Context
ADR-CAT-054 removed the 12 `FK_<TABLE>_TENANT → CORE_TENANT(ID)` constraints so that `gov.py analyze` C6.15 (db-column-only) would pass. The given library says the opposite, twice:
- the erp-core review checklist, line C1: "Every table has `TENANT_ID BIGINT NOT NULL`, `FK_<TABLE>_TENANT → CORE_TENANT(ID)`, an index led by `TENANT_ID`, every `UNIQUE` composite with `TENANT_ID`";
- the erp-core conventions, Migrations rule 5: "Core tables are referenced by FK only where the core documents it (`CORE_TENANT(ID)`)".

`CORE_TENANT` belongs to the given `erp-core:modules.tenant`, which the given migration chain creates before any application script. It is not a business module, so the reference creates no edge in the module graph. C6.15 guards business-module boundaries; it was applied to a given table it was not written for.

## Decision
1. Every CAT table carries `FK_<TABLE>_TENANT FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID)` (12 constraints, db-script BLOCK 5e), as checklist C1 requires.
2. The given tenant table is a **permitted given reference**. It is not an XM: no XM record is minted, SRS A8 stays `consumes: []`, and the reference is listed with the other given tables in registry-db.
3. Every other reference to a given row stays a soft read with no constraint: `FILE_DOCUMENT_REF` (FILE_DOCUMENT), the lookup codes (MDL_LOOKUP_VALUE) and the number-series codes. Conventions rule 5 allows an FK only to `CORE_TENANT(ID)`.
4. The C6.15 refusal these constraints met in repair round 1 is a defect of the check, not of the script. It is filed as an OPEN platform finding (owner: factory / `gov.py`): C6.15 must exempt the given tables that checklist C1 names (`CORE_TENANT`). The finding is recorded in `business-policies-cat.md` (PLATFORM FINDINGS, row 1).

## Consequences
- D-P2-03 states the FK again; the header counts "tenant FK 12".
- DBF ids, columns and counts are unchanged.
- With the 12 constraints restored, `gov.py analyze --scope all` (2026-10-28, after this revise) reports no C6.15 finding: the clause examined 0 subjects, because CAT has no XM. In repair round 1 the same clause refused these constraints, and ADR-CAT-054 removed them in response. The exemption for given tables is therefore implicit and unstable. The platform finding asks the factory to make it explicit, so a later run cannot refuse the C1 FK again.

## Traces
REQ-CAT-001 · REQ-CAT-132 · ENT-CAT-001 · ENT-CAT-012

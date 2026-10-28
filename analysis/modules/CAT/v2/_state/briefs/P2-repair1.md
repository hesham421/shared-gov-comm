# REPAIR BRIEF — stage `P2` (Database) · module CAT · v2 · round 1

## What to do
You wrote this stage's files, and `gov.py analyze` refused them with the 1 finding(s) below.
Read the files at the absolute paths listed (an exception to the base brief's 'read only what this brief
contains'), fix EVERY finding by editing those files in place with your file tools, and change nothing else:
never re-number an id, never restart a sequence, never raise a `[QUESTION]`.
A finding that names an artifact this stage does not write is fixed from THIS stage's side (a test that asserts
a text the plan does not bind asserts the code and the control instead; a value nothing seeds is replaced by a
seeded one or created by the test's own precondition). Where a fix needs a choice, take the best-practice one and
record it as an ADR (`analysis/decisions/CAT/ADR-{MOD}-{seq:03d}.md`, the next free id, status ACCEPTED).
End with a short list of what you changed. The files you may edit:
- `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P2\db-script-cat.md`
- `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P2\registry-db-cat.md`

This repair follows a gate revise, which edited the earlier stages too. A finding whose fix belongs in
one of THEIR files (a `given-usage` row lives in the SRS and is repeated in the contract) is fixed there,
in place, keeping both sides in step. Those files:
- `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P0\platform-summary.md`
- `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P0\module-registry-cat.md`
- `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P0\business-policies-cat.md`
- `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P0_5\prd-cat.md`
- `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P1\srs-cat.md`
- `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P1\registry-srs-cat.md`
- `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P1_5\contract-cat.md`

## Findings to fix
- [MAJOR] C-GIVEN-2 (given-via-extension) backend-execution-plan — `backend-execution-plan` names `FilePrivateStoreApi` (erp-core:modules.file interfaces) and no `given-usage` row declares that use — add the row (erp-core · file · com.erp.file.crossmodule.FilePrivateStoreApi · purpose) to the `given-usage` block of `srs` and repeat it in `contract`, or stop using it

---
# THE STAGE'S BRIEF (its rules still bind; its inputs are below)

# BRIEF — stage `P2` (Database) · module CAT · v2 · profile `commerce`

Lane `analysis` · implementer ['claude:opus'] · effort high · round 1

## Rules that bind this run
- Questions: **forbidden**. A `[QUESTION]` block is refused. Ambiguity → ADR in `analysis/decisions/CAT/` (`ADR-{MOD}-{seq:03d}.md`): non-breaking → continue; breaking → status BLOCKED and stop.
- Owns IDs: DBF, XM — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-CAT-077`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files; this is a delta version, so only what changed):
- `governance-shared\analysis\modules\CAT\v2\P2\db-script-cat.md` — absolute path `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P2\db-script-cat.md`
- `governance-shared\analysis\modules\CAT\v2\P2\registry-db-cat.md` (registry) — absolute path `C:\Users\moe133212665\source\repos\commerce\governance-shared\analysis\modules\CAT\v2\P2\registry-db-cat.md`
- `governance-shared\analysis\modules\CAT\v2\change-manifest.md` — fill the `artifacts` map of its `change-manifest` block for what you wrote (ADDED / MODIFIED / REMOVED / UNCHANGED ids); it is ONE file per version, at the version root, never in a stage folder.
- Write each file above, complete, with your Write tool at its ABSOLUTE path (a long file does not fit in one response); write a long file in parts if you must (Write the first part, then append with Edit), and end with a short summary of what you wrote. Write nowhere else. Only if you cannot write, respond with one `<<<FILE: path/from/the/repo/root.md>>>` … `<<<END FILE>>>` block per file.

## Contracts checked by `gov.py analyze` after this stage
- **C5** SRS → database: C5.1 exists {'artifact': 'srs'} [CRITICAL]; C5.2 ears {'kind': 'REQ', 'patterns': 'factory.ids.ears.patterns'} [CRITICAL]; C5.3 traces {'from': 'REQ', 'to': ['US'], 'min': 1} [MAJOR]; C5.4 orphans {'kind': 'REQ', 'referenced_by': ['AC'], 'min': 1} [CRITICAL]; C5.5 traces {'from': 'AC', 'to': ['REQ'], 'min': 1} [MAJOR]; C5.6 traces {'from': 'RULE', 'to': ['REQ'], 'min': 1} [MAJOR]; C5.7 ids-owned {'stage': 'P1'} [CRITICAL]; C5.8 registry-agree {'artifact': 'srs', 'registry': 'registry-srs', 'kinds': ['REQ', 'AC', 'ENT', 'RULE']} [MAJOR]; C5.9 no-questions {'stage': 'P1'} [CRITICAL]; C5.10 languages {'stage': 'P1'} [MAJOR]; C5.11 ids-continue {'stage': 'P1'} [CRITICAL]; C5.12 data-source {'kind': 'RULE', 'label': 'Data source', 'resolves_to': ['ENT'], 'deferral': 'DEFERRED'} [CRITICAL]; C5.13 ambiguity {'artifact': 'srs', 'kinds': ['REQ', 'AC', 'RULE'], 'lines': ['Statement', 'Given', 'When', 'Then'], 'lexicon': 'analyze.maturity.ambiguity_lexicon', 'extend': 'review.ambiguity_lexicon'} [MINOR]; C5.14 ac-measurable {'artifact': 'srs', 'kind': 'AC', 'label': 'Then', 'spec': 'analyze.maturity.measurable'} [MINOR]; C5.15 crud-covered {'artifact': 'srs', 'entity': 'ENT', 'kind': 'REQ', 'statement': 'Statement', 'spec': 'analyze.maturity.crud'} [MINOR]; C5.16 feature-unwanted {'artifact': 'srs', 'group': 'US', 'kind': 'REQ', 'statement': 'Statement', 'pattern': 'unwanted'} [MINOR]; C5.17 glossary {'artifact': ['srs'], 'glossary': 'vocabulary.glossary', 'synonyms': 'vocabulary.glossary_synonyms'} [MINOR]
- **C6** SRS + database → backend execution plan: C6.1 exists {'artifact': 'db-script'} [CRITICAL]; C6.2 traces {'from': 'DBF', 'to': ['REQ', 'ENT'], 'min': 1} [MAJOR]; C6.3 traces {'from': 'XM', 'to': ['REQ'], 'min': 1} [MAJOR]; C6.4 ids-owned {'stage': 'P2'} [CRITICAL]; C6.5 registry-agree {'artifact': 'db-script', 'registry': 'registry-db', 'kinds': ['DBF', 'XM']} [MAJOR]; C6.6 orphans {'kind': 'ENT', 'referenced_by': ['DBF'], 'min': 1} [MAJOR]; C6.7 no-questions {'stage': 'P2'} [CRITICAL]; C6.8 ids-continue {'stage': 'P2'} [CRITICAL]; C6.9 data-source {'kind': 'RULE', 'label': 'Data source', 'resolves_to': ['ENT'], 'deferral': 'DEFERRED', 'bound_in': 'db-script'} [CRITICAL]; C6.10 xm-record-valid {'artifact': ['db-script', 'registry-db']} [MAJOR]; C6.11 xm-tier-rule {} [CRITICAL]; C6.12 xm-transition-valid {'artifact': ['db-script', 'registry-db']} [MAJOR]; C6.13 graph-acyclic {} [CRITICAL]; C6.14 graph-target-known {} [CRITICAL]; C6.15 db-column-only {'artifact': 'db-script', 'register': ['db-script', 'registry-db']} [MAJOR]
- **C13** contract (outbound promise) → database and every consumer: C13.1 exists {'artifact': 'contract'} [CRITICAL]; C13.2 ids-owned {'stage': 'P1.5'} [CRITICAL]; C13.3 no-questions {'stage': 'P1.5'} [CRITICAL]; C13.4 ids-continue {'stage': 'P1.5'} [CRITICAL]; C13.5 traces {'from': 'CON', 'to': ['ENT', 'REQ'], 'min': 1, 'mode': 'any'} [MAJOR]; C13.6 languages {'stage': 'P1.5'} [MAJOR]
- **C-GIVEN** given dependency → every stage: C-GIVEN-1 given-no-duplicate {'spec': 'capabilities.given', 'entity': 'ENT', 'srs': 'srs', 'db': 'db-script', 'when': 'factory.capabilities.given'} [MAJOR]; C-GIVEN-2 given-via-extension {'spec': 'capabilities.given', 'srs': 'srs', 'contract': 'contract', 'plan': 'backend-execution-plan', 'when': 'factory.capabilities.given'} [MAJOR]

## Blocks this stage emits — each a ```yaml name={name} fence, held to its schema by C0.1 (`gov.py analyze`)
- `dbf-matrix` in `db-script` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"dbf-matrix","description":"P2 db-script §2: the DB field traceability matrix — every DBF the module owns, the physical column it is, the ENT.field and the REQ ids it traces to. The single canonical source of DBF → column (value-agreement C7.10 binds the backend plan to it); the ENT and REQ ids in a row are the record's traces (C6.2, C6.6).","type":"object","required":["rows"],"additionalProperties":false,"properties":{"rows":{"type":"array","minItems":1,"items":{"type":"object","required":["id","table","column","type","entity_field","traces"],"additionalProperties":false,"properties":{"id":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"},"table":{"type":"string","pattern":"^[A-Za-z_][A-Za-z0-9_.]*$"},"column":{"type":"string","pattern":"^[A-Za-z_][A-Za-z0-9_]*$"},"type":{"type":"string"},"entity_field":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}\\.[A-Za-z_][A-Za-z0-9_]*$"},"traces":{"type":"array","minItems":1,"items":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"}},"nullable":{"type":["string","boolean","null"],"description":"NOT NULL / NULL (YAML reads a bare NULL as null), or a boolean"},"default":{"type":["string","number","boolean","null"],"description":"the column default, or nothing"}}}}}}
  ```
- `xm-register` in `db-script` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"xm-register","description":"P2 db-script: every cross-module dependency of this module — one record per XM, the fields of factory.yaml → xm.record (the state machine's own vocabulary; xm-record-valid checks the values)","type":"object","required":["records"],"additionalProperties":false,"properties":{"records":{"type":"array","items":{"type":"object","required":["id","type","target_module","target_entity","traces","state"],"additionalProperties":false,"properties":{"id":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"},"type":{"type":"string"},"target_module":{"type":"string","pattern":"^[A-Z][A-Z0-9]*$"},"target_entity":{"type":"string"},"traces":{"type":"array","items":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"}},"state":{"type":"string"},"contract_ref":{"type":"string"},"workaround":{"type":"string"},"unblock_condition":{"type":"string"},"column":{"type":"string"}}}}}}
  ```

## The previous run of this stage was refused by `gov.py analyze` — fix exactly these, keep everything else
- [MAJOR] C-GIVEN-2 (given-via-extension) `backend-execution-plan`: `backend-execution-plan` names `FilePrivateStoreApi` (erp-core:modules.file interfaces) and no `given-usage` row declares that use — add the row (erp-core · file · com.erp.file.crossmodule.FilePrivateStoreApi · purpose) to the `given-usage` block of `srs` and repeat it in `contract`, or stop using it

---
# ENGINE
# Database — ENGINE

```
Engine        : Database
Stage id      : P2
Pass          : 1
Questions     : forbidden — ambiguity is self-resolved (§9)
Lane          : analysis
Inputs        : srs, registry-srs, contract, contracts?
Produces      : db-script-{mod}.md · registry-db-{mod}.md (registry)
Owns IDs      : DBF, XM   → `{prefix}-{MOD}-{seq}` (seq width 3)
Dialect       : postgresql16   (profile.stack.db.target_dialect; syntax from profile.stack.db.syntax_map)
Next          : P3.1
Module        : CAT   Version: 2
Profile       : commerce — COMMERCE
```

This engine produces the module's **structural truth**: an executable database script
derived from the SRS, a field-level traceability matrix and the cross-module dependency
register. It invents no business logic and does not redesign SRS meaning; when the script [G]
and the SRS disagree, the SRS governs and the script is corrected. It produces no
execution phases and no implementation sequencing.

Completion (write → registry → analyze → commit) is owned by the orchestrator — see
`shared/GOVERNANCE-CORE.md`. **Delta mode is active (v2)**: read
`_state/current-{artifact}` of the previous version for every input and for this
stage's own artifacts, and emit only ADDED / MODIFIED / REMOVED elements plus the [C:C12.2]
`change-manifest.md` per `shared/VERSIONING.md` — the script of a delta version is a
migration (ALTER / CREATE / DROP for the changed objects only); sequences continue. [C:C12.2]

### IDs this stage assigns

| Atom | Meaning | Traces to |
|---|---|---|
| `DBF` | db field | REQ, ENT |
| `XM` | cross-module dependency | REQ |

---

## 1 — Inputs and entry check

```
  srs             : ✓ present / ✗ MISSING (pipeline error — not a question)
  registry-srs    : ✓ present / ✗ MISSING (pipeline error — not a question)
  contract        : ✓ present / ✗ MISSING (pipeline error — not a question)
  contracts?      : ✓ present / ✗ MISSING (pipeline error — not a question)
  domain-profile STEERING : vocabulary verbatim; identifier rules
  project-registry        : structural registry (names fixed by other modules),
                            shared declarations; the dependency graph (gov.py graph)
  Extracted               : [N] entities → [N] tables · [N] intra-module FKs ·
                            [N] XM candidates (SRS A8) · [N] lookups (SRS A6)
```

Reading protocol for the SRS: PART A entirely — A3 (entities, fields, logical types),
A5 (rules → constraints), A6 (lookups → seed data), A7 (status → check constraints),
A8 (consumed entities → XM). PART B is not read for structure (screens are not tables).

---

## 2 — DB field traceability matrix (`DBF`)

The single canonical source of `DBF` → column → type → SRS origin. Downstream
artifacts (the backend plan's alignment manifest) reference columns **by DBF id only** [C:C7.10]
and never restate column names, types or SRS references. [C:C7.10]

ONE named block (data, not prose — `gov.py analyze` reads it: C6.2 traces, C6.6 orphans, C7.10 value-agreement; C0.1 holds it to its schema), one row per DBF: [C:C0.1]

```yaml name=dbf-matrix
rows:
  - {id: DBF-CAT-001, table: <table>, column: <column>, type: <postgresql16 type>, entity_field: ENT-CAT-001.<field>, traces: [REQ-CAT-…], nullable: NOT NULL, default: "—"}
```

Then, for the reader: `Total: [N] DBF ids across [N] tables`.

```
ASSIGNMENT RULES
  - Sequence continuous across the module (not per table); never reused, even for a [C:C6.8]
    removed column.
  - Per table: PK first, then the entity's own columns in SRS order, then FK columns,
    then standard columns (audit fields last).
  - Every column traces to an ENT.field of the SRS AND to ≥ 1 REQ (via the entity's
    requirements); a standard column traces to the profile default that mandates it
    ("profile: entity_defaults.<kind>") and to the ENT.
  - A column with no SRS origin does not exist (NO-COLUMN-INVENTION, §3).
```

---

## 3 — Naming and column rules

```
IDENTIFIER TRANSFORMATION (stated once in the script header, applied everywhere)
  logical field name (SRS)  →  physical column name: one deterministic transformation
  (case + word separator) declared for postgresql16 — never two spellings of one field. [C:C7.10]
  Respect the dialect's identifier length limit and reserved words.

TABLE NAMES     : [module code]_[entity abbreviation] — module code from the domain-profile
PRIMARY KEY     : the SRS field named by `{entity}Pk` (profile.stack.db.naming.pk_pattern)
FOREIGN KEYS    : the SRS reference field; constraint FK_[LOCAL]_[REF] (FK_[LOCAL]_[REF]_[n] when several)
AUDIT COLUMNS   : createdBy, createdAt, updatedBy, updatedAt on every table that carries them per its entity kind —
                  filled by the platform, not by a client; user columns hold a [G]
                  principal string, not a numeric FK
FLAG COLUMNS    : end with `Fl`; type BOOLEAN; soft-deactivate flag defaults to active —
                  there is no "deleted" column: deactivation, not deletion
INDEXES         : IDX_[TABLE]_[COLUMN] (composite: IDX_[TABLE]_[ABBR1]_[ABBR2])
CONSTRAINTS     : PK_[TABLE] · UQ_[TABLE]_[COL] · CHK_[TABLE]_[COL]
SEQUENCES       : `SEQ_{TABLE}` (profile.stack.db.naming.sequence_pattern) — emitted only when [G]
                  profile.stack.db.pk_generation is `sequence` (§4)

NO-COLUMN-INVENTION (CRITICAL)
  Every column is (1) an SRS field, or (2) a profile default for the entity's kind,
  or (3) derived from an FK / XM. Nothing from generic templates or prior examples.
  Modules declared EXCEPTION in the registry keep their real names as-is.
```

---

## 4 — Table definition rules

```
For every ENT in SRS A3 → one table (consumed SHARED entities are NOT re-created).
A table a given already owns (`erp-core:modules.audit`: CORE_AUDIT_EVENT; `erp-core:modules.cu`: CU_APP_CONFIGURATION; `erp-core:modules.file`: FILE_CATEGORY, FILE_DOCUMENT; `erp-core:modules.mdl`: MDL_LOOKUP_TYPE, MDL_LOOKUP_VALUE; `erp-core:modules.notif`: NOTIF_CHANNEL_CONFIG, NOTIF_INBOX, NOTIF_LOG, NOTIF_LOG_ATTACHMENT, NOTIF_TEMPLATE; `erp-core:modules.sec`: SEC_ACTION_REG, SEC_AUDIT_LOG, SEC_MODULE_REG, SEC_ROLE, SEC_ROLE_ACTION_GRANT, SEC_ROLE_MODULE_GRANT, SEC_ROLE_SCREEN_GRANT, SEC_SCREEN_REG, SEC_SIGNUP_REQUEST, SEC_USER, SEC_USER_ROLE; `erp-core:modules.sequence`: CORE_NUMBER_SERIES; `erp-core:modules.tenant`: CORE_TENANT) is
  NOT created again — the module reads it through the given's interface, never through a second table. [C:C-GIVEN-1]
Each table block, in order:
  CREATE TABLE (all columns, inline NOT NULL, inline CHECK)
  COMMENT ON TABLE + COMMENT ON COLUMN for every column (the comment cites the DBF id)
  PRIMARY KEY · UNIQUE (from RULEs) · CHECK (from RULEs / status values)
  FK constraints — intra-module only; a cross-module FK is column-only (§5.1) [C:C6.15]
PK GENERATION — profile.stack.db.pk_generation = `identity` (a PROFILE decision, not a [G]
  dialect default; the same database may not carry two PK strategies)
IDENTIFIERS Profile rule: every entity identifier is a BIGINT identity key the database assigns (stack.db.pk_generation = identity); a contract states identifier type BIGINT; a consumer's HARD-FK column is BIGINT; no other identifier type exists in v1 — every PK column and every [G]
  cross-module FK column (§5.1) is typed by this rule and by the target's contract, which states the same.
  Strategy `identity`: the PK column carries the postgresql16 identity clause from
  profile.stack.db.syntax_map.identity:
      GENERATED ALWAYS AS IDENTITY
  BLOCK 1 stays empty (state "none: every PK uses the identity clause") unless the SRS
  needs a sequence for something other than a PK.
  NEVER a trigger for PK population; NEVER a default that calls a sequence on the PK column.
```

### 4.1 Datatype governance (profile.stack.db.syntax_map → postgresql16)

| Logical type (SRS) | postgresql16 syntax |
|---|---|
| pk | BIGINT |
| string | VARCHAR(n) |
| boolean | BOOLEAN |
| timestamp | TIMESTAMPTZ |
| decimal | NUMERIC(p,s) |
| text | TEXT |
| json | JSONB |

(`identity` and `sequence` are not column types — they are the PK-generation clauses of
§4, selected by `profile.stack.db.pk_generation`, and are listed there only.) [G]

Rules: only the syntaxes above (or one stated once in the script header for a logical [G]
type the map lacks); `n` / `p,s` are filled from the SRS field definition; a
deviation carries a governance note citing the SRS field that requires it; the other
declared dialects (none) are not emitted — one dialect per script.

### 4.2 Lookup and reference data

Profile rule: all LOV values runtime-loaded from the core lookup module (mdl); no hardcoded enums in APIs or field specs; activity presets and capability flags are core settings, read through FeatureFlags
```
Lookup-backed field (SRS A6, control = lookup) → the stored value is the lookup CODE
  (not a numeric key); seed rows for every value the SRS lists, each block citing [G]
  the SRS lookup key; the shared lookup tables (if the platform uses them) are created
  once by the first module that needs them and only seeded afterwards — not [G]
  re-created in a later module's script.
SEED ROWS FOR A TABLE THIS SCRIPT DOES NOT CREATE — required, not optional. The common
  case is that the lookup tables belong to ANOTHER module, and a seed block written only [G]
  for tables this script creates is then EMPTY: nothing anywhere in the pipeline ever
  creates the values, and the module's first create call fails validating a code against
  an empty table. Emit the INSERT block for every key this module owns, against the owning
  module's table by its exact name, and say which module owns it. Whichever module owns the
  table, the module that owns the KEY owns the seed.
Reference entity (SRS decided: its own ENT) → an ordinary table per §4; consumers
  hold an FK.
```

### 4.3 Indexes

```
Mandatory : every FK column; every column used in SRS search / list filters (PART B B2);
            every UNIQUE business key. PK indexes are implicit — not duplicated. [G]
```

---

## 5 — XM register (`XM`) — cross-module dependencies

`XM` is the single identifier for every cross-module dependency in the pipeline.
Its types, states, transitions and record fields are the ONE state machine of
`factory.yaml → xm` (rendered in `shared/XM-PROTOCOL.md`) — never a list of this [L:C3-xm-vocabulary]
engine's own. Assigned here; wired by `P3.1`, whose last phase carries one
integration block per edge (shared/XM-PROTOCOL.md §6) — never re-assigned; never touched by [C:C9.9]
the frontend stage. The factory's lifecycle ends at DELIVERED; the states after it belong to the
consumer repository.

The register is ONE named block (data, not prose — `gov.py graph`, `gov.py analyze` C6.10/C6.12 and the gate read it; C0.1 holds it to its schema; `gov.py graph` appends the edges later stages mint):

```yaml name=xm-register
records:
  - {id: XM-CAT-001, type: HARD-FK, target_module: [CODE], target_entity: ENT-[CODE]-[SEQ], traces: [REQ-CAT-[SEQ]], state: [state], contract_ref: "[contract item, when the state needs it]", workaround: "[when the state needs it]", unblock_condition: "[when the state needs it]", column: "[table] · [FK column / access]"}
```

<!-- RENDER:xm:types -->
Types: `HARD-FK` · `SOFT-READ` — no other type exists. [C:C6.10]
<!-- /RENDER:xm:types -->

<!-- RENDER:xm:record -->
| Field | Column / line label | Required |
|---|---|---|
| `id` | `XM id` | always |
| `type` | `Type` | always |
| `target_module` | `Target module` | always |
| `target_entity` | `Target entity` | always |
| `traces` | `Traces` | always |
| `state` | `State` | always |
| `contract_ref` | `Contract` | state `CONTRACTED` |
| `workaround` | `Workaround` | state `DEFERRED` |
| `unblock_condition` | `Unblock condition` | state `DEFERRED` |
<!-- /RENDER:xm:record -->

```
STATE AT THIS STAGE (one of xm.states.order; a gate never opens on DECLARED) [T:xm-gate]
  the target's published contract (platform/contracts/) carries the item   → CONTRACTED, Contract = its CON id
  the target entity exists in the target module's committed artifacts        → READY
  the target is neither contracted nor built                                  → DEFERRED, with Workaround + Unblock condition
  (whatever the state, a HARD-FK is column-only here — §5.1)
TYPES
  HARD-FK    physical FK constraint on the owner's table
  SOFT-READ  application-level read of another module's table, no FK column; the
             "column / access" cell describes the access pattern
SOURCES
  SRS A8 consumed entities (the `module-dependencies` block — type as classified there);
  RULEs that join by code to another module's table; APIs that read another module's data.
  An XM with no SRS A8 origin is an ORPHAN finding. Audit columns are not XMs. [G]
```

### 5.1 Cross-module FK — column only [C:C6.15]

```
For every HARD-FK XM, whatever its state: create the column (with its DBF id), nullable as the
SRS field says, typed EXACTLY as the target's published contract states its identifier type (the [G]
CON entity item — it is a promise, not a guess: a type inferred here is the ambiguity that stops
a later stage), and put ONE pointer on it — in its column comment or on the line beside it:
  -- constraint → XM-CAT-[n]
Do NOT create the constraint, live or commented. Its text is written once, in full, in the
integration block of the backend plan (P3.1 — shared/XM-PROTOCOL.md §6), which the
executor applies when the target is there. A second copy here is the drift this rule removes
(`gov.py analyze` → C6.15 `db-column-only`, C7.28 `patch-single-source`).
```

### 5.2 SOFT-READ handling

```
For every SOFT-READ XM: register it (Type SOFT-READ); add a commentary block:
  -- XM-CAT-[n] SOFT-READ — this module's [service/query] reads [TARGET].[COLUMN]
  -- from [module] without an FK. Rationale: [from SRS]. Risk: changes to [TARGET]
  -- require impact assessment on [affected requirements].
```

---

## 6 — FK classification (every FK is exactly one of these) [G]

```
INTRA-MODULE FK    both tables in this script → constraint in main DDL; DBF on the column; no XM
CROSS-MODULE FK    the target is another module's table → column + pointer, no constraint (§5.1); XM HARD-FK
SOFT-READ          application read → no constraint by design; XM SOFT-READ (§5.2)
```

---

## 7 — `db-script-{mod}.md` — output structure

```
1. HEADER          module · version · dialect postgresql16 · schema prefix (or "none") ·
                   identifier transformation (§3) · date · counts
2. DB FIELD TRACEABILITY MATRIX   (§2 — governance documentation, not SQL)
3. XM REGISTER                    (§5)
4. FULL_DATABASE_SCRIPT           (§7.1 — the ONLY place SQL appears)
5. DECISIONS APPLIED              (DEFAULTs + ADR ids, §9)
6. REGISTRY CONTENT               (§10)
```

### 7.1 FULL_DATABASE_SCRIPT — one consolidated executable

Copy-and-run against a clean schema of postgresql16 without editing. Not documentation:
a deployable, inside ONE ```sql fence — the only place SQL appears; every DDL reader [C:C6.15]
(`gov.py analyze` C6.15, C7.28, the tables this script creates) reads that fence and nothing else.
Mandatory block order (guarantees zero dependency errors):

```
BLOCK 1   SEQUENCES [G] (only if the SRS needs one; PK generation uses the identity clause)
BLOCK 2   PARENT TABLES (no FK dependencies; lookup/reference tables DDL only) [G]
BLOCK 3   CHILD TABLES (intra-module FK targets already created; chain A → B → C)
BLOCK 4   COMMENTS (table + every column; each column comment cites its DBF id)
BLOCK 5   CONSTRAINTS  5a PK · 5b UNIQUE · 5c CHECK · 5d intra-module FK (parent PK first)
BLOCK 6   TRIGGERS — audit triggers only when an SRS RULE requires them; no PK triggers [G]
BLOCK 7   INDEXES (non-PK)
BLOCK 8   LOOKUP SEED DATA (INSERT with column lists; COMMIT at the end of the block)
BLOCK 9   VIEWS (CREATE OR REPLACE)
BLOCK 10  FUNCTIONS / PROCEDURES (dialect terminator syntax; only if the SRS needs them) [G]
```

```
SYNTAX RULES (dialect-conditional — the dialect's own syntax comes from
profile.stack.db.syntax_map; the rules below hold for any dialect)
  S-1  Every statement ends with the dialect's terminator; no trailing comma before a
       closing parenthesis; every referenced object has its CREATE in this script.
  S-2  Types: only §4.1 syntaxes; not a type from another dialect. [G]
  S-3  Constraints in ALTER TABLE form (PK / FK / UQ / CHK names per §3); FK declared
       after the parent PK exists.
  S-4  No PK-population trigger; no sequence default on a PK column.
  S-5  Seed INSERTs carry a column list; NULL is not the string 'NULL'; COMMIT after DML. [G]
  S-6  No constraint references another module's table — live or commented (§5.1).
  S-7  Schema prefix: all objects qualified, or none — not mixed. [G]
  S-8  No placeholders: no "...", no "[...]" inside SQL — real names and values only. [G]
```

### 7.2 Self-verification before emitting the script

```
SYNTAX        □ S-1 … S-8 hold for every statement
              □ every type appears in §4.1 (or is declared once in the header)
PK STRATEGY   □ every PK follows profile.stack.db.pk_generation (`identity`) — the identity clause on every
                PK column and no PK sequence
ORDER         □ sequences first (if any) · parents before children · PK before FK ·
                lookup DDL before lookup INSERTs · COMMIT after the last INSERT of a block
COMPLETENESS  □ every SRS entity has a table · every SRS field a column (DBF) ·
                every lookup its seed rows · every index present ·
                shared lookup tables not re-created
CROSS-MODULE  □ every HARD-FK XM has its column and its pointer · no constraint,
                live or commented, references another module's table
TRACE         □ every DBF traces to ENT.field + REQ · every XM traces to REQ and to an
                SRS A8 row · no orphan, no dangling id
```

---

## 8 — Governance recovery

```
A module whose script was produced from an incomplete or corrected SRS, or whose
script arrives after downstream artifacts exist:
  1. Re-run this stage on the current SRS (_state/ current state).
  2. Re-run `gov.py analyze` on the affected artifacts (this script, the backend plan's
     alignment manifest, the registries). Findings are resolved at the next pass gate.
  3. XM rows whose target moved (contract published, built, delivered) → the next state along
     shared/XM-PROTOCOL.md §4 — `gov.py graph` raises the resolution event.
No separate audit stage exists; `analyze` is the recovery check.
```

---

## 9 — Ambiguity rule (questions are forbidden here) [T:questions-forbidden]

```
Structural choices the SRS does not settle (normalisation of a repeating group, a
composite vs surrogate key, an index strategy, a precision):
  NON-BREAKING → ADR, CONTINUE with the chosen best practice
      action: adr · then: continue
  BREAKING (contradicts an SRS REQ / ENT, a registered name, or a gated module's
  structure) → ADR status BLOCKED, STOP the pass
      action: adr · status: BLOCKED · then: stop
ADR file : analysis/decisions/CAT/ADR-{MOD}-{seq:03d}.md  (Context · Decision · Consequences · traces · status)
Details  : shared/GOVERNANCE-CORE.md
```

---

## 10 — `registry-db-{mod}.md` — registry content

```
## REGISTRY — P2 — CAT v2
Tables        : table · ENT id · kind · DBF range              (→ structural registry)
XM index      : the ids of the register block above                (→ gov.py graph)
Lookups       : key · seeded values count · owner
Sequences     : last DBF · last XM
Decisions     : ADR ids (+ BLOCKED, if any)
Event         : "P2 completed: CAT v2 — [N] tables, [N] DBF, [N] XM"
Cascade       : none by hand — `gov.py graph` derives the edges targeting CAT and raises
                their resolution events (shared/XM-PROTOCOL.md §5)
```

---

## 11 — Boundaries

```
OWNS      : DBF, XM · the traceability matrix · the XM register · DDL structure,
            naming and datatype governance for this module
DOES NOT  : POL (P0) · US (P0.5) · REQ (P1) · AC (P1) · ENT (P1) · RULE (P1) · CON (P1.5) · API (P3.1) · QR (P3.1) · UXD (P3.2) · SCR (P3.2) · SCR-REQ (P1) · TC (P4) · FEAT (feature)
            · business logic · execution phases · frontend structure (the frontend stage
            does not read this script; it consumes the API document) [G]
```

---

## 12 — Self-check before emitting

- [ ] Every SRS entity → one table; every consumed SHARED entity → FK / XM, not a table.
- [ ] Every column has a DBF id, a type from §4.1, a comment, and traces (ENT.field + REQ).
- [ ] Every cross-module reference is exactly one FK class (§6) and, unless intra-module, an XM row traced to REQ + SRS A8. [G]
- [ ] Every HARD-FK XM has its column and its pointer `constraint → XM-…`; no cross-module constraint, live or commented.
- [ ] Script block order 1–10 respected; §7.2 checklist passed; script is copy-and-run for postgresql16.
- [ ] PK generation matches `profile.stack.db.pk_generation` = `identity` for **every** table (§4) — no second strategy anywhere.
- [ ] Every RULE that maps to a constraint is present (UNIQUE / CHECK) and named per §3.
- [ ] Every DEFAULT / ADR listed under Decisions applied; no BLOCKED ADR unless the pass stopped.
- [ ] No question raised; sequences continuous; continued from v1 current state; the script is a migration.
- [ ] Profile check `CM-3` (MINOR): every flag column ends with the flag_suffix.
- [ ] Profile check `CM-8` (MAJOR): every stock-bearing row carries warehouse_id, every monetary row carries its currency and every price its price list — the dimension exists from day one even while the tenant has one (brief §6.8).


---
# INPUTS (generated current state)

<<<INPUT: srs>>>
# SRS — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module : CAT   Version : v2   Profile : commerce
Inputs : prd, domain-profile, project-registry (PRD approved 2026-10-26 — gate prd-approval)
Counts : REQ 147 · AC 164 · ENT 12 · RULE 53 · SCR-REQ 6 · ADR 19
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information

| Item | Value |
|---|---|
| Module | CAT — Catalog / الكتالوج |
| Feature code | CAT |
| Version | v2 (baseline v1; change set CS-CAT-001 ADDITIVE). No SRS exists in the current state, so this SRS is written in full and every P1 sequence starts at 001 |
| Date | 2026-10-26 |
| Status | DRAFT — P1 pass 1 |
| Prepared by | claude:opus (analysis lane) |
| Decisions applied | 19 ADR (ADR-CAT-038 … ADR-CAT-043; ADR-CAT-058, ADR-CAT-059, ADR-CAT-060, ADR-CAT-062 at the gate-analysis revise; ADR-CAT-063, ADR-CAT-064, ADR-CAT-065 at the second gate-analysis revise; ADR-CAT-068, ADR-CAT-069, ADR-CAT-070 at the third gate-analysis revise; ADR-CAT-072 at the sixth gate-analysis revise; ADR-CAT-074 at the seventh gate-analysis revise; ADR-CAT-076 at the eighth gate-analysis revise) · 15 DEFAULT (D-01 … D-15) |
| Revisions (third gate-analysis revise) | ADDED: REQ-CAT-136 … REQ-CAT-138, AC-CAT-153 … AC-CAT-160 · MODIFIED: REQ-CAT-135 and RULE-CAT-053 (three repair operations stay allowed on an inactive Item — ADR-CAT-068), RULE-CAT-003, AC-CAT-007 and AC-CAT-043 (the message names the existing Item's code), RULE-CAT-016, RULE-CAT-023 and RULE-CAT-024 (also checked on reactivation — ADR-CAT-069), A7 |
| Revisions (fourth gate-analysis revise) | ADDED: REQ-CAT-139, AC-CAT-161 (an import past its time limit is rolled back whole — ADR-CAT-071) · MODIFIED: REQ-CAT-005 and A7 (deactivating an Item leaves its barcodes active; a GTIN is released by deactivating the barcode), RULE-CAT-049 (traces REQ-CAT-139) |
| Revisions (sixth gate-analysis revise) | ADDED: AC-CAT-162 (reactivation refused when another active Item took the name), AC-CAT-163 and AC-CAT-164 (the default Variant moves — ADR-CAT-072) · MODIFIED: REQ-CAT-025, A7 and ENT-CAT-002.isDefaultFl (an active Item always has exactly one active default Variant), REQ-CAT-130 and RULE-CAT-003 (RULE-CAT-003 named on reactivation), RULE-CAT-029 (a GTIN repeated in one import file), AC-CAT-096 (scans the code of the precondition's SCALE barcode), AC-CAT-145 (the read of an inactive Variant returns no active barcode) |
| Revisions (seventh gate-analysis revise) | ADDED: REQ-CAT-140 and REQ-CAT-141 (split from REQ-CAT-025: one REQ per default-Variant move), REQ-CAT-142, REQ-CAT-143 and REQ-CAT-144 (split from REQ-CAT-130: one REQ per reactivation refusal), REQ-CAT-145, REQ-CAT-146 and REQ-CAT-147 (split from REQ-CAT-135: one REQ per repair allowed on an inactive Item) · MODIFIED: REQ-CAT-025, REQ-CAT-130 and REQ-CAT-135 (each keeps one behaviour), AC-CAT-163 and AC-CAT-164 re-pointed to REQ-CAT-140 / 141, AC-CAT-133, AC-CAT-134 and AC-CAT-162 to REQ-CAT-142 / 143 / 144, AC-CAT-154, AC-CAT-155 and AC-CAT-156 to REQ-CAT-145 / 146 / 147 (no AC renumbered), RULE-CAT-003, 019, 026 and 053 traces, A7, REQ-CAT-139 rationale (the 60-second figure is a measured target — ADR-CAT-074) |
| Revisions (eighth gate-analysis revise) | ADDED: none · MODIFIED: RULE-CAT-038 (the file part is enforced at upload by the FILE category CAT_ITEM_IMAGE; CAT refuses the 21st image), REQ-CAT-110 rationale, AC-CAT-112 (the 7 MB JPG refused by the image category, the 21st image by RULE-CAT-038), A9 given-usage (FileDocumentLookupApi lists only its published methods; the tenant row registers the image category) — ADR-CAT-076 |

## A2 — Functional context

### In scope
The catalogue that every channel uses, i.e. what is sold: Items, Variants, Variant attributes and their values, Categories, Bundles (component lines), Item units, Barcodes (GTIN, internal, scale codes), Item media, publish state, tax class and e-invoice data on Items and Variants, the e-invoice unit of each unit of measure, scale label layouts, Excel import of Items, barcode label printing, the catalogue search used by the global search, and catalogue-change events.

### Out of scope
- Prices, promotions and price labels (PRC). CAT holds no price [DP §7.2].
- Stock quantities, lots and dual-quantity movements (INV). CAT holds no stock quantity [DP §7.2].
- Serial numbers and warranty (SWR).
- Owning the capability flags, the activity presets and the setup wizard (owner OPEN, P-02 — ADR-CAT-041).
- Maintaining lookup values: brands and units of measure are kept in the core lookup module (`erp-core:modules.mdl`). CAT uses them and does not rebuild them [DP §5 G6].
- Importing Variants, Bundles or media from Excel. The v2 import takes one Item, with its default Variant, per row.
- Pre-assembled kits, activity-specific catalogue models and multi-vendor catalogues (business-policies SCOPE EXCEPTIONS, per the PRD DEFERRED note).

### Module function
CAT is the single catalogue of a tenant. The owner defines an Item once, and the back-office, the point of sale and the storefront all read that same record. CAT is first in the fixed module order and depends on no business module; downstream modules read it and react to its catalogue-change events [KB:raw-idea.md §5]. What the catalogue does depends only on the capability flags `variants` and `bundles` [KB:raw-idea.md §3 D2, §4.1].

### Detailed description
- **Owner** creates Items with only the Arabic name to start; the other mandatory entries are pre-filled. Under "Advanced" the owner adds the English name, Category, brand, descriptions and e-invoice data. When the `variants` flag is on, the owner describes an Item by up to three Variant attributes, and the system creates one Variant per combination. When the `bundles` flag is on, the owner builds Bundles from existing Variants. The owner also adds units (for example a carton of 12 pieces) and barcodes, uploads images, publishes Items to the storefront, imports an existing item list from Excel at the setup wizard's item-import step, and maintains the catalogue settings (scale label layouts and the e-invoice unit of each unit).
- **Seller/cashier** finds Items through the global search or by scanning a barcode or a scale label, and reads the same Item the back-office maintains.
- **Warehouse staff** read Items, look Variants up by barcode, and print barcode labels.
- **Storefront shopper** (customer role) sees only active, published Items through the storefront module, which reads the catalogue.

### Current situation
| Step | Party | Notes |
|---|---|---|
| Item kept in a spreadsheet or a separate POS list | Owner | Same item typed again in each tool [KB:raw-idea.md §1] |
| Online offer kept in a separate e-commerce back-end | Owner | Changes reach each channel by hand |
| Barcodes and weighed goods keyed at the counter | Seller/cashier | No shared barcode list |

### Current difficulties
The same item has to be maintained in three places. Names and codes drift between channels. Setup is slow, because tools demand many mandatory fields. Tools also force functions (variants, kits) on businesses that do not use them [KB:raw-idea.md §2, §6].

### Proposed system and benefits
- One catalogue record per Item, read by every channel (US-CAT-001, US-CAT-002).
- Behaviour driven by flags, with only the enabled functions shown (US-CAT-003, US-CAT-004).
- At most four mandatory entries per screen, with the Item's code assigned automatically (US-CAT-007, US-CAT-009) [KB:raw-idea.md §6.1].
- Excel import inside the setup wizard (US-CAT-010) [KB:raw-idea.md §6.9].

### General notes
- Tenancy is handled by the given core; CAT is tenant-agnostic [DP §5 G4].
- Every error message offers a next action [KB:raw-idea.md §6.5].
- Every identifier is a BIGINT identity the database assigns.
- Lookup values are loaded at runtime from `mdl`, and enumerations are never hard-coded.
- Document and business numbers come from the core sequence module.
- Change history is provided by the given `audit` and has no CAT requirement (PRD P-5).

## A3 — Entities and fields

**Standard fields per kind (profile `conventions.entity_defaults`), listed once and carried by every entity of the kind:**
- master: nameAr, nameEn, code, isActiveFl, createdBy, createdAt, updatedBy, updatedAt
- lookup: code, nameAr, nameEn, sortOrder, isActiveFl
- config: key, valueAr, valueEn, isActiveFl
- Audit fields (createdBy, createdAt, updatedBy, updatedAt) are filled by the system and never accepted from a caller.
- Dependent records (ENT-CAT-005, 007, 008, 009, 010) take their parent's kind, under ADR-CAT-039. They carry isActiveFl and the audit fields. Their code and names are either the parent's or are named in their own table.

Primary key naming follows `{entity}Pk`. Logical types are: text, number, decimal, flag, date-time, lookup, reference, identifier (BIGINT identity).

### ENT-CAT-001 — Item / صنف
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master — the catalogue record that every Variant belongs to | SHARED (owner) | yes — `code`, by test (b): US-CAT-009 asks for it, and it appears on labels and receipts | create, read, search, update, deactivate, reactivate, publish, unpublish, import | read by PRC, INV, PUR, SLS, SFR, INS (they declare it in their own SRS) | DP §7.1; registry CAND-CAT-001; US-CAT-001 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| itemPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| code | text (≤ 20) | yes | core number series CAT_ITEM (D-11) | assigned on first save and read-only after (RULE-CAT-004) | الرمز | Code |
| nameAr | text (≤ 200) | yes | entered | RULE-CAT-001, RULE-CAT-003 | الاسم بالعربية | Arabic name |
| nameEn | text (≤ 200) | no | entered | RULE-CAT-002; nameAr is returned when empty (REQ-CAT-029) | الاسم بالإنجليزية | English name |
| itemKindCode | lookup | yes | CAT_ITEM_KIND, default STANDARD | read-only after first save (RULE-CAT-022) | نوع الصنف | Item kind |
| baseUomCode | lookup | yes | CAT_UOM, default PIECE (D-09) | read-only after first save (RULE-CAT-007) | وحدة القياس الأساسية | Base unit of measure |
| categoryFk | reference | no | ENT-CAT-006 | RULE-CAT-026 | الفئة | Category |
| brandCode | lookup | no | CAT_BRAND | RULE-CAT-006 | العلامة التجارية | Brand |
| taxClassCode | lookup | yes | CAT_TAX_CLASS, default STANDARD (ADR-CAT-040) | RULE-CAT-006 | التصنيف الضريبي | Tax class |
| einvoiceCodeTypeCode | lookup | no | CAT_EINVOICE_ITEM_CODE_TYPE | RULE-CAT-042 | نوع رمز الفاتورة الإلكترونية | E-invoice code type |
| einvoiceCode | text (≤ 100) | no | entered | RULE-CAT-042 | رمز الفاتورة الإلكترونية | E-invoice code |
| descriptionAr | text (≤ 2000) | no | entered | — | الوصف بالعربية | Arabic description |
| descriptionEn | text (≤ 2000) | no | entered | — | الوصف بالإنجليزية | English description |
| publishStateCode | lookup | yes | CAT_PUBLISH_STATE, default UNPUBLISHED | RULE-CAT-041 | حالة النشر | Publish state |
| isActiveFl | flag | yes | default true | RULE-CAT-048 | نشط | Active |

### ENT-CAT-002 — Variant / متغيّر الصنف
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master — a concrete version of an Item that can be sold and stocked; every Item has at least one (its default Variant) | SHARED (owner) | yes — `code`, by test (a): it is printed on labels and read by the point of sale | create (default, generate), read, search, update (e-invoice override), deactivate | read by PRC, INV, PUR, SLS, SFR (stock, price and barcode attach to the Variant) | DP §8 P-04; registry CAND-CAT-002; US-CAT-011 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| variantPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| itemFk | reference | yes | ENT-CAT-001 | fixed after creation | الصنف | Item |
| code | text (≤ 20) | yes | core number series CAT_VARIANT (D-11) | read-only (RULE-CAT-004) | الرمز | Code |
| nameAr | text (≤ 400) | yes | derived at read: Item nameAr, then the value nameAr of each assignment in attribute sortOrder, joined by " / " (D-15) | not entered | الاسم بالعربية | Arabic name |
| nameEn | text (≤ 400) | no | derived the same way from the nameEn values, with nameAr used where nameEn is empty | not entered | الاسم بالإنجليزية | English name |
| isDefaultFl | flag | yes | true for exactly one Variant of the Item: the Variant created with the Item, until the default moves (REQ-CAT-140, REQ-CAT-141, ADR-CAT-072) | — | المتغيّر الأساسي | Default variant |
| einvoiceCodeTypeCode | lookup | no | CAT_EINVOICE_ITEM_CODE_TYPE | override (RULE-CAT-042) | نوع رمز الفاتورة الإلكترونية | E-invoice code type |
| einvoiceCode | text (≤ 100) | no | entered | override (RULE-CAT-042) | رمز الفاتورة الإلكترونية | E-invoice code |
| isActiveFl | flag | yes | default true | RULE-CAT-015, RULE-CAT-021, RULE-CAT-048 | نشط | Active |

### ENT-CAT-003 — Variant attribute / خاصية المتغيّر
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| lookup — a dimension that Variants differ by (size, colour). The tenant defines the attributes themselves at runtime, each with its own nested value list that Variant assignments reference by foreign key, so it is an entity rather than an mdl key (ADR-CAT-070) | PRIVATE | no — code entered by the owner | create, read, list, update, deactivate, reactivate | none (consumers read the names through Variant reads) | registry CAND-CAT-003; DP §8 P-03 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| variantAttributePk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| code | text (≤ 30) | yes | entered | RULE-CAT-016 | الرمز | Code |
| nameAr | text (≤ 200) | yes | entered | RULE-CAT-001, RULE-CAT-016 | الاسم بالعربية | Arabic name |
| nameEn | text (≤ 200) | no | entered | RULE-CAT-002 | الاسم بالإنجليزية | English name |
| sortOrder | number | yes | default: next number in the list | — | الترتيب | Sort order |
| isActiveFl | flag | yes | default true | RULE-CAT-017 | نشط | Active |

### ENT-CAT-004 — Variant attribute value / قيمة خاصية المتغيّر
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| lookup — one value of a Variant attribute (for example M, أحمر); held in CAT with its attribute (ADR-CAT-070) | PRIVATE | no | create, read, list, update, deactivate, reactivate | none | US-CAT-011; DP §8 P-03 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| variantAttributeValuePk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| variantAttributeFk | reference | yes | ENT-CAT-003 | fixed after creation | الخاصية | Attribute |
| code | text (≤ 30) | yes | entered | RULE-CAT-016 | الرمز | Code |
| nameAr | text (≤ 200) | yes | entered | RULE-CAT-001, RULE-CAT-016 | الاسم بالعربية | Arabic name |
| nameEn | text (≤ 200) | no | entered | RULE-CAT-002 | الاسم بالإنجليزية | English name |
| sortOrder | number | yes | default: next number within the attribute | — | الترتيب | Sort order |
| isActiveFl | flag | yes | default true | RULE-CAT-017 | نشط | Active |

### ENT-CAT-005 — Variant attribute assignment / إسناد قيمة خاصية للمتغيّر
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master (dependent of ENT-CAT-002, ADR-CAT-039) — the value a Variant carries for one attribute | PRIVATE | no | create (through generation), read, deactivate (with its Variant) | none | US-CAT-011 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| variantAttributeAssignmentPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| variantFk | reference | yes | ENT-CAT-002 | — | المتغيّر | Variant |
| variantAttributeFk | reference | yes | ENT-CAT-003 | one per Variant (RULE-CAT-013) | الخاصية | Attribute |
| variantAttributeValueFk | reference | yes | ENT-CAT-004 of the same attribute | RULE-CAT-013 | القيمة | Value |
| isActiveFl | flag | yes | follows the Variant | RULE-CAT-010 | نشط | Active |

### ENT-CAT-006 — Category / فئة
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master — a node in the tenant's item classification tree | SHARED (owner) | no — code entered by the owner | create, read, list (tree), update, deactivate, reactivate | read by SFR (browse) and INS (reporting) | DP §8 P-05; registry CAND-CAT-004; US-CAT-013 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| categoryPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| code | text (≤ 30) | yes | entered | RULE-CAT-024 | الرمز | Code |
| nameAr | text (≤ 200) | yes | entered | RULE-CAT-001, RULE-CAT-024 | الاسم بالعربية | Arabic name |
| nameEn | text (≤ 200) | no | entered | RULE-CAT-002 | الاسم بالإنجليزية | English name |
| parentCategoryFk | reference | no | ENT-CAT-006 | empty means top level; RULE-CAT-023 | الفئة الأم | Parent category |
| sortOrder | number | yes | default: next number under the parent | — | الترتيب | Sort order |
| isActiveFl | flag | yes | default true | RULE-CAT-025, RULE-CAT-048 | نشط | Active |

### ENT-CAT-007 — Bundle component / مكوّن الحزمة
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master (dependent of a BUNDLE Item, ADR-CAT-039) — one component Variant of a Bundle and its quantity | SHARED (owner) | no | create, read, update (quantity), deactivate (remove line) | read by INV and SLS to resolve a Bundle into its components (DP §8 P-06) | US-CAT-012; registry CAND-CAT-005 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| bundleComponentPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| bundleItemFk | reference | yes | ENT-CAT-001 of kind BUNDLE | — | الحزمة | Bundle |
| componentVariantFk | reference | yes | ENT-CAT-002 | RULE-CAT-019, RULE-CAT-021 | المتغيّر المكوِّن | Component variant |
| quantity | decimal (3 decimals) | yes | entered, default 1 | RULE-CAT-018 | الكمية | Quantity |
| sortOrder | number | yes | default: line order | — | الترتيب | Sort order |
| isActiveFl | flag | yes | default true | RULE-CAT-048 | نشط | Active |

### ENT-CAT-008 — Item unit / وحدة الصنف
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master (dependent of ENT-CAT-001, ADR-CAT-039) — an extra unit an Item is bought or sold in, with its factor to the base unit | SHARED (owner) | no | create, read, update (purchase/sales flags), deactivate | read by INV, PUR, SLS for quantity conversion | US-CAT-015 (registry AUTO "Item unit conversion owned by CAT") |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| itemUnitPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| itemFk | reference | yes | ENT-CAT-001 | — | الصنف | Item |
| uomCode | lookup | yes | CAT_UOM | RULE-CAT-027 | وحدة القياس | Unit of measure |
| conversionFactor | decimal (6 decimals) | yes | entered: base units in one of this unit (carton = 12) | read-only after saving (RULE-CAT-028) | معامل التحويل | Conversion factor |
| isPurchaseFl | flag | yes | default true | — | للشراء | Used for purchasing |
| isSalesFl | flag | yes | default true | — | للبيع | Used for selling |
| isActiveFl | flag | yes | default true | RULE-CAT-048 | نشط | Active |

### ENT-CAT-009 — Barcode / باركود
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master (dependent of ENT-CAT-002, ADR-CAT-039) — a scannable code identifying a Variant in one unit | SHARED (owner) | no (an internal barcode takes its digits from the core number series, D-05) | create (GTIN, internal, scale), read, lookup, deactivate | read by SLS (scanning at the point of sale) and PUR (receiving) | DP §8 P-07; registry CAND-CAT-007; US-CAT-016, US-CAT-017 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| barcodePk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| variantFk | reference | yes | ENT-CAT-002 | — | المتغيّر | Variant |
| barcodeValue | text (≤ 48) | yes | entered (GTIN, SCALE) or generated (INTERNAL) | serves as this record's code; read-only after saving; RULE-CAT-029 … RULE-CAT-033 | الباركود | Barcode |
| barcodeTypeCode | lookup | yes | CAT_BARCODE_TYPE | — | نوع الباركود | Barcode type |
| uomCode | lookup | yes | CAT_UOM, default the Item's baseUomCode | RULE-CAT-031 | وحدة القياس | Unit of measure |
| isActiveFl | flag | yes | default true | RULE-CAT-048 | نشط | Active |

### ENT-CAT-010 — Item media / وسائط الصنف
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master (dependent of ENT-CAT-001, ADR-CAT-039) — one image of an Item (optionally bound to a Variant). The file itself is kept by the given `file` module | PRIVATE | no | create (upload), read, update, deactivate (remove) | none (SFR reads images through Item and Variant reads) | DP §8 P-12; registry CAND-CAT-006; US-CAT-019 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| itemMediaPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| itemFk | reference | yes | ENT-CAT-001 | — | الصنف | Item |
| variantFk | reference | no | ENT-CAT-002 of the same Item | RULE-CAT-040 | المتغيّر | Variant |
| fileDocumentRef | reference | yes | given FileDocument (`erp-core:modules.file`, public image storage) — GIVEN, no XM edge | RULE-CAT-038 | ملف الصورة | Image file |
| nameAr | text (≤ 200) | yes | image description, default the Item's nameAr | RULE-CAT-001 | وصف الصورة بالعربية | Arabic image description |
| nameEn | text (≤ 200) | no | image description | RULE-CAT-002 | وصف الصورة بالإنجليزية | English image description |
| sortOrder | number | yes | default: next number for the Item | — | الترتيب | Sort order |
| isPrimaryFl | flag | yes | true for exactly one active image (RULE-CAT-039) | — | الصورة الرئيسية | Main image |
| isActiveFl | flag | yes | default true | RULE-CAT-038 | نشط | Active |

### ENT-CAT-011 — Scale label layout / إعداد ملصق الميزان
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| config — the tenant's layout for the 13-digit labels its scale prints (ADR-CAT-042) | PRIVATE | no | create, read, update, deactivate | none | US-CAT-017; module-registry OI-10 (tenant setting, GS1 restricted-circulation prefixes) |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| scaleLabelLayoutPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| key | text (2 digits) | yes | entered: the label prefix (02 or 20–29) | fixed after creation; RULE-CAT-034 | البادئة | Prefix |
| valueAr | text (≤ 200) | no | layout description, default «ملصق ميزان {key}» | — | الوصف بالعربية | Arabic description |
| valueEn | text (≤ 200) | no | layout description, default "Scale label {key}" | — | الوصف بالإنجليزية | English description |
| scaleCodeLength | number (4–6) | yes | default 5 | fixed after creation, because the SCALE barcodes of the layout were validated against it (ADR-CAT-064); the embedded value takes 10 − scaleCodeLength digits | طول رمز الميزان | Scale code length |
| embeddedValueTypeCode | lookup | yes | CAT_SCALE_VALUE_TYPE, default WEIGHT | — | نوع القيمة المضمّنة | Embedded value type |
| valueDecimals | number (0–3) | yes | default 3 | — | الأرقام العشرية | Decimals |
| isActiveFl | flag | yes | default true | — | نشط | Active |

### ENT-CAT-012 — Unit e-invoice code / رمز الوحدة في الفاتورة الإلكترونية
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| config — the e-invoice unit that one unit of measure carries (ADR-CAT-042) | PRIVATE | no | create, read, update, deactivate | read through Variant and Item unit reads by SLS and TAX | US-CAT-021 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| unitEinvoiceCodePk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| key | lookup | yes | CAT_UOM value (the unit of measure) | RULE-CAT-043 | وحدة القياس | Unit of measure |
| valueAr | text (≤ 200) | no | derived from the unit's nameAr | not entered | الاسم بالعربية | Arabic name |
| valueEn | text (≤ 200) | no | derived from the unit's nameEn | not entered | الاسم بالإنجليزية | English name |
| einvoiceUnitCode | lookup | yes | CAT_EINVOICE_UNIT; seeded per D-12 | RULE-CAT-043 | وحدة الفاتورة الإلكترونية | E-invoice unit |
| isActiveFl | flag | yes | default true | — | نشط | Active |

## A4 — Functional requirements (EARS) and acceptance criteria

### REQ-CAT-001 — One catalogue record for every channel
Pattern    : ubiquitous
Statement  : The system shall serve every Item and its Variants to the back-office, the point of sale and the storefront from one catalogue record per Item.
Traces     : US-CAT-001
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : The owner never maintains the same Item in three places.
Source     : US-CAT-001; POL-CAT-001; KB:raw-idea.md §1
Priority   : HIGH

#### AC-CAT-001 — [REQ-CAT-001]
Given : an Item with code ITM-000001 created from the back-office
When  : the point-of-sale read and the storefront read request that Item by its itemPk
Then  : both reads return the same itemPk, code ITM-000001 and nameAr as the back-office read, and the tenant holds exactly 1 Item record with that code

### REQ-CAT-002 — Read an Item
Pattern    : event
Statement  : When a caller requests an Item by its identifier, the system shall return the Item with its active Variants, Variant attribute assignments, Item units, barcodes, Bundle component lines and active media.
Traces     : US-CAT-001
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010
Rationale  : Every channel reads the whole catalogue entry in one request.
Source     : US-CAT-001
Priority   : HIGH

#### AC-CAT-002 — [REQ-CAT-002]
Given : an Item with 2 active Variants, 1 inactive Variant, 1 active Item unit and 3 active barcodes
When  : a caller requests the Item by its itemPk
Then  : the response holds the Item, exactly the 2 active Variants, the 1 Item unit and the 3 barcodes

### REQ-CAT-003 — Search Items
Pattern    : event
Statement  : When a user searches Items with any of the filters text, Category, brand, kind, publish state and active state, the system shall return a page of the matching Items sorted by nameAr.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : Back-office list of the single catalogue.
Source     : US-CAT-001
Priority   : HIGH

#### AC-CAT-003 — [REQ-CAT-003]
Given : 30 Items, of which 12 belong to the Category «هواتف»
When  : a user searches with that Category as filter and a page size of 10
Then  : page 1 holds 10 Items, the total count is 12, and the Items are sorted by nameAr ascending

### REQ-CAT-004 — Update an Item
Pattern    : event
Statement  : When an owner submits changes to an active Item, the system shall save the changed nameAr, nameEn, categoryFk, brandCode, taxClassCode, descriptionAr and descriptionEn values.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : The Item is changed once, in one place.
Source     : US-CAT-001
Priority   : HIGH

#### AC-CAT-004 — [REQ-CAT-004]
Given : an active Item with an empty brandCode
When  : the owner sets brandCode to an active CAT_BRAND value and saves
Then  : the next read returns that brandCode, and updatedBy holds the owner's user identifier

### REQ-CAT-005 — Deactivate an Item
Pattern    : event
Statement  : When an owner deactivates an Item, the system shall set its isActiveFl to false and its publishStateCode to UNPUBLISHED, and leave its Variants and their barcodes active.
Traces     : US-CAT-001
Entities   : ENT-CAT-001, ENT-CAT-009
Rationale  : An Item that is no longer sold leaves every channel but stays readable by earlier documents. Its barcodes stay active so that the Item can be reactivated with them: while the Item is inactive they no longer resolve (REQ-CAT-086) but still hold their values under RULE-CAT-029. The owner releases a GTIN by deactivating the barcode (REQ-CAT-085, API-CAT-016), which stays allowed on an inactive Item (RULE-CAT-053 never refuses a child deactivation). Deactivating a Variant, by contrast, deactivates its barcodes (REQ-CAT-050).
Source     : US-CAT-001; RULE-CAT-041; RULE-CAT-048; ADR-CAT-063
Priority   : HIGH

#### AC-CAT-005 — [REQ-CAT-005]
Given : an active Item with publishStateCode PUBLISHED
When  : the owner deactivates it
Then  : isActiveFl is false, publishStateCode is UNPUBLISHED, a read by itemPk still returns the Item, and the response shows ar: «تم الإيقاف. يبقى السجل متاحًا للمستندات السابقة ولا يظهر في الاختيارات الجديدة.» · en: "Deactivated. The record stays available to earlier documents and no longer appears in new selections."

### REQ-CAT-006 — Reactivate an Item
Pattern    : event
Statement  : When an owner reactivates an inactive Item that meets the ACTIVE-state invariants of A7, the system shall set its isActiveFl to true and keep its publishStateCode UNPUBLISHED.
Traces     : US-CAT-001
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-006, ENT-CAT-007
Rationale  : An Item comes back without reappearing on the storefront by accident, and in a state an active Item may hold.
Source     : US-CAT-001; ADR-CAT-059
Priority   : HIGH

#### AC-CAT-006 — [REQ-CAT-006]
Given : an inactive STANDARD Item with 1 active Variant, no Category, and no other active Item with its nameAr
When  : the owner reactivates it
Then  : isActiveFl is true, publishStateCode is UNPUBLISHED, and the Item read returns exactly 1 active Variant

### REQ-CAT-007 — Same Item entered twice
Pattern    : unwanted
Statement  : If a create or update request gives an Item the nameAr of another active Item of the tenant, then the system shall reject the request with RULE-CAT-003.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : Prevents one Item from being kept as two records.
Source     : US-CAT-001; D-13
Priority   : HIGH

#### AC-CAT-007 — [REQ-CAT-007]
Given : an active Item named «شاحن سامسونج 25W» with code ITM-000012
When  : the owner creates another Item named «شاحن سامسونج 25W»
Then  : no Item is created and the response shows ar: «يوجد صنف نشط بالاسم نفسه: ITM-000012. افتح الصنف الموجود أو غيّر الاسم.» · en: "An active item already has this name: ITM-000012. Open the existing item or change the name.", naming the existing Item's code as the next action

#### AC-CAT-149 — [REQ-CAT-007]
Given : no active Item named «كوب زجاج»
When  : two requests that each create an Item named «كوب زجاج» are submitted in parallel
Then  : exactly 1 Item named «كوب زجاج» is created, and the other request is refused with code CAT-409-ITEM-NAME-DUPLICATE

### REQ-CAT-008 — Publish catalogue-change events
Pattern    : event
Statement  : When a change to an Item, Variant, Variant attribute assignment, Bundle component, Item unit, barcode or Item media is committed, the system shall publish one catalogue-change event carrying the record identifier, the itemPk and the change kind.
Traces     : US-CAT-002
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010
Rationale  : Downstream modules react to catalogue changes without CAT reading them.
Source     : US-CAT-002; module-registry AUTO "CAT publishes catalogue-change events"
Priority   : HIGH

#### AC-CAT-008 — [REQ-CAT-008]
Given : an active Item
When  : the owner changes its nameEn and the change is committed
Then  : exactly 1 catalogue-change event is published, carrying the itemPk and change kind UPDATED

### REQ-CAT-009 — No event for a change that was not saved
Pattern    : unwanted
Statement  : If the transaction holding a catalogue change is rolled back, then the system shall publish no catalogue-change event for that change.
Traces     : US-CAT-002
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Channels never act on a change that does not exist.
Source     : US-CAT-002; RULE-CAT-049
Priority   : HIGH

#### AC-CAT-009 — [REQ-CAT-009]
Given : an Item update whose commit fails
When  : the transaction is rolled back
Then  : 0 catalogue-change events are published and the response shows ar: «تعذّر حفظ التغيير ولم يُرسل أي إشعار بالتغيير. أعد المحاولة.» · en: "The change was not saved and no change notice was sent. Try again."

### REQ-CAT-010 — Committed change seen by every channel
Pattern    : ubiquitous
Statement  : The system shall return a committed catalogue change on every read that starts after the commit, whichever channel reads.
Traces     : US-CAT-002
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Every channel sells the same, current Item.
Source     : US-CAT-002
Priority   : HIGH

#### AC-CAT-010 — [REQ-CAT-010]
Given : the owner changed an Item's nameEn from "Charger" to "Charger 25W" and the change is committed
When  : the point-of-sale read and the storefront read request the Item after the commit
Then  : both reads return nameEn "Charger 25W"

### REQ-CAT-011 — Behaviour follows capability flags only
Pattern    : ubiquitous
Statement  : The system shall decide every catalogue behaviour that differs between trading activities from the capability flags `variants` and `bundles`, read through the FeatureFlags API, and never from the tenant's activity or activity preset.
Traces     : US-CAT-003
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : One codebase serves every activity (G1).
Source     : US-CAT-003; POL-CAT-002; KB:raw-idea.md §3 D2; ADR-CAT-041
Priority   : HIGH

#### AC-CAT-011 — [REQ-CAT-011]
Given : two tenants with different activities and the same values of `variants` and `bundles`
When  : each tenant creates an Item with the same entries
Then  : both responses are equal in every field except itemPk, variantPk, code and the audit fields

### REQ-CAT-012 — Flag change applied at once
Pattern    : event
Statement  : When the value of the `variants` or `bundles` capability flag changes, the system shall apply the new value from the next catalogue request onward without a restart.
Traces     : US-CAT-003
Entities   : ENT-CAT-003, ENT-CAT-007
Rationale  : The owner adjusts flags at any time.
Source     : US-CAT-003; KB:raw-idea.md §6.2
Priority   : HIGH

#### AC-CAT-012 — [REQ-CAT-012]
Given : the `variants` flag is off
When  : the owner turns it on and then creates a Variant attribute in the next request
Then  : the Variant attribute is created and returned with isActiveFl true

### REQ-CAT-013 — Flag value unreadable
Pattern    : unwanted
Statement  : If the value of a capability flag cannot be read, then the system shall refuse the operation that depends on that flag with RULE-CAT-009.
Traces     : US-CAT-003
Entities   : ENT-CAT-001, ENT-CAT-005, ENT-CAT-007
Rationale  : A flag-bound operation never runs on an unknown flag value.
Source     : US-CAT-003; ADR-CAT-041
Priority   : HIGH

#### AC-CAT-013 — [REQ-CAT-013]
Given : the settings service does not answer the `bundles` flag read
When  : the owner saves an Item of kind BUNDLE
Then  : no record is saved and the response shows ar: «تعذّر التحقق من إعدادات القدرات الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.» · en: "Capability settings could not be checked, and nothing was saved. Try again in a moment."

### REQ-CAT-014 — Variant functions hidden while off
Pattern    : state
Statement  : While the `variants` capability flag is off, the system shall exclude the Variant attribute functions, the Variant attribute fields and the Variant list from every catalogue screen, menu and search filter.
Traces     : US-CAT-004
Entities   : ENT-CAT-002, ENT-CAT-003, ENT-CAT-004, ENT-CAT-005
Rationale  : No dead menus (G2).
Source     : US-CAT-004; POL-CAT-003; KB:raw-idea.md §4.1, §6.3
Priority   : HIGH

#### AC-CAT-014 — [REQ-CAT-014]
Given : the `variants` flag is off
When  : a seller/cashier opens the Catalog menu and the Items screen
Then  : the menu holds no "Variant attributes" entry, and the Items screen shows 0 Variant attribute fields and 0 Variant list sections

### REQ-CAT-015 — Bundle functions hidden while off
Pattern    : state
Statement  : While the `bundles` capability flag is off, the system shall exclude the BUNDLE kind, the Bundle component lines and the bundle filter from every catalogue screen, menu and search filter.
Traces     : US-CAT-004
Entities   : ENT-CAT-001, ENT-CAT-007
Rationale  : No dead menus (G2).
Source     : US-CAT-004; POL-CAT-003
Priority   : HIGH

#### AC-CAT-015 — [REQ-CAT-015]
Given : the `bundles` flag is off
When  : the owner opens the Item entry and the Item search
Then  : the kind field and the kind filter are absent, and 0 Bundle component sections are shown

### REQ-CAT-016 — Flag-off operation refused
Pattern    : unwanted
Statement  : If a request creates an Item of kind BUNDLE, generates Variants, saves Bundle components, or creates or updates a Variant attribute or value while the capability flag that enables it is off, then the system shall reject the request with RULE-CAT-008.
Traces     : US-CAT-004
Entities   : ENT-CAT-001, ENT-CAT-003, ENT-CAT-004, ENT-CAT-005, ENT-CAT-007
Rationale  : Hidden functions also stay closed to direct calls. Reading, deactivating and reactivating records that already exist stay allowed while the flag is off, so data that depends on a flag can always be cleaned up (POL-CAT-005 is not yet enforced by the flag writer — platform finding 2).
Source     : US-CAT-004; POL-CAT-003
Priority   : HIGH

#### AC-CAT-016 — [REQ-CAT-016]
Given : the `variants` flag is off
When  : a request creates a Variant attribute
Then  : nothing is created and the response shows ar: «هذه الوظيفة غير مفعّلة لنشاطك. فعّل مفتاح القدرة «variants» من الإعدادات لاستخدامها.» · en: "This function is not switched on for your business. Turn on the «variants» capability flag in settings to use it."

#### AC-CAT-017 — [REQ-CAT-016]
Given : the `bundles` flag is off
When  : a request creates an Item of kind BUNDLE
Then  : nothing is created and the response shows ar: «هذه الوظيفة غير مفعّلة لنشاطك. فعّل مفتاح القدرة «bundles» من الإعدادات لاستخدامها.» · en: "This function is not switched on for your business. Turn on the «bundles» capability flag in settings to use it."

#### AC-CAT-143 — [REQ-CAT-016]
Given : the `bundles` flag is off and an active Bundle created while it was on
When  : the owner reads the Bundle, then deactivates it
Then  : the read returns the Bundle with its component lines, the deactivation sets isActiveFl to false, and neither request answers code CAT-422-CAPABILITY-OFF

### REQ-CAT-017 — Turning variants on keeps existing Items
Pattern    : event
Statement  : When the `variants` capability flag is turned on, the system shall keep every existing Item with its default Variant, codes, barcodes and identifiers unchanged.
Traces     : US-CAT-005
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Rationale  : No re-setup and no data move (G3, G8).
Source     : US-CAT-005; POL-CAT-004; KB:raw-idea.md §6.2, §6.8
Priority   : HIGH

#### AC-CAT-018 — [REQ-CAT-017]
Given : 200 Items, each with only its default Variant, while `variants` is off
When  : the owner turns `variants` on
Then  : the 200 Items return the same itemPk, variantPk, codes and barcodes as before, and 0 of these records changes its updatedAt

### REQ-CAT-018 — Existing Variants take the first values
Pattern    : event
Statement  : When Variants are generated for an Item that already has Variants, the system shall assign the first selected value of each newly used attribute to the existing Variants before it creates Variants for the remaining combinations.
Traces     : US-CAT-005
Entities   : ENT-CAT-002, ENT-CAT-005
Rationale  : Items already in use grow variants without losing their identity.
Source     : US-CAT-005; RULE-CAT-012
Priority   : HIGH

#### AC-CAT-019 — [REQ-CAT-018]
Given : an Item whose only Variant is its default Variant VAR-000010
When  : the owner selects the values S and M of the attribute «المقاس»
Then  : VAR-000010 carries the value S, 1 new Variant carries the value M, and the Item has 2 active Variants

### REQ-CAT-019 — Barcodes stay on the default Variant
Pattern    : unwanted
Statement  : If Variants are generated for an Item whose default Variant carries barcodes, then the system shall keep those barcodes on the default Variant and create the new Variants with no barcode.
Traces     : US-CAT-005
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : Goods already labelled keep scanning to the same Variant.
Source     : US-CAT-005; RULE-CAT-012
Priority   : HIGH

#### AC-CAT-020 — [REQ-CAT-019]
Given : a default Variant carrying the GTIN 6223000000014
When  : the owner generates 3 Variants for its Item
Then  : the GTIN 6223000000014 still belongs to the default Variant, the 2 new Variants carry 0 barcodes, and the response shows ar: «احتفظت المتغيّرات الحالية بأرقامها وباركوداتها وأُسندت إليها القيم الأولى المختارة.» · en: "Existing variants kept their codes and barcodes and took the first selected values."

### REQ-CAT-020 — Turning bundles on uses existing Variants
Pattern    : event
Statement  : When the `bundles` capability flag is turned on, the system shall accept every active Variant of an existing STANDARD Item as a Bundle component without changing that Variant.
Traces     : US-CAT-005
Entities   : ENT-CAT-002, ENT-CAT-007
Rationale  : Bundles are built from Items already sold.
Source     : US-CAT-005; POL-CAT-004
Priority   : HIGH

#### AC-CAT-021 — [REQ-CAT-020]
Given : an active STANDARD Item created while `bundles` was off
When  : the owner turns `bundles` on and adds its Variant to a new Bundle
Then  : the Bundle is saved with 1 component line, and the component Variant's updatedAt is unchanged

### REQ-CAT-021 — Turn-off check
Pattern    : event
Statement  : When a turn-off check is requested for the `variants` or `bundles` capability flag, the system shall return whether the turn-off is allowed, the count of dependent Items and at most 20 dependent Items by code and nameAr.
Traces     : US-CAT-006
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Rationale  : The flag owner and the owner learn what blocks a turn-off and what to do next.
Source     : US-CAT-006; POL-CAT-005; ADR-CAT-041
Priority   : HIGH

#### AC-CAT-022 — [REQ-CAT-021]
Given : no active Item uses Variant attributes
When  : a turn-off check for `variants` is requested
Then  : the response returns allowed = true, a dependent count of 0 and an empty list

### REQ-CAT-022 — Variants turn-off blocked
Pattern    : unwanted
Statement  : If an active Variant attribute assignment exists on an active Variant of an active Item, then the system shall answer a turn-off check of the `variants` flag as not allowed with RULE-CAT-010.
Traces     : US-CAT-006
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Rationale  : Data that depends on a flag blocks its turn-off (G3).
Source     : US-CAT-006; POL-CAT-005
Priority   : HIGH

#### AC-CAT-023 — [REQ-CAT-022]
Given : 3 active Items whose active Variants carry attribute assignments
When  : a turn-off check for `variants` is requested
Then  : the response returns allowed = false, a dependent count of 3, the 3 Items by code and nameAr, and ar: «لا يمكن إيقاف المتغيّرات لأن 3 صنفًا يستخدمها. أوقف متغيّرات هذه الأصناف أو أبقِ المتغيّرات مفعّلة.» · en: "Variants cannot be turned off because 3 items use them. Deactivate the variants of these items or keep variants on."

### REQ-CAT-023 — Bundles turn-off blocked
Pattern    : unwanted
Statement  : If an active Item of kind BUNDLE exists, then the system shall answer a turn-off check of the `bundles` flag as not allowed with RULE-CAT-011.
Traces     : US-CAT-006
Entities   : ENT-CAT-001
Rationale  : Data that depends on a flag blocks its turn-off (G3).
Source     : US-CAT-006; POL-CAT-005
Priority   : HIGH

#### AC-CAT-024 — [REQ-CAT-023]
Given : 2 active Items of kind BUNDLE
When  : a turn-off check for `bundles` is requested
Then  : the response returns allowed = false, a dependent count of 2, and ar: «لا يمكن إيقاف الحزم لوجود 2 حزمة نشطة. أوقف هذه الحزم أو أبقِ الحزم مفعّلة.» · en: "Bundles cannot be turned off because 2 active bundles exist. Deactivate these bundles or keep bundles on."

### REQ-CAT-024 — Create an Item from its Arabic name
Pattern    : event
Statement  : When an owner saves a new Item that carries only nameAr, the system shall create the Item with itemKindCode STANDARD, baseUomCode PIECE, taxClassCode STANDARD and publishStateCode UNPUBLISHED.
Traces     : US-CAT-007
Entities   : ENT-CAT-001
Rationale  : Adding Items does not slow down setup (G7).
Source     : US-CAT-007; POL-CAT-006; KB:raw-idea.md §6.1; D-09
Priority   : HIGH

#### AC-CAT-025 — [REQ-CAT-024]
Given : the Item entry with only nameAr «كوب زجاج» filled
When  : the owner saves
Then  : the Item is created with itemKindCode STANDARD, baseUomCode PIECE, taxClassCode STANDARD, publishStateCode UNPUBLISHED and isActiveFl true

### REQ-CAT-025 — Default Variant created with the Item
Pattern    : event
Statement  : When an Item is created, the system shall create one default Variant for it with isDefaultFl true.
Traces     : US-CAT-007
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Stock, price and barcode always have a Variant to attach to (D-01). The moves of the default flag are REQ-CAT-140 and REQ-CAT-141 (split from this REQ at the seventh gate-analysis revise).
Source     : US-CAT-007; DP §8 P-04; ADR-CAT-072
Priority   : HIGH

#### AC-CAT-026 — [REQ-CAT-025]
Given : no Item named «كوب زجاج» exists
When  : the owner creates it
Then  : the Item has exactly 1 Variant, with isDefaultFl true, isActiveFl true and a code from the series CAT_VARIANT

### REQ-CAT-026 — At most four mandatory entries
Pattern    : ubiquitous
Statement  : The system shall require at most four mandatory entries to create an Item, namely nameAr, baseUomCode, taxClassCode and itemKindCode, with every one of them except nameAr pre-filled.
Traces     : US-CAT-007
Entities   : ENT-CAT-001
Rationale  : Simplicity is measured as at most 4 mandatory fields per screen.
Source     : US-CAT-007; KB:raw-idea.md §3 D9, §6.1
Priority   : HIGH

#### AC-CAT-027 — [REQ-CAT-026]
Given : the `bundles` flag is on
When  : the owner opens a new Item entry
Then  : exactly 4 mandatory fields appear outside "Advanced", and 3 of them hold the values PIECE, STANDARD and STANDARD

### REQ-CAT-027 — Arabic name missing
Pattern    : unwanted
Statement  : If a create request for an Item carries no nameAr, then the system shall reject the request with RULE-CAT-001.
Traces     : US-CAT-007
Entities   : ENT-CAT-001
Rationale  : The Arabic name is the one entry the owner must give.
Source     : US-CAT-007; PRD P-3
Priority   : HIGH

#### AC-CAT-028 — [REQ-CAT-027]
Given : the Item entry with an empty nameAr
When  : the owner saves
Then  : no Item is created and the response shows ar: «أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل.» · en: "Enter the Arabic name (up to 200 characters) with at least one Arabic letter."

### REQ-CAT-028 — Brand and model text kept in the Arabic name
Pattern    : ubiquitous
Statement  : The system shall accept a nameAr that mixes Arabic letters with Latin letters, digits and symbols, and store it as written.
Traces     : US-CAT-008
Entities   : ENT-CAT-001
Rationale  : Brand and model text stays as written.
Source     : US-CAT-008; PRD P-3
Priority   : HIGH

#### AC-CAT-029 — [REQ-CAT-028]
Given : the Item entry
When  : the owner saves nameAr «سامسونج Galaxy A55 128GB»
Then  : the read returns nameAr «سامسونج Galaxy A55 128GB», character for character

### REQ-CAT-029 — English name falls back to Arabic
Pattern    : optional
Statement  : Where an Item has no nameEn, the system shall return its nameAr in place of nameEn for every English display.
Traces     : US-CAT-008
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : The English name is optional, and receipts never show an empty name.
Source     : US-CAT-008; PRD P-3
Priority   : HIGH

#### AC-CAT-030 — [REQ-CAT-029]
Given : an Item with nameAr «كوب زجاج» and an empty nameEn
When  : a reader requests the English display name
Then  : the value returned is «كوب زجاج»

### REQ-CAT-030 — Arabic name without an Arabic letter
Pattern    : unwanted
Statement  : If a nameAr has no Arabic letter or exceeds 200 characters, then the system shall reject the request with RULE-CAT-001.
Traces     : US-CAT-008
Entities   : ENT-CAT-001
Rationale  : The Arabic name is a real Arabic name (KSA VAT IR Art. 53).
Source     : US-CAT-008; D-07
Priority   : HIGH

#### AC-CAT-031 — [REQ-CAT-030]
Given : the Item entry
When  : the owner saves nameAr "Galaxy A55"
Then  : no Item is saved and the response shows ar: «أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل.» · en: "Enter the Arabic name (up to 200 characters) with at least one Arabic letter."

### REQ-CAT-031 — English name too long
Pattern    : unwanted
Statement  : If a nameEn exceeds 200 characters, then the system shall reject the request with RULE-CAT-002.
Traces     : US-CAT-008
Entities   : ENT-CAT-001
Rationale  : Bounded names on receipts and invoices.
Source     : US-CAT-008
Priority   : HIGH

#### AC-CAT-032 — [REQ-CAT-031]
Given : the Item entry
When  : the owner saves a nameEn of 201 characters
Then  : no Item is saved and the response shows ar: «الاسم بالإنجليزية لا يتجاوز 200 حرف.» · en: "The English name can have at most 200 characters."

### REQ-CAT-032 — Item code assigned automatically
Pattern    : event
Statement  : When an Item is first saved, the system shall assign it the next code of the core number series CAT_ITEM.
Traces     : US-CAT-009
Entities   : ENT-CAT-001
Rationale  : The code is never an entry the owner thinks up (NUMBERING rule).
Source     : US-CAT-009; module-registry AUTO (sequence); D-11
Priority   : HIGH

#### AC-CAT-033 — [REQ-CAT-032]
Given : the last code issued by series CAT_ITEM is ITM-000041
When  : the owner saves a new Item
Then  : the Item's code is ITM-000042

### REQ-CAT-033 — Variant code assigned automatically
Pattern    : event
Statement  : When a Variant is first saved, the system shall assign it the next code of the core number series CAT_VARIANT.
Traces     : US-CAT-009
Entities   : ENT-CAT-002
Rationale  : Variants carry a printable code without owner entry.
Source     : US-CAT-009; D-11
Priority   : HIGH

#### AC-CAT-034 — [REQ-CAT-033]
Given : the last code issued by series CAT_VARIANT is VAR-000107
When  : a Variant is created
Then  : the Variant's code is VAR-000108

### REQ-CAT-034 — Code supplied or changed
Pattern    : unwanted
Statement  : If a request supplies or changes the code of an Item or a Variant, then the system shall reject the request with RULE-CAT-004.
Traces     : US-CAT-009
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Codes stay unique and system-made.
Source     : US-CAT-009
Priority   : HIGH

#### AC-CAT-035 — [REQ-CAT-034]
Given : an Item with code ITM-000042
When  : an update request sets its code to ITM-999999
Then  : the code stays ITM-000042 and the response shows ar: «يُعيَّن الرمز تلقائيًا ولا يمكن إدخاله أو تعديله.» · en: "The code is assigned automatically and cannot be entered or changed."

### REQ-CAT-035 — Number series unavailable
Pattern    : unwanted
Statement  : If the core number series cannot issue a code for a new Item or Variant, then the system shall reject the save with RULE-CAT-005 and store no record.
Traces     : US-CAT-009
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : No Item is stored without a code.
Source     : US-CAT-009
Priority   : HIGH

#### AC-CAT-036 — [REQ-CAT-035]
Given : series CAT_ITEM returns an error
When  : the owner saves a new Item
Then  : 0 Items and 0 Variants are stored and the response shows ar: «تعذّر إصدار رمز جديد الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.» · en: "A new code could not be issued, and nothing was saved. Try again in a moment."

### REQ-CAT-036 — Download the import sheet
Pattern    : event
Statement  : When an owner requests the item import sheet, the system shall return an xlsx file whose header row names each import column in ar and en.
Traces     : US-CAT-010
Entities   : ENT-CAT-001
Rationale  : The owner fills a known layout instead of guessing columns.
Source     : US-CAT-010; POL-CAT-007; KB:raw-idea.md §6.9
Priority   : HIGH

#### AC-CAT-037 — [REQ-CAT-036]
Given : the setup wizard's item-import step
When  : the owner requests the import sheet
Then  : an xlsx file is returned with 1 sheet whose header row holds 11 columns (nameAr, nameEn, baseUomCode, category code, brandCode, taxClassCode, einvoiceCodeTypeCode, einvoiceCode, GTIN barcode, descriptionAr, descriptionEn), each labelled in ar and en

### REQ-CAT-037 — Validate every imported row
Pattern    : event
Statement  : When an owner submits an xlsx item import file, the system shall validate every data row against RULE-CAT-045 before it creates any Item.
Traces     : US-CAT-010
Entities   : ENT-CAT-001, ENT-CAT-009
Rationale  : Imported Items meet the same rules as Items entered by hand.
Source     : US-CAT-010
Priority   : HIGH

#### AC-CAT-038 — [REQ-CAT-037]
Given : an import file of 50 data rows, of which row 7 has an empty nameAr
When  : the owner submits it
Then  : the result reports row 7 as rejected for column nameAr before any Item is created, and 49 rows as valid

### REQ-CAT-038 — Create Items from valid rows
Pattern    : event
Statement  : When the validation of an item import file ends, the system shall create one Item, its default Variant and, where the row carries a GTIN, its barcode for each valid row.
Traces     : US-CAT-010
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Rationale  : The owner starts selling without re-typing the catalogue.
Source     : US-CAT-010
Priority   : HIGH

#### AC-CAT-039 — [REQ-CAT-038]
Given : an import file of 49 valid rows, 20 of which carry a GTIN
When  : validation ends
Then  : 49 Items, 49 default Variants and 20 GTIN barcodes are created

### REQ-CAT-039 — Import result
Pattern    : event
Statement  : When an item import ends, the system shall return the count of rows read, Items created and rows rejected, with the row number, column and ar/en reason of each rejected row.
Traces     : US-CAT-010
Entities   : ENT-CAT-001
Rationale  : The owner knows exactly what to fix (no dead ends).
Source     : US-CAT-010; KB:raw-idea.md §6.5
Priority   : HIGH

#### AC-CAT-040 — [REQ-CAT-039]
Given : an import of 50 rows of which 1 is rejected
When  : the import ends
Then  : the result shows rows read 50, Items created 49, rows rejected 1, and for row 7 the column nameAr with ar: «الصف 7، العمود nameAr: أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل. صحّح الصف وأعد استيراده.» · en: "Row 7, column nameAr: Enter the Arabic name (up to 200 characters) with at least one Arabic letter. Correct the row and import it again."

### REQ-CAT-040 — Import file not accepted
Pattern    : unwanted
Statement  : If an import file is not xlsx, exceeds 10 MB, holds more than 5,000 data rows or carries a header row that differs from the import sheet, then the system shall reject the whole file with RULE-CAT-044 and create no Item.
Traces     : US-CAT-010
Entities   : ENT-CAT-001
Rationale  : Bounded import inside the setup time target.
Source     : US-CAT-010; D-10
Priority   : HIGH

#### AC-CAT-041 — [REQ-CAT-040]
Given : a .csv file
When  : the owner submits it for import
Then  : 0 Items are created and the response shows ar: «الملف غير مقبول: يجب أن يكون بصيغة xlsx بحجم لا يتجاوز 10 ميجابايت وحتى 5000 صف، وبعناوين ورقة الاستيراد نفسها. نزّل ورقة الاستيراد واستخدمها.» · en: "The file was not accepted: it must be xlsx, at most 10 MB and 5,000 rows, with the import sheet headers. Download the import sheet and use it."

### REQ-CAT-041 — Failed row skipped
Pattern    : unwanted
Statement  : If a data row of an import file fails a rule, then the system shall skip that row, create the Items of the other valid rows and report the row with RULE-CAT-045.
Traces     : US-CAT-010
Entities   : ENT-CAT-001
Rationale  : One bad row never blocks the whole catalogue.
Source     : US-CAT-010
Priority   : HIGH

#### AC-CAT-042 — [REQ-CAT-041]
Given : an import file of 10 rows where row 4 carries the brandCode «XYZ», which is not an active CAT_BRAND value
When  : the owner submits it
Then  : 9 Items are created, row 4 creates nothing, and row 4 is reported for column brandCode

#### AC-CAT-043 — [REQ-CAT-041]
Given : an import file whose rows 2 and 9 both carry nameAr «كوب زجاج», with no such active Item in the tenant
When  : the owner submits it
Then  : row 2 creates the Item, row 9 is rejected for column nameAr, and the reason reads ar: «يوجد صنف نشط بالاسم نفسه: {code}. افتح الصنف الموجود أو غيّر الاسم.» · en: "An active item already has this name: {code}. Open the existing item or change the name.", with {code} the code the import gave to the Item of row 2

### REQ-CAT-042 — Create a Variant attribute
Pattern    : event
Statement  : When an owner saves a new Variant attribute with a code and a nameAr, the system shall create the Variant attribute as active.
Traces     : US-CAT-011
Entities   : ENT-CAT-003
Rationale  : The tenant keeps its own list of dimensions.
Source     : US-CAT-011; POL-CAT-002
Priority   : HIGH

#### AC-CAT-044 — [REQ-CAT-042]
Given : the `variants` flag is on and no attribute with code SIZE exists
When  : the owner saves code SIZE and nameAr «المقاس»
Then  : the Variant attribute is created with isActiveFl true and sortOrder 1

### REQ-CAT-043 — Update a Variant attribute
Pattern    : event
Statement  : When an owner changes the nameAr, nameEn or sortOrder of a Variant attribute, the system shall save the change.
Traces     : US-CAT-011
Entities   : ENT-CAT-003
Rationale  : Names are corrected without touching the Variants.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-045 — [REQ-CAT-043]
Given : the Variant attribute SIZE with nameEn empty
When  : the owner sets nameEn "Size" and saves
Then  : the next read returns nameEn "Size", and the derived nameEn of every Variant carrying a SIZE value includes the value's English name

### REQ-CAT-044 — Deactivate a Variant attribute
Pattern    : event
Statement  : When an owner deactivates a Variant attribute, the system shall set its isActiveFl to false and exclude it from new Variant generation.
Traces     : US-CAT-011
Entities   : ENT-CAT-003
Rationale  : Old dimensions leave the selection lists.
Source     : US-CAT-011; RULE-CAT-017
Priority   : HIGH

#### AC-CAT-046 — [REQ-CAT-044]
Given : the Variant attribute MATERIAL_TYPE used by 0 active Variants
When  : the owner deactivates it
Then  : isActiveFl is false, and the Variant generation list offers 0 values of that attribute

### REQ-CAT-045 — Create a Variant attribute value
Pattern    : event
Statement  : When an owner saves a new value with a code and a nameAr under an active Variant attribute, the system shall create the Variant attribute value as active.
Traces     : US-CAT-011
Entities   : ENT-CAT-004
Rationale  : The tenant keeps its own values (M, أحمر).
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-047 — [REQ-CAT-045]
Given : the active Variant attribute SIZE with values S and M
When  : the owner saves value code L with nameAr «كبير»
Then  : the value L is created under SIZE with isActiveFl true and sortOrder 3

### REQ-CAT-046 — Update a Variant attribute value
Pattern    : event
Statement  : When an owner changes the nameAr, nameEn or sortOrder of a Variant attribute value, the system shall save the change.
Traces     : US-CAT-011
Entities   : ENT-CAT-004
Rationale  : Value names are corrected in one place.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-048 — [REQ-CAT-046]
Given : the value L with nameAr «كبير»
When  : the owner changes nameAr to «لارج L» and saves
Then  : the next read of every Variant carrying L returns a derived nameAr ending in «لارج L»

### REQ-CAT-047 — Deactivate a Variant attribute value
Pattern    : event
Statement  : When an owner deactivates a Variant attribute value, the system shall set its isActiveFl to false and exclude it from new Variant generation.
Traces     : US-CAT-011
Entities   : ENT-CAT-004
Rationale  : Discontinued values leave the selection lists.
Source     : US-CAT-011; RULE-CAT-017
Priority   : HIGH

#### AC-CAT-049 — [REQ-CAT-047]
Given : the value XS used by 0 active Variants
When  : the owner deactivates it
Then  : isActiveFl is false, and the Variant generation list for SIZE omits XS

### REQ-CAT-048 — List Variant attributes
Pattern    : event
Statement  : When a user opens the Variant attributes, the system shall return every Variant attribute with its values, ordered by sortOrder.
Traces     : US-CAT-011
Entities   : ENT-CAT-003, ENT-CAT-004
Rationale  : One view of the tenant's dimensions.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-050 — [REQ-CAT-048]
Given : 2 attributes (SIZE sortOrder 1 with 3 values; COLOUR sortOrder 2 with 4 values)
When  : the owner opens the Variant attributes
Then  : the response lists SIZE then COLOUR, with 3 and 4 values respectively, each list ordered by sortOrder

### REQ-CAT-049 — Generate Variants from attribute values
Pattern    : event
Statement  : When an owner selects values of one to three active Variant attributes for a STANDARD Item, the system shall create one Variant for each selected combination that the Item does not yet carry.
Traces     : US-CAT-011
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Rationale  : Every combination can be picked, counted and bought as itself.
Source     : US-CAT-011; POL-CAT-002; KB:raw-idea.md §4.1
Priority   : HIGH

#### AC-CAT-051 — [REQ-CAT-049]
Given : the STANDARD Item «قميص قطن» with only its default Variant, and `variants` on
When  : the owner selects SIZE values M and L and COLOUR values «أحمر» and «أزرق»
Then  : the Item has 4 active Variants (the default Variant takes M / «أحمر»), each with a distinct combination, and one derived nameAr reads «قميص قطن / L / أزرق»

### REQ-CAT-050 — Deactivate a Variant
Pattern    : event
Statement  : When an owner deactivates a Variant, the system shall set its isActiveFl to false, set the isActiveFl of its active attribute assignments and active barcodes to false, and keep the Variant readable by its identifier.
Traces     : US-CAT-011
Entities   : ENT-CAT-002, ENT-CAT-005, ENT-CAT-009
Rationale  : Discontinued combinations leave the channels but earlier documents still resolve them. A barcode of an inactive Variant can no longer be looked up (RULE-CAT-047), so it is released for RULE-CAT-029 instead of blocking its value (ADR-CAT-063).
Source     : US-CAT-011; RULE-CAT-048; ADR-CAT-063
Priority   : HIGH

#### AC-CAT-052 — [REQ-CAT-050]
Given : an Item with 4 active Variants
When  : the owner deactivates the Variant L / «أزرق»
Then  : the Item read returns 3 active Variants, and a read by that Variant's variantPk returns it with isActiveFl false

#### AC-CAT-145 — [REQ-CAT-050]
Given : an Item with 2 active Variants, the first carrying the active GTIN 6223000000014
When  : the owner deactivates the first Variant, then adds the GTIN 6223000000014 to a Variant of another Item
Then  : a read of the deactivated Variant returns it with isActiveFl false and with no active barcode, and the GTIN 6223000000014 is saved as an active barcode of the other Variant

### REQ-CAT-051 — Duplicate combination
Pattern    : unwanted
Statement  : If a Variant would carry the same combination of values as another active Variant of the same Item, then the system shall reject that Variant with RULE-CAT-013.
Traces     : US-CAT-011
Entities   : ENT-CAT-002, ENT-CAT-005
Rationale  : Each combination exists once.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-053 — [REQ-CAT-051]
Given : an Item with an active Variant M / «أحمر»
When  : a request creates another Variant M / «أحمر» on that Item
Then  : no Variant is created and the response shows ar: «هذه التركيبة من القيم موجودة في متغيّر آخر لهذا الصنف.» · en: "This combination of values already exists on another variant of this item."

### REQ-CAT-052 — Variant limits exceeded
Pattern    : unwanted
Statement  : If a generation request would give an Item more than 3 Variant attributes or more than 300 active Variants, then the system shall reject the whole request with RULE-CAT-014.
Traces     : US-CAT-011
Entities   : ENT-CAT-002, ENT-CAT-005
Rationale  : Bounded combinations per Item (D-10).
Source     : US-CAT-011; D-10
Priority   : HIGH

#### AC-CAT-054 — [REQ-CAT-052]
Given : an Item with 290 active Variants
When  : the owner generates 20 new combinations
Then  : 0 Variants are created and the response shows ar: «الحد الأقصى 3 خصائص و300 متغيّر نشط للصنف الواحد.» · en: "An item can have at most 3 attributes and 300 active variants."

#### AC-CAT-147 — [REQ-CAT-052]
Given : an Item with 280 active Variants
When  : two generation requests, each adding 15 combinations the Item does not carry, are submitted in parallel
Then  : exactly 1 request succeeds, the other is refused with code CAT-422-VARIANT-LIMIT-EXCEEDED, and the Item has 295 active Variants

### REQ-CAT-053 — Last active Variant
Pattern    : unwanted
Statement  : If an owner deactivates the last active Variant of an active Item, then the system shall reject the deactivation with RULE-CAT-015.
Traces     : US-CAT-011
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : An active Item always has a Variant that can be sold.
Source     : US-CAT-011; DP §8 P-04
Priority   : HIGH

#### AC-CAT-055 — [REQ-CAT-053]
Given : an active Item with exactly 1 active Variant
When  : the owner deactivates that Variant
Then  : the Variant stays active and the response shows ar: «لا يمكن إيقاف آخر متغيّر نشط لصنف نشط. أوقف الصنف نفسه بدلًا من ذلك.» · en: "The last active variant of an active item cannot be deactivated. Deactivate the item instead."

#### AC-CAT-146 — [REQ-CAT-053]
Given : an active Item with exactly 2 active Variants
When  : two requests, each deactivating a different one of the 2 Variants, are submitted in parallel
Then  : exactly 1 request succeeds, the other is refused with code CAT-409-LAST-ACTIVE-VARIANT, and the Item keeps exactly 1 active Variant

### REQ-CAT-054 — Attribute or value in use
Pattern    : unwanted
Statement  : If an owner deactivates a Variant attribute or a Variant attribute value that an active Variant carries, then the system shall reject the deactivation with RULE-CAT-017.
Traces     : US-CAT-011
Entities   : ENT-CAT-003, ENT-CAT-004, ENT-CAT-005
Rationale  : Active Variants keep a valid description.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-056 — [REQ-CAT-054]
Given : the value M carried by 12 active Variants
When  : the owner deactivates M
Then  : M stays active and the response shows ar: «هذه الخاصية أو القيمة مستخدمة في 12 متغيّرًا نشطًا. أوقف هذه المتغيّرات أولًا.» · en: "This attribute or value is used by 12 active variants. Deactivate those variants first."

### REQ-CAT-055 — Duplicate attribute or value
Pattern    : unwanted
Statement  : If a Variant attribute or a Variant attribute value repeats the code or nameAr of another one in the same scope, then the system shall reject the request with RULE-CAT-016.
Traces     : US-CAT-011
Entities   : ENT-CAT-003, ENT-CAT-004
Rationale  : Each dimension and value is named once.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-057 — [REQ-CAT-055]
Given : the attribute SIZE exists
When  : the owner creates another attribute with code SIZE
Then  : nothing is created and the response shows ar: «الرمز أو الاسم مستخدم بالفعل. استخدم رمزًا (حتى 30 حرفًا لاتينيًا أو رقمًا أو شرطة) واسمًا مختلفين.» · en: "The code or name is already used. Use a different code (up to 30 Latin letters, digits or hyphens) and name."

### REQ-CAT-056 — Save a Bundle with its components
Pattern    : event
Statement  : When an owner saves an Item of kind BUNDLE, the system shall store its Bundle component lines, each with one component Variant and its quantity.
Traces     : US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-007
Rationale  : A kit or promo pack is sold as one line.
Source     : US-CAT-012; POL-CAT-002; DP §8 P-06
Priority   : HIGH

#### AC-CAT-058 — [REQ-CAT-056]
Given : `bundles` is on and 2 active STANDARD Items exist (a phone and a case)
When  : the owner saves the Bundle «باقة الهاتف مع الجراب» with the phone Variant × 1 and the case Variant × 1
Then  : the Bundle Item is created with itemKindCode BUNDLE, 1 default Variant and 2 active component lines of quantity 1

### REQ-CAT-057 — Change Bundle components
Pattern    : event
Statement  : When an owner adds a component line to a Bundle, removes one or changes its quantity, the system shall save the Bundle's new component lines.
Traces     : US-CAT-012
Entities   : ENT-CAT-007
Rationale  : Packs change with promotions.
Source     : US-CAT-012
Priority   : HIGH

#### AC-CAT-059 — [REQ-CAT-057]
Given : a Bundle with 2 component lines of quantity 1
When  : the owner changes the second line's quantity to 2 and saves
Then  : the Bundle read returns 2 active lines with quantities 1 and 2

### REQ-CAT-058 — Bundle components returned to readers
Pattern    : ubiquitous
Statement  : The system shall return, with every read of a Bundle Item, its active Bundle component lines with the component Variant, its code and the quantity.
Traces     : US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-007
Rationale  : Stock is counted on the component Variants; the Bundle keeps no stock of its own.
Source     : US-CAT-012; DP §8 P-06
Priority   : HIGH

#### AC-CAT-060 — [REQ-CAT-058]
Given : a Bundle with 3 active component lines
When  : a caller reads the Bundle Item
Then  : the response holds 3 component lines, each with componentVariantFk, the Variant code and the quantity

### REQ-CAT-059 — Bundle without a valid component
Pattern    : unwanted
Statement  : If a Bundle is saved with no active component line or with a quantity that is not above zero, then the system shall reject the save with RULE-CAT-018.
Traces     : US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-007
Rationale  : A Bundle is made of real quantities.
Source     : US-CAT-012
Priority   : HIGH

#### AC-CAT-061 — [REQ-CAT-059]
Given : a new Bundle entry with 0 component lines
When  : the owner saves
Then  : nothing is saved and the response shows ar: «أضف مكوّنًا واحدًا على الأقل بكمية أكبر من صفر (حتى 3 أرقام عشرية).» · en: "Add at least one component with a quantity above zero (up to 3 decimals)."

### REQ-CAT-060 — Invalid Bundle component
Pattern    : unwanted
Statement  : If a component line names an inactive Variant, a Variant of a Bundle Item, or a Variant already listed in the same Bundle, then the system shall reject the save with RULE-CAT-019.
Traces     : US-CAT-012
Entities   : ENT-CAT-002, ENT-CAT-007
Rationale  : No nested Bundles, and each component is listed once (D-04).
Source     : US-CAT-012; D-04
Priority   : HIGH

#### AC-CAT-062 — [REQ-CAT-060]
Given : an active Bundle B1
When  : the owner adds the Variant of B1 as a component of a new Bundle B2
Then  : B2 is not saved and the response shows ar: «المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها.» · en: "A component must be an active variant of an item that is not a bundle, listed once per bundle."

### REQ-CAT-061 — Variant attributes on a Bundle
Pattern    : unwanted
Statement  : If a request generates Variants or assigns Variant attribute values for an Item of kind BUNDLE, then the system shall reject the request with RULE-CAT-020.
Traces     : US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Rationale  : A Bundle is one sellable line with one Variant.
Source     : US-CAT-012; D-04
Priority   : HIGH

#### AC-CAT-063 — [REQ-CAT-061]
Given : a Bundle Item with its default Variant
When  : the owner selects SIZE values for it
Then  : 0 Variants are created and the response shows ar: «الحزمة لها متغيّر واحد فقط ولا تقبل خصائص المتغيّرات.» · en: "A bundle has one variant only and takes no variant attributes."

### REQ-CAT-062 — Component Variant deactivation
Pattern    : unwanted
Statement  : If an owner deactivates a Variant, or the Item of a Variant, that is a component of an active Bundle, then the system shall reject the deactivation with RULE-CAT-021.
Traces     : US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-007
Rationale  : An active Bundle never holds a component that cannot be sold.
Source     : US-CAT-012
Priority   : HIGH

#### AC-CAT-064 — [REQ-CAT-062]
Given : a Variant that is a component of 2 active Bundles
When  : the owner deactivates that Variant
Then  : the Variant stays active and the response shows ar: «هذا المتغيّر مكوّن في 2 حزمة نشطة. أزله من هذه الحزم أو أوقفها أولًا.» · en: "This variant is a component of 2 active bundles. Remove it from those bundles or deactivate them first."

#### AC-CAT-151 — [REQ-CAT-062]
Given : a Variant P of an active STANDARD Item that has a second active Variant, and an active Bundle B whose lines do not include P
When  : a request that deactivates P and a request that saves B's components with P added are submitted in parallel
Then  : exactly 1 request succeeds; either P is inactive and the Bundle save is refused with code CAT-422-BUNDLE-COMPONENT-INVALID, or B holds P and the deactivation is refused with code CAT-409-VARIANT-IN-ACTIVE-BUNDLE

### REQ-CAT-063 — Item kind changed after saving
Pattern    : unwanted
Statement  : If an update request changes the itemKindCode of a saved Item, then the system shall reject the request with RULE-CAT-022.
Traces     : US-CAT-012
Entities   : ENT-CAT-001
Rationale  : Downstream modules rely on the kind fixed at creation.
Source     : US-CAT-012; D-04
Priority   : HIGH

#### AC-CAT-065 — [REQ-CAT-063]
Given : a saved STANDARD Item
When  : an update sets itemKindCode to BUNDLE
Then  : the kind stays STANDARD and the response shows ar: «لا يمكن تغيير نوع الصنف بعد حفظه. أنشئ صنفًا جديدًا بالنوع المطلوب.» · en: "The item kind cannot be changed after saving. Create a new item of the required kind."

### REQ-CAT-064 — Create a Category
Pattern    : event
Statement  : When an owner saves a new Category with a code and a nameAr, the system shall create it as active under the chosen parent Category, or at the top level when no parent is chosen.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : Shoppers browse, and reports aggregate, by Category.
Source     : US-CAT-013; POL-CAT-001; D-03
Priority   : —

#### AC-CAT-066 — [REQ-CAT-064]
Given : the active top-level Category «إلكترونيات»
When  : the owner saves code PHONES, nameAr «هواتف» and parent «إلكترونيات»
Then  : the Category PHONES is created with isActiveFl true and parentCategoryFk pointing to «إلكترونيات»

### REQ-CAT-065 — Update a Category
Pattern    : event
Statement  : When an owner changes the nameAr, nameEn, sortOrder or parent of a Category, the system shall save the change.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : The tree is reorganised without re-entering Items.
Source     : US-CAT-013
Priority   : —

#### AC-CAT-067 — [REQ-CAT-065]
Given : the Category PHONES under «إلكترونيات», holding 3 active Items
When  : the owner moves it to the top level and saves
Then  : PHONES has an empty parentCategoryFk, and all 3 Items keep categoryFk PHONES

### REQ-CAT-066 — Deactivate a Category
Pattern    : event
Statement  : When an owner deactivates a Category, the system shall set its isActiveFl to false and exclude it from new selections.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : Retired Categories leave the storefront and the selection lists.
Source     : US-CAT-013; RULE-CAT-048
Priority   : —

#### AC-CAT-068 — [REQ-CAT-066]
Given : a Category with 0 active sub-categories and 0 active Items
When  : the owner deactivates it
Then  : isActiveFl is false, the Item entry's Category list omits it, and a read by categoryPk still returns it

#### AC-CAT-142 — [REQ-CAT-066]
Given : an inactive Item «كوب زجاج» whose Category «أدوات قديمة» was deactivated after the Item
When  : the owner lists Items with the active filter set to inactive, then opens that Item
Then  : the list row and the Item header both show the Category label «أدوات قديمة» (not its identifier), and the Category selection list offers 0 entries named «أدوات قديمة»

### REQ-CAT-067 — Category tree
Pattern    : event
Statement  : When a user opens the Categories, the system shall return the Category tree with each Category under its parent, ordered by sortOrder.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : One view of the classification.
Source     : US-CAT-013
Priority   : —

#### AC-CAT-069 — [REQ-CAT-067]
Given : 2 top-level Categories with 3 and 0 sub-categories
When  : the owner opens the Categories
Then  : the response holds 2 root nodes ordered by sortOrder, the first with 3 children

### REQ-CAT-068 — Items of a Category
Pattern    : event
Statement  : When a caller requests the Items of a Category, the system shall return the active Items of that Category and of its active sub-categories.
Traces     : US-CAT-013
Entities   : ENT-CAT-001, ENT-CAT-006
Rationale  : Storefront browsing and report aggregation by branch of the tree.
Source     : US-CAT-013
Priority   : —

#### AC-CAT-070 — [REQ-CAT-068]
Given : «إلكترونيات» holding 2 active Items, and its sub-category PHONES holding 5 active Items and 1 inactive Item
When  : a caller requests the Items of «إلكترونيات»
Then  : 7 Items are returned

### REQ-CAT-069 — Invalid Category parent
Pattern    : unwanted
Statement  : If a Category would get an inactive parent, itself or one of its sub-categories as parent, or a depth above 5 levels, then the system shall reject the save with RULE-CAT-023.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : The tree stays a bounded, acyclic hierarchy (D-03).
Source     : US-CAT-013; D-03
Priority   : —

#### AC-CAT-071 — [REQ-CAT-069]
Given : PHONES is a sub-category of «إلكترونيات»
When  : the owner sets PHONES as the parent of «إلكترونيات»
Then  : nothing is saved and the response shows ar: «الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات.» · en: "The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels."

### REQ-CAT-070 — Duplicate Category
Pattern    : unwanted
Statement  : If a Category repeats the code of another Category, or the nameAr of another active Category under the same parent, then the system shall reject the save with RULE-CAT-024.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : Shoppers never see two identical Categories side by side.
Source     : US-CAT-013
Priority   : —

#### AC-CAT-072 — [REQ-CAT-070]
Given : an active Category «هواتف» under «إلكترونيات»
When  : the owner creates a second «هواتف» under «إلكترونيات»
Then  : nothing is created and the response shows ar: «يوجد رمز الفئة نفسه، أو الاسم نفسه تحت الفئة الأم نفسها. استخدم رمزًا أو اسمًا مختلفًا.» · en: "The category code already exists, or the name already exists under the same parent. Use a different code or name."

### REQ-CAT-071 — Category deactivation blocked
Pattern    : unwanted
Statement  : If an owner deactivates a Category that holds active sub-categories or active Items, then the system shall reject the deactivation with RULE-CAT-025.
Traces     : US-CAT-013
Entities   : ENT-CAT-001, ENT-CAT-006
Rationale  : Active Items never point to a retired Category (ARCH-5).
Source     : US-CAT-013
Priority   : —

#### AC-CAT-073 — [REQ-CAT-071]
Given : a Category holding 4 active Items
When  : the owner deactivates it
Then  : it stays active and the response shows ar: «لا يمكن إيقاف الفئة لوجود 4 فئة فرعية أو صنف نشط فيها. انقلها أو أوقفها أولًا.» · en: "The category cannot be deactivated while it holds 4 active sub-categories or items. Move or deactivate them first."

#### AC-CAT-153 — [REQ-CAT-071]
Given : an active Category «إلكترونيات» with 0 active sub-categories and 0 active Items
When  : a request that creates the sub-category PHONES under «إلكترونيات» and a request that deactivates «إلكترونيات» are submitted in parallel
Then  : exactly 1 request succeeds; either PHONES is created and the deactivation is refused with code CAT-409-CATEGORY-IN-USE, or «إلكترونيات» is inactive and the create is refused with code CAT-422-CATEGORY-PARENT-INVALID; no active Category has an inactive parent

### REQ-CAT-072 — Inactive Category on an Item
Pattern    : unwanted
Statement  : If an Item is saved with a categoryFk that points to an inactive Category, then the system shall reject the save with RULE-CAT-026.
Traces     : US-CAT-013
Entities   : ENT-CAT-001, ENT-CAT-006
Rationale  : Items are classified only into live Categories.
Source     : US-CAT-013
Priority   : —

#### AC-CAT-074 — [REQ-CAT-072]
Given : an inactive Category OLD
When  : the owner saves an Item with categoryFk OLD
Then  : nothing is saved and the response shows ar: «الفئة المختارة غير نشطة. اختر فئة نشطة.» · en: "The selected category is inactive. Choose an active category."

### REQ-CAT-073 — Brand values offered
Pattern    : event
Statement  : When a user opens the brand field of an Item, the system shall offer the active values of lookup CAT_BRAND, loaded at request time from the core lookup module.
Traces     : US-CAT-014
Entities   : ENT-CAT-001
Rationale  : Every channel describes brands the same way.
Source     : US-CAT-014; D-02
Priority   : —

#### AC-CAT-075 — [REQ-CAT-073]
Given : CAT_BRAND holds 5 active values and 1 inactive value
When  : the owner opens the brand field
Then  : exactly the 5 active values are offered

### REQ-CAT-074 — Unit values offered
Pattern    : event
Statement  : When a user opens a unit-of-measure field of an Item, an Item unit or a barcode, the system shall offer the active values of lookup CAT_UOM, loaded at request time from the core lookup module.
Traces     : US-CAT-014
Entities   : ENT-CAT-001, ENT-CAT-008, ENT-CAT-009
Rationale  : The same field uses the same lookup key on every screen.
Source     : US-CAT-014; D-02
Priority   : —

#### AC-CAT-076 — [REQ-CAT-074]
Given : CAT_UOM holds the 8 seeded values, all active
When  : the owner opens the base unit field
Then  : 8 values are offered, PIECE pre-selected

### REQ-CAT-075 — Lookup value not active
Pattern    : unwanted
Statement  : If a request carries a lookup-backed field value that is not an active value of the field's lookup key, then the system shall reject the request with RULE-CAT-006.
Traces     : US-CAT-014
Entities   : ENT-CAT-001, ENT-CAT-008, ENT-CAT-009, ENT-CAT-011, ENT-CAT-012
Rationale  : Only known codes enter the catalogue.
Source     : US-CAT-014
Priority   : —

#### AC-CAT-077 — [REQ-CAT-075]
Given : CAT_BRAND has no value «XYZ»
When  : the owner saves an Item with brandCode «XYZ»
Then  : nothing is saved and the response shows ar: «القيمة المختارة غير متاحة. اختر قيمة نشطة من القائمة.» · en: "The selected value is not available. Choose an active value from the list."

### REQ-CAT-076 — Deactivated lookup value kept on records
Pattern    : ubiquitous
Statement  : The system shall keep a lookup code on existing records after the value is deactivated in the core lookup module, and exclude that value from new selections.
Traces     : US-CAT-014
Entities   : ENT-CAT-001, ENT-CAT-008, ENT-CAT-009
Rationale  : Retiring a brand or unit never rewrites existing Items.
Source     : US-CAT-014
Priority   : —

#### AC-CAT-078 — [REQ-CAT-076]
Given : 6 Items with brandCode OLDBRAND, then OLDBRAND is deactivated in the core lookup module
When  : the owner reads one of the 6 Items and opens a new Item's brand field
Then  : the Item still returns brandCode OLDBRAND, and the brand field offers 0 entries named OLDBRAND

### REQ-CAT-077 — Add an Item unit
Pattern    : event
Statement  : When an owner adds a unit to an Item with its conversion factor to the base unit, the system shall create the Item unit as active, with isPurchaseFl and isSalesFl true unless the owner clears them.
Traces     : US-CAT-015
Entities   : ENT-CAT-001, ENT-CAT-008
Rationale  : Purchasing, selling and stock agree on quantities.
Source     : US-CAT-015 (registry AUTO "Item unit conversion owned by CAT")
Priority   : —

#### AC-CAT-079 — [REQ-CAT-077]
Given : an Item with base unit PIECE
When  : the owner adds unit CARTON with conversionFactor 12
Then  : the Item unit CARTON is created with conversionFactor 12.000000, isPurchaseFl true, isSalesFl true and isActiveFl true

### REQ-CAT-078 — Update an Item unit
Pattern    : event
Statement  : When an owner changes isPurchaseFl or isSalesFl of an Item unit, the system shall save the change.
Traces     : US-CAT-015
Entities   : ENT-CAT-008
Rationale  : The owner states whether a unit is used for buying, for selling or for both.
Source     : US-CAT-015
Priority   : —

#### AC-CAT-080 — [REQ-CAT-078]
Given : the Item unit CARTON with isSalesFl true
When  : the owner clears isSalesFl and saves
Then  : the next read returns isSalesFl false, isPurchaseFl true and conversionFactor unchanged

### REQ-CAT-079 — Deactivate an Item unit
Pattern    : event
Statement  : When an owner deactivates an Item unit, the system shall set its isActiveFl to false and keep it readable by its identifier.
Traces     : US-CAT-015
Entities   : ENT-CAT-008
Rationale  : Earlier documents keep resolving the unit they used.
Source     : US-CAT-015; RULE-CAT-048
Priority   : —

#### AC-CAT-081 — [REQ-CAT-079]
Given : an active Item unit CARTON
When  : the owner deactivates it
Then  : isActiveFl is false, the Item read lists 0 active CARTON units, and a read by itemUnitPk returns it

### REQ-CAT-080 — Invalid Item unit
Pattern    : unwanted
Statement  : If an Item unit repeats the base unit or another active unit of the same Item, or carries a conversion factor that is not above zero, then the system shall reject the save with RULE-CAT-027.
Traces     : US-CAT-015
Entities   : ENT-CAT-001, ENT-CAT-008
Rationale  : One factor per unit, with no ambiguous conversion.
Source     : US-CAT-015
Priority   : —

#### AC-CAT-082 — [REQ-CAT-080]
Given : an Item with base unit PIECE
When  : the owner adds Item unit PIECE with conversionFactor 1
Then  : nothing is saved and the response shows ar: «الوحدة يجب أن تختلف عن الوحدة الأساسية وألا تتكرر، ومعامل التحويل أكبر من صفر (حتى 6 أرقام عشرية).» · en: "The unit must differ from the base unit and appear once, with a conversion factor above zero (up to 6 decimals)."

### REQ-CAT-081 — Conversion factor changed
Pattern    : unwanted
Statement  : If an update request changes the conversionFactor of a saved Item unit, then the system shall reject the request with RULE-CAT-028.
Traces     : US-CAT-015
Entities   : ENT-CAT-008
Rationale  : Quantities recorded by downstream modules keep their meaning (D-14).
Source     : US-CAT-015; D-14
Priority   : —

#### AC-CAT-083 — [REQ-CAT-081]
Given : the Item unit CARTON with conversionFactor 12
When  : an update sets it to 24
Then  : the factor stays 12 and the response shows ar: «لا يمكن تعديل معامل التحويل بعد الحفظ. أوقف هذه الوحدة وأضفها من جديد بالمعامل الصحيح.» · en: "The conversion factor cannot be changed after saving. Deactivate this unit and add it again with the correct factor."

### REQ-CAT-082 — Base unit changed
Pattern    : unwanted
Statement  : If an update request changes the baseUomCode of a saved Item, then the system shall reject the request with RULE-CAT-007.
Traces     : US-CAT-015
Entities   : ENT-CAT-001
Rationale  : Stock and documents downstream are kept in the base unit (D-14).
Source     : US-CAT-015; D-14
Priority   : —

#### AC-CAT-084 — [REQ-CAT-082]
Given : a saved Item with base unit PIECE
When  : an update sets baseUomCode to KILOGRAM
Then  : the base unit stays PIECE and the response shows ar: «لا يمكن تغيير وحدة القياس الأساسية بعد حفظ الصنف. أضف وحدة أخرى للصنف بدلًا من ذلك.» · en: "The base unit cannot be changed after the item is saved. Add another unit to the item instead."

### REQ-CAT-083 — Add a GTIN barcode
Pattern    : event
Statement  : When an owner adds a GTIN barcode to a Variant, the system shall store it as an active barcode of that Variant, bound to the Item's base unit when no unit is chosen.
Traces     : US-CAT-016
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : Scanning the barcode printed on the goods finds the exact Variant.
Source     : US-CAT-016; DP §8 P-07
Priority   : —

#### AC-CAT-085 — [REQ-CAT-083]
Given : an active Variant with 0 barcodes and base unit PIECE
When  : the owner adds GTIN 6223000000014 with no unit chosen
Then  : the Variant has 1 active barcode with barcodeTypeCode GTIN and uomCode PIECE

### REQ-CAT-084 — Generate an internal barcode
Pattern    : event
Statement  : When an owner requests an internal barcode for a Variant, the system shall assign a 13-digit barcode that starts with 04, takes its next 10 digits from the core number series CAT_INTERNAL_BARCODE and ends with the GS1 check digit.
Traces     : US-CAT-016
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : Goods without a printed GTIN still scan (GS1 restricted-circulation prefix 04, D-05).
Source     : US-CAT-016; D-05
Priority   : —

#### AC-CAT-086 — [REQ-CAT-084]
Given : series CAT_INTERNAL_BARCODE last issued 0000000041
When  : the owner requests an internal barcode for a Variant
Then  : the Variant gets the active INTERNAL barcode 0400000000428 (04 + 0000000042 + check digit 8)

### REQ-CAT-085 — Deactivate a barcode
Pattern    : event
Statement  : When an owner deactivates a barcode, the system shall set its isActiveFl to false and exclude it from barcode lookups.
Traces     : US-CAT-016
Entities   : ENT-CAT-009
Rationale  : A withdrawn barcode stops scanning.
Source     : US-CAT-016; RULE-CAT-048
Priority   : —

#### AC-CAT-087 — [REQ-CAT-085]
Given : an active barcode 6223000000014
When  : the owner deactivates it and a lookup of 6223000000014 follows
Then  : isActiveFl is false and the lookup returns no Variant

### REQ-CAT-086 — Barcode lookup
Pattern    : event
Statement  : When a barcode value is submitted for lookup, the system shall return the active Variant of an active Item, that Item and the unit of the active barcode with that value.
Traces     : US-CAT-016
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Rationale  : Selling and receiving find the exact Variant and unit; an Item that left every channel no longer scans (REQ-CAT-005).
Source     : US-CAT-016; ADR-CAT-059
Priority   : —

#### AC-CAT-088 — [REQ-CAT-086]
Given : the active barcode 16223000000011 bound to unit CARTON of a Variant VAR-000200
When  : a seller/cashier scans 16223000000011
Then  : the response returns VAR-000200, its Item and uomCode CARTON

#### AC-CAT-135 — [REQ-CAT-086]
Given : the active barcode 6223000000021 on the active Variant of an Item that the owner deactivated
When  : a seller/cashier scans 6223000000021
Then  : no Variant is returned and the response shows ar: «لا يوجد صنف بهذا الباركود. ابحث بالاسم أو أضف الباركود إلى الصنف.» · en: "No item has this barcode. Search by name or add the barcode to the item."

### REQ-CAT-087 — Duplicate barcode
Pattern    : unwanted
Statement  : If a barcode value equals an active barcode of the tenant, then the system shall reject the new barcode with RULE-CAT-029.
Traces     : US-CAT-016
Entities   : ENT-CAT-009
Rationale  : One scan resolves to one Variant.
Source     : US-CAT-016
Priority   : —

#### AC-CAT-089 — [REQ-CAT-087]
Given : the active GTIN 6223000000014 on an Item with code ITM-000005
When  : the owner adds 6223000000014 to another Variant
Then  : nothing is saved and the response shows ar: «هذا الباركود مستخدم في الصنف ITM-000005. افتح ذلك الصنف أو استخدم باركودًا آخر.» · en: "This barcode is already used by item ITM-000005. Open that item or use another barcode."

#### AC-CAT-150 — [REQ-CAT-087]
Given : no active barcode 6223000000038 in the tenant
When  : two requests that add the GTIN 6223000000038 to two different Variants are submitted in parallel
Then  : exactly 1 barcode 6223000000038 is saved, and the other request is refused with code CAT-409-BARCODE-DUPLICATE

### REQ-CAT-088 — Invalid GTIN
Pattern    : unwanted
Statement  : If a GTIN barcode does not have 8, 12, 13 or 14 digits with a valid GS1 check digit, then the system shall reject it with RULE-CAT-030.
Traces     : US-CAT-016
Entities   : ENT-CAT-009
Rationale  : A mistyped barcode never enters the catalogue.
Source     : US-CAT-016
Priority   : —

#### AC-CAT-090 — [REQ-CAT-088]
Given : a Variant
When  : the owner adds GTIN 6223000000015 (wrong check digit)
Then  : nothing is saved and the response shows ar: «باركود GTIN يجب أن يتكون من 8 أو 12 أو 13 أو 14 رقمًا برقم تحقق صحيح.» · en: "A GTIN barcode must have 8, 12, 13 or 14 digits with a valid check digit."

### REQ-CAT-089 — Barcode unit not of the Item
Pattern    : unwanted
Statement  : If a barcode names a unit that is neither the Item's base unit nor an active Item unit of that Item, then the system shall reject the barcode with RULE-CAT-031.
Traces     : US-CAT-016
Entities   : ENT-CAT-001, ENT-CAT-008, ENT-CAT-009
Rationale  : A carton barcode always converts to a known quantity.
Source     : US-CAT-016
Priority   : —

#### AC-CAT-091 — [REQ-CAT-089]
Given : an Item with base unit PIECE and no Item unit BOX
When  : the owner adds a barcode with uomCode BOX
Then  : nothing is saved and the response shows ar: «وحدة الباركود يجب أن تكون الوحدة الأساسية للصنف أو إحدى وحداته النشطة.» · en: "The barcode unit must be the item's base unit or one of its active units."

### REQ-CAT-090 — Barcode not found
Pattern    : unwanted
Statement  : If no active barcode and no active scale label layout matches a looked-up value, then the system shall return no Variant with RULE-CAT-047.
Traces     : US-CAT-016
Entities   : ENT-CAT-009
Rationale  : The cashier gets a next action instead of a dead end.
Source     : US-CAT-016; KB:raw-idea.md §6.5
Priority   : —

#### AC-CAT-092 — [REQ-CAT-090]
Given : no active barcode equals 6229999999995 and no scale label layout uses prefix 62
When  : a seller/cashier scans 6229999999995
Then  : no Variant is returned and the response shows ar: «لا يوجد صنف بهذا الباركود. ابحث بالاسم أو أضف الباركود إلى الصنف.» · en: "No item has this barcode. Search by name or add the barcode to the item."

### REQ-CAT-091 — Barcode clashes with a scale prefix
Pattern    : unwanted
Statement  : If a GTIN or internal barcode begins with the prefix of an active scale label layout, then the system shall reject the barcode with RULE-CAT-032.
Traces     : US-CAT-016
Entities   : ENT-CAT-009, ENT-CAT-011
Rationale  : A scan is never read as both a barcode and a scale label.
Source     : US-CAT-016; D-05
Priority   : —

#### AC-CAT-093 — [REQ-CAT-091]
Given : an active scale label layout with prefix 20
When  : the owner adds GTIN 2000000000008
Then  : nothing is saved and the response shows ar: «الباركود يبدأ ببادئة ملصق ميزان نشط (20). استخدم باركودًا آخر أو عدّل إعداد الميزان.» · en: "The barcode starts with the prefix of an active scale label layout (20). Use another barcode or change the scale settings."

### REQ-CAT-092 — Create a scale label layout
Pattern    : event
Statement  : When an owner saves a new scale label layout with its prefix, scale code length, embedded value type and decimals, the system shall create the layout as active.
Traces     : US-CAT-017
Entities   : ENT-CAT-011
Rationale  : The owner states how the scale prints its labels.
Source     : US-CAT-017; ADR-CAT-042
Priority   : LOW

#### AC-CAT-094 — [REQ-CAT-092]
Given : no active scale label layout
When  : the owner saves prefix 20, scaleCodeLength 5, embeddedValueTypeCode WEIGHT and valueDecimals 3
Then  : the layout is created with isActiveFl true

### REQ-CAT-093 — Update a scale label layout
Pattern    : event
Statement  : When an owner changes the embedded value type, decimals or descriptions of a scale label layout, the system shall save the change and read the embedded value of every later scanned label with that prefix by the new type and decimals.
Traces     : US-CAT-017
Entities   : ENT-CAT-011, ENT-CAT-009
Rationale  : A scale replaced or re-set keeps working. The prefix and the scale code length are fixed after creation, because the layout's SCALE barcodes were validated against that length (RULE-CAT-033); a change of type or decimals changes only how the embedded value is read, and every SCALE barcode keeps resolving (ADR-CAT-064).
Source     : US-CAT-017; ADR-CAT-064
Priority   : LOW

#### AC-CAT-095 — [REQ-CAT-093]
Given : the layout with prefix 20 and embeddedValueTypeCode WEIGHT
When  : the owner changes embeddedValueTypeCode to PRICE and valueDecimals to 2
Then  : the next read returns PRICE and 2

#### AC-CAT-152 — [REQ-CAT-093]
Given : the active layout prefix 20, scaleCodeLength 5, WEIGHT, valueDecimals 3, and a Variant with SCALE barcode 01230
When  : the owner changes valueDecimals to 2 and a seller/cashier then scans 2001230012342
Then  : the response returns that Variant with a weight of 12.34, and the SCALE barcode 01230 is unchanged and active

### REQ-CAT-094 — Deactivate a scale label layout
Pattern    : event
Statement  : When an owner deactivates a scale label layout, the system shall set its isActiveFl to false, stop decoding values with its prefix, and keep every SCALE barcode stored and active.
Traces     : US-CAT-017
Entities   : ENT-CAT-011, ENT-CAT-009
Rationale  : A scale taken out of use stops being decoded. Its SCALE barcodes stay stored but are not decodable until an active layout with the same scale code length exists again (ADR-CAT-064).
Source     : US-CAT-017; ADR-CAT-064
Priority   : LOW

#### AC-CAT-096 — [REQ-CAT-094]
Given : the active layout with prefix 20 (scaleCodeLength 5), no other active layout, and a Variant with the active SCALE barcode 01230
When  : the owner deactivates the layout and a seller/cashier scans 2001230012342 (prefix 20, code 01230, valid check digit)
Then  : isActiveFl is false and the lookup returns no Variant with the RULE-CAT-047 message

### REQ-CAT-095 — Read the catalogue settings
Pattern    : event
Statement  : When an owner opens the catalogue settings, the system shall return every scale label layout and every Unit e-invoice code with its active state.
Traces     : US-CAT-017, US-CAT-021
Entities   : ENT-CAT-011, ENT-CAT-012
Rationale  : One place for the tenant's catalogue settings.
Source     : US-CAT-017; US-CAT-021; ADR-CAT-042
Priority   : HIGH

#### AC-CAT-097 — [REQ-CAT-095]
Given : 1 active scale label layout, 8 active and 1 inactive Unit e-invoice codes
When  : the owner opens the catalogue settings
Then  : the response lists 1 layout and 9 Unit e-invoice codes, each with its isActiveFl

### REQ-CAT-096 — Scale code entry offered
Pattern    : optional
Statement  : Where at least one active scale label layout exists, the system shall offer the SCALE barcode type in a Variant's barcode entry.
Traces     : US-CAT-017
Entities   : ENT-CAT-009, ENT-CAT-011
Rationale  : Weighed-item entry appears only when it can be used, with no new flag (OI-10).
Source     : US-CAT-017; module-registry OI-10
Priority   : LOW

#### AC-CAT-098 — [REQ-CAT-096]
Given : 1 active scale label layout with scaleCodeLength 5
When  : the owner adds barcode type SCALE with value 01230 to a Variant
Then  : the Variant has 1 active SCALE barcode 01230

### REQ-CAT-097 — Decode a scale label
Pattern    : event
Statement  : When a submitted 13-digit value begins with the prefix of an active scale label layout, the system shall return the active Variant whose SCALE barcode equals the embedded scale code, with the embedded value as a weight or a price carrying the layout's decimals.
Traces     : US-CAT-017
Entities   : ENT-CAT-009, ENT-CAT-011
Rationale  : Weighed goods are sold without re-keying.
Source     : US-CAT-017; DP §8 P-07
Priority   : LOW

#### AC-CAT-099 — [REQ-CAT-097]
Given : the active layout prefix 20, scaleCodeLength 5, WEIGHT, valueDecimals 3, and a Variant with SCALE barcode 01230
When  : a seller/cashier scans 2001230012342 (prefix 20, code 01230, value 01234, check digit 2)
Then  : the response returns that Variant with a weight of 1.234 in the Item's base unit

### REQ-CAT-098 — Invalid scale label layout
Pattern    : unwanted
Statement  : If a scale label layout carries a prefix outside 02 and 20–29, a prefix used by another active layout, a scale code length outside 4–6 or decimals outside 0–3, then the system shall reject the save with RULE-CAT-034.
Traces     : US-CAT-017
Entities   : ENT-CAT-011
Rationale  : Layouts follow the GS1 restricted-circulation prefixes for variable-measure items.
Source     : US-CAT-017; module-registry OI-10
Priority   : LOW

#### AC-CAT-100 — [REQ-CAT-098]
Given : the catalogue settings
When  : the owner saves a layout with prefix 62
Then  : nothing is saved and the response shows ar: «البادئة رقمان (02 أو من 20 إلى 29) غير مستخدمة في إعداد نشط آخر، وطول رمز الميزان من 4 إلى 6، والأرقام العشرية من 0 إلى 3.» · en: "The prefix is two digits (02 or 20 to 29) not used by another active layout, the scale code length is 4 to 6, and the decimals are 0 to 3."

### REQ-CAT-099 — Scale code length mismatch
Pattern    : unwanted
Statement  : If a SCALE barcode is not all digits, does not match the scale code length of an active layout, or is added while no active layout exists, then the system shall reject it with RULE-CAT-033.
Traces     : US-CAT-017
Entities   : ENT-CAT-009, ENT-CAT-011
Rationale  : Every scale code can be found inside a printed label.
Source     : US-CAT-017
Priority   : LOW

#### AC-CAT-101 — [REQ-CAT-099]
Given : the only active layout has scaleCodeLength 5
When  : the owner adds SCALE barcode 123
Then  : nothing is saved and the response shows ar: «رمز الميزان يجب أن يتكون من 5 أرقام، ويلزم وجود إعداد ملصق ميزان نشط.» · en: "The scale code must have 5 digits, and an active scale label layout is required."

### REQ-CAT-100 — Scale label not readable
Pattern    : unwanted
Statement  : If a value that begins with an active layout prefix has a wrong check digit or an embedded scale code that no active SCALE barcode carries, then the system shall return no Variant with RULE-CAT-035.
Traces     : US-CAT-017
Entities   : ENT-CAT-009, ENT-CAT-011
Rationale  : A misread label never sells the wrong goods.
Source     : US-CAT-017
Priority   : LOW

#### AC-CAT-102 — [REQ-CAT-100]
Given : the active layout prefix 20 and no SCALE barcode 09999
When  : a seller/cashier scans a valid-check-digit label 20 09999 01234 x
Then  : no Variant is returned and the response shows ar: «تعذّر قراءة ملصق الميزان: رقم التحقق غير صحيح أو رمز الميزان غير معرَّف. اطلب الصنف بالاسم أو أضف رمز الميزان إليه.» · en: "The scale label could not be read: the check digit is wrong or the scale code is not defined. Find the item by name or add the scale code to it."

### REQ-CAT-101 — Find Variants for labels
Pattern    : event
Statement  : When a user searches Variants by code, name or barcode, the system shall return a page of the active Variants of active Items with their Item nameAr and their first active barcode.
Traces     : US-CAT-018
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Rationale  : Warehouse staff pick the Variants to label; an Item that left every channel gets no new labels (REQ-CAT-005).
Source     : US-CAT-018; ADR-CAT-059
Priority   : —

#### AC-CAT-103 — [REQ-CAT-101]
Given : 3 active Variants of active Items whose Item nameAr contains «قميص»
When  : warehouse staff search Variants with «قميص» and page size 20
Then  : 3 Variants are returned, each with its Item nameAr and its earliest-created active barcode (empty when it has none)

#### AC-CAT-136 — [REQ-CAT-101]
Given : 2 active Variants of an active Item named «حذاء رياضي» and 1 active Variant of an inactive Item named «حذاء جلد»
When  : warehouse staff search Variants with «حذاء» and page size 20
Then  : exactly 2 Variants are returned, both of «حذاء رياضي», and 0 Variants of «حذاء جلد»

### REQ-CAT-102 — Produce barcode labels
Pattern    : event
Statement  : When a user submits a label request listing Variants with a copy count for each, the system shall return one printable document, produced through the core print service, holding per copy the Variant nameAr, the Variant code and the Variant's earliest-created active barcode.
Traces     : US-CAT-018
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : Goods without a printed GTIN can still be scanned at the point of sale.
Source     : US-CAT-018; module-registry AUTO (report, PrintApi)
Priority   : —

#### AC-CAT-104 — [REQ-CAT-102]
Given : 2 Variants that each carry an active barcode
When  : warehouse staff request 10 copies of the first and 5 of the second
Then  : one printable document holding 15 labels is returned, each label showing nameAr, code and barcode

### REQ-CAT-103 — Label copy limits
Pattern    : unwanted
Statement  : If a label request asks fewer than 1 or more than 999 copies of a Variant, or more than 5,000 labels in total, then the system shall reject the request with RULE-CAT-036.
Traces     : US-CAT-018
Entities   : ENT-CAT-002
Rationale  : Bounded print jobs (D-10).
Source     : US-CAT-018; D-10
Priority   : —

#### AC-CAT-105 — [REQ-CAT-103]
Given : a label request
When  : warehouse staff ask 1,000 copies of one Variant
Then  : no document is produced and the response shows ar: «عدد النسخ لكل متغيّر من 1 إلى 999، وإجمالي الملصقات لا يتجاوز 5000.» · en: "Copies per variant are 1 to 999, and the total cannot exceed 5,000 labels."

### REQ-CAT-104 — Variant without a barcode
Pattern    : unwanted
Statement  : If a label request lists a Variant with no active barcode, then the system shall reject the request with RULE-CAT-037 and list those Variants.
Traces     : US-CAT-018
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : No label is printed without a scannable code; the next action generates one.
Source     : US-CAT-018; KB:raw-idea.md §6.5
Priority   : —

#### AC-CAT-106 — [REQ-CAT-104]
Given : a label request for 3 Variants, 2 of which have no active barcode
When  : warehouse staff submit it
Then  : no document is produced, the 2 Variants are listed, and the response shows ar: «لا يوجد باركود نشط لـ 2 متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع.» · en: "2 variants have no active barcode. Generate an internal barcode for them, then print."

### REQ-CAT-105 — Add an image
Pattern    : event
Statement  : When an owner adds to an Item an image file already uploaded to the core public image storage, the system shall create an active Item media record that points to that file, with nameAr defaulting to the Item's nameAr.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : Shoppers see the Item as it really is; the bytes are kept by the core file module only.
Source     : US-CAT-019; DP §8 P-12; ADR-CAT-056
Priority   : MEDIUM

#### AC-CAT-107 — [REQ-CAT-105]
Given : an Item «كوب زجاج» with 2 active images
When  : the owner uploads a 1 MB PNG with no description
Then  : the Item has 3 active media records; the new one has nameAr «كوب زجاج», sortOrder 3 and a fileDocumentRef from the public image storage

### REQ-CAT-106 — Update an image
Pattern    : event
Statement  : When an owner changes the sortOrder, isPrimaryFl, nameAr, nameEn or variantFk of an Item media record, the system shall save the change.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : The owner orders the images, chooses the main one and binds images to Variants.
Source     : US-CAT-019; RULE-CAT-039
Priority   : MEDIUM

#### AC-CAT-108 — [REQ-CAT-106]
Given : an Item whose primary image is image A, and image B is not primary
When  : the owner sets isPrimaryFl on image B
Then  : B is primary, A is not, the Item has exactly 1 primary image, and the response shows ar: «للصنف صورة رئيسية واحدة فقط؛ تم تعيين الصورة المختارة رئيسية بدل السابقة.» · en: "An item has exactly one main image; the selected image replaced the previous main image."

#### AC-CAT-148 — [REQ-CAT-106]
Given : an Item with active images A (primary), B and C
When  : a request that sets isPrimaryFl on B and a request that sets isPrimaryFl on C are submitted in parallel
Then  : both requests complete one after the other, and the Item then has exactly 1 primary image, B or C

### REQ-CAT-107 — Remove an image
Pattern    : event
Statement  : When an owner removes an image from an Item, the system shall set the Item media record's isActiveFl to false and exclude it from every image read.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : Outdated images leave the storefront.
Source     : US-CAT-019
Priority   : MEDIUM

#### AC-CAT-109 — [REQ-CAT-107]
Given : an Item with 3 active images
When  : the owner removes the image with sortOrder 2
Then  : the Item read returns 2 images

### REQ-CAT-108 — First image is the main image
Pattern    : event
Statement  : When the first active image of an Item is added, the system shall mark it as primary.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : Every Item with images has a main image without an extra step.
Source     : US-CAT-019; RULE-CAT-039
Priority   : MEDIUM

#### AC-CAT-110 — [REQ-CAT-108]
Given : an Item with 0 active images
When  : the owner uploads one image
Then  : that image has isPrimaryFl true, and exactly 1 active image of the Item has isPrimaryFl true

### REQ-CAT-109 — Images of a Variant
Pattern    : event
Statement  : When a caller requests the images of a Variant, the system shall return the active media bound to that Variant, or the Item's active media bound to no Variant when none is bound to it, with the primary image first and the others by sortOrder.
Traces     : US-CAT-019
Entities   : ENT-CAT-002, ENT-CAT-010
Rationale  : The storefront shows the right image for each Variant.
Source     : US-CAT-019
Priority   : MEDIUM

#### AC-CAT-111 — [REQ-CAT-109]
Given : an Item with 2 images bound to no Variant, and 1 image bound to Variant «أحمر»
When  : the storefront requests the images of Variant «أحمر», then of Variant «أزرق»
Then  : the first request returns 1 image (the «أحمر» image), and the second returns the 2 unbound images, primary first

### REQ-CAT-110 — Image file not accepted
Pattern    : unwanted
Statement  : If an uploaded image is not JPG, PNG or WEBP, exceeds 5 MB, or would give the Item more than 20 active images, then the system shall reject the upload with RULE-CAT-038.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : Storefront pages load bounded media (D-10). The file part of RULE-CAT-038 (type, size, public storage) is enforced at upload by the FILE category CAT_ITEM_IMAGE that CAT registers, in the file module's own envelope; CAT itself refuses the 21st active image (ADR-CAT-076).
Source     : US-CAT-019; D-10; ADR-CAT-076
Priority   : MEDIUM

#### AC-CAT-112 — [REQ-CAT-110]
Given : an Item with 20 active images
When  : the owner uploads a 7 MB JPG, then a 1 MB PNG as a 21st image
Then  : the 7 MB JPG is refused at its upload to the image category, in the file module's envelope, and the 1 MB PNG is refused with ar: «الصورة يجب أن تكون JPG أو PNG أو WEBP بحجم لا يتجاوز 5 ميجابايت، وبحد أقصى 20 صورة نشطة للصنف.» · en: "The image must be JPG, PNG or WEBP of at most 5 MB, with at most 20 active images per item."; the Item keeps 20 active media records

### REQ-CAT-111 — Main image removed
Pattern    : unwanted
Statement  : If the primary image of an Item is removed while other active images remain, then the system shall mark the remaining active image with the lowest sortOrder as primary.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : The Item never loses its main image while it has images.
Source     : US-CAT-019; RULE-CAT-039
Priority   : MEDIUM

#### AC-CAT-113 — [REQ-CAT-111]
Given : an Item with primary image sortOrder 1 and other images sortOrder 2 and 3
When  : the owner removes the primary image
Then  : the image with sortOrder 2 has isPrimaryFl true, and exactly 1 active image is primary

### REQ-CAT-112 — Image bound to another Item's Variant
Pattern    : unwanted
Statement  : If an Item media record names a variantFk of another Item, then the system shall reject the save with RULE-CAT-040.
Traces     : US-CAT-019
Entities   : ENT-CAT-002, ENT-CAT-010
Rationale  : An image always describes its own Item.
Source     : US-CAT-019
Priority   : MEDIUM

#### AC-CAT-114 — [REQ-CAT-112]
Given : an image of Item A
When  : the owner binds it to a Variant of Item B
Then  : nothing is saved and the response shows ar: «المتغيّر المختار لا ينتمي إلى هذا الصنف.» · en: "The selected variant does not belong to this item."

### REQ-CAT-113 — Publish an Item
Pattern    : event
Statement  : When an owner publishes an active Item, the system shall set its publishStateCode to PUBLISHED.
Traces     : US-CAT-020
Entities   : ENT-CAT-001
Rationale  : The owner decides what is offered online.
Source     : US-CAT-020; POL-CAT-008
Priority   : MEDIUM

#### AC-CAT-115 — [REQ-CAT-113]
Given : an active UNPUBLISHED Item with 1 active Variant
When  : the owner publishes it
Then  : publishStateCode is PUBLISHED and 1 catalogue-change event with change kind UPDATED is published

### REQ-CAT-114 — Unpublish an Item
Pattern    : event
Statement  : When an owner unpublishes an Item, the system shall set its publishStateCode to UNPUBLISHED and keep its isActiveFl unchanged.
Traces     : US-CAT-020
Entities   : ENT-CAT-001
Rationale  : The Item leaves the storefront and stays on sale in the back-office and at the point of sale.
Source     : US-CAT-020; POL-CAT-008
Priority   : MEDIUM

#### AC-CAT-116 — [REQ-CAT-114]
Given : an active PUBLISHED Item
When  : the owner unpublishes it
Then  : publishStateCode is UNPUBLISHED, isActiveFl is true, and the point-of-sale search still returns the Item

### REQ-CAT-115 — Storefront reads published Items only
Pattern    : event
Statement  : When the storefront requests catalogue Items, the system shall return only Items with isActiveFl true and publishStateCode PUBLISHED.
Traces     : US-CAT-020
Entities   : ENT-CAT-001
Rationale  : Shoppers see only what the owner chose to sell online.
Source     : US-CAT-020
Priority   : MEDIUM

#### AC-CAT-117 — [REQ-CAT-115]
Given : 10 Items: 6 active PUBLISHED, 3 active UNPUBLISHED, 1 inactive
When  : the storefront requests catalogue Items
Then  : exactly the 6 active PUBLISHED Items are returned

### REQ-CAT-116 — Publish blocked
Pattern    : unwanted
Statement  : If an owner publishes an inactive Item or an Item with no active Variant, then the system shall reject the request with RULE-CAT-041.
Traces     : US-CAT-020
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : The storefront never offers something that cannot be sold.
Source     : US-CAT-020
Priority   : MEDIUM

#### AC-CAT-118 — [REQ-CAT-116]
Given : an inactive Item
When  : the owner publishes it
Then  : publishStateCode stays UNPUBLISHED and the response shows ar: «لا يمكن نشر صنف غير نشط أو بلا متغيّر نشط. فعّل الصنف أو أحد متغيّراته أولًا.» · en: "An inactive item, or one with no active variant, cannot be published. Activate the item or one of its variants first."

### REQ-CAT-117 — Item e-invoice code
Pattern    : event
Statement  : When an owner saves the e-invoice code type and code of an Item, the system shall store them as the Item's e-invoice code.
Traces     : US-CAT-021
Entities   : ENT-CAT-001
Rationale  : Invoices from every channel carry an accepted classification code.
Source     : US-CAT-021; PRD P-4; DP §8 P-08
Priority   : HIGH

#### AC-CAT-119 — [REQ-CAT-117]
Given : an Item with no e-invoice code
When  : the owner saves einvoiceCodeTypeCode EGS and einvoiceCode «EG-123456789-1001»
Then  : the next read returns EGS and «EG-123456789-1001»

### REQ-CAT-118 — Variant e-invoice override
Pattern    : event
Statement  : When an owner saves an e-invoice code type and code on a Variant, the system shall store them as that Variant's override of the Item's e-invoice code.
Traces     : US-CAT-021
Entities   : ENT-CAT-002
Rationale  : A Variant classified differently from its Item is invoiced correctly.
Source     : US-CAT-021
Priority   : HIGH

#### AC-CAT-120 — [REQ-CAT-118]
Given : an Item with e-invoice code EGS «EG-123456789-1001» and 2 Variants
When  : the owner saves GS1 «6223000000014» on the second Variant
Then  : the second Variant stores GS1 «6223000000014», and the first Variant stores no override

### REQ-CAT-119 — Effective e-invoice data of a Variant
Pattern    : event
Statement  : When a caller reads a Variant, the system shall return its effective e-invoice code type and code, being the Variant's override when present and the Item's otherwise, together with the Item's taxClassCode.
Traces     : US-CAT-021
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Sales and Tax read one resolved value per Variant.
Source     : US-CAT-021
Priority   : HIGH

#### AC-CAT-121 — [REQ-CAT-119]
Given : the Item and Variants of AC-CAT-120, with taxClassCode STANDARD
When  : a caller reads each Variant
Then  : the first Variant returns EGS «EG-123456789-1001», the second returns GS1 «6223000000014», and both return taxClassCode STANDARD

### REQ-CAT-120 — Create a Unit e-invoice code
Pattern    : event
Statement  : When an owner saves an e-invoice unit for a unit of measure that has no active Unit e-invoice code, the system shall create the Unit e-invoice code as active.
Traces     : US-CAT-021
Entities   : ENT-CAT-012
Rationale  : Every unit carries the unit code the tax authority accepts.
Source     : US-CAT-021; D-12
Priority   : HIGH

#### AC-CAT-122 — [REQ-CAT-120]
Given : CAT_UOM gains the value BAG, which has no Unit e-invoice code
When  : the owner saves key BAG with einvoiceUnitCode EA
Then  : an active Unit e-invoice code for BAG with EA is created

### REQ-CAT-121 — Update a Unit e-invoice code
Pattern    : event
Statement  : When an owner changes the einvoiceUnitCode of an active Unit e-invoice code, the system shall save the change.
Traces     : US-CAT-021
Entities   : ENT-CAT-012
Rationale  : Mappings are corrected in one place.
Source     : US-CAT-021
Priority   : HIGH

#### AC-CAT-123 — [REQ-CAT-121]
Given : the Unit e-invoice code for BAG with EA
When  : the owner changes einvoiceUnitCode to BX
Then  : the next read returns BX for BAG

### REQ-CAT-122 — Deactivate a Unit e-invoice code
Pattern    : event
Statement  : When an owner deactivates a Unit e-invoice code, the system shall set its isActiveFl to false and return no e-invoice unit for that unit of measure.
Traces     : US-CAT-021
Entities   : ENT-CAT-012
Rationale  : A wrong mapping is withdrawn before a correct one is added.
Source     : US-CAT-021
Priority   : HIGH

#### AC-CAT-124 — [REQ-CAT-122]
Given : the active Unit e-invoice code for BAG
When  : the owner deactivates it
Then  : isActiveFl is false and reads of BAG return an empty e-invoice unit

### REQ-CAT-123 — Incomplete or malformed e-invoice code
Pattern    : unwanted
Statement  : If an Item or Variant carries only one of e-invoice code type and code, a GS1 code that is not 8 to 14 digits, or an EGS code that does not start with "EG-", then the system shall reject the save with RULE-CAT-042.
Traces     : US-CAT-021
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Codes the tax authority would refuse are caught at entry.
Source     : US-CAT-021; PRD P-4
Priority   : HIGH

#### AC-CAT-125 — [REQ-CAT-123]
Given : an Item
When  : the owner saves einvoiceCodeTypeCode EGS with einvoiceCode «123»
Then  : nothing is saved and the response shows ar: «أدخل نوع رمز الفاتورة الإلكترونية والرمز معًا: GS1 من 8 إلى 14 رقمًا، وEGS يبدأ بـ EG- ولا يتجاوز 100 حرف.» · en: "Enter the e-invoice code type and code together: GS1 is 8 to 14 digits, EGS starts with EG- and has at most 100 characters."

### REQ-CAT-124 — Second e-invoice unit for one unit
Pattern    : unwanted
Statement  : If an owner creates a Unit e-invoice code for a unit of measure that already has an active one, then the system shall reject the request with RULE-CAT-043.
Traces     : US-CAT-021
Entities   : ENT-CAT-012
Rationale  : One unit maps to one e-invoice unit.
Source     : US-CAT-021
Priority   : HIGH

#### AC-CAT-126 — [REQ-CAT-124]
Given : the active Unit e-invoice code PIECE → EA
When  : the owner creates PIECE → H87
Then  : nothing is created and the response shows ar: «لهذه الوحدة رمز فاتورة إلكترونية نشط بالفعل. عدّله بدلًا من إضافة رمز جديد.» · en: "This unit already has an active e-invoice unit. Change it instead of adding a new one."

### REQ-CAT-125 — Catalogue search for the global search
Pattern    : event
Statement  : When a search term of at least 2 characters is submitted to the catalogue search, the system shall return at most 20 active Items whose nameAr, nameEn, code or active barcode contains the term, with exact code or barcode matches first.
Traces     : US-CAT-022
Entities   : ENT-CAT-001, ENT-CAT-009
Rationale  : Items are reached from the one global search.
Source     : US-CAT-022; POL-CAT-009; KB:raw-idea.md §6.6; ADR-CAT-043
Priority   : HIGH

#### AC-CAT-127 — [REQ-CAT-125]
Given : 25 active Items whose nameAr contains «شاحن», one of them with code ITM-000077
When  : a seller/cashier searches «شاحن», then «ITM-000077»
Then  : the first search returns 20 Items, and the second returns ITM-000077 in position 1

### REQ-CAT-126 — Search term too short
Pattern    : unwanted
Statement  : If the search term has fewer than 2 characters, then the system shall return no results with RULE-CAT-046.
Traces     : US-CAT-022
Entities   : ENT-CAT-001
Rationale  : One-letter searches would return the whole catalogue.
Source     : US-CAT-022
Priority   : HIGH

#### AC-CAT-128 — [REQ-CAT-126]
Given : the global search
When  : a seller/cashier submits «ش»
Then  : 0 results are returned and the response shows ar: «اكتب حرفين على الأقل للبحث.» · en: "Type at least 2 characters to search."

### REQ-CAT-127 — Search without permission
Pattern    : unwanted
Statement  : If the user who submits the catalogue search lacks the VIEW permission of page CAT_ITEMS, then the system shall return no Item results.
Traces     : US-CAT-022
Entities   : ENT-CAT-001
Rationale  : The global search respects catalogue access through the platform authorisation layer.
Source     : US-CAT-022; profile security_model
Priority   : HIGH

#### AC-CAT-129 — [REQ-CAT-127]
Given : a custom role without VIEW on CAT_ITEMS
When  : its user searches «شاحن» in the global search
Then  : the catalogue contributes 0 results

### REQ-CAT-128 — Item unit used by active barcodes
Pattern    : unwanted
Statement  : If an owner deactivates an Item unit while active barcodes of the Item's Variants are bound to that unit, then the system shall reject the deactivation with RULE-CAT-051.
Traces     : US-CAT-016
Entities   : ENT-CAT-008, ENT-CAT-009
Rationale  : A barcode never resolves to a unit the Item no longer offers.
Source     : US-CAT-016; US-CAT-015; ADR-CAT-060
Priority   : —

#### AC-CAT-130 — [REQ-CAT-128]
Given : an Item with the active Item unit CARTON and 2 active barcodes bound to unit CARTON
When  : the owner deactivates the Item unit CARTON
Then  : CARTON stays active and the response shows ar: «هذه الوحدة مستخدمة في 2 باركود نشط. أوقف هذه الباركودات أولًا.» · en: "This unit is used by 2 active barcodes. Deactivate those barcodes first."

### REQ-CAT-129 — Generation omits a used attribute
Pattern    : unwanted
Statement  : If a Variant generation request for an Item omits an attribute that the Item's active Variants already carry, or selects no value for it, then the system shall reject the whole request with RULE-CAT-052.
Traces     : US-CAT-011
Entities   : ENT-CAT-002, ENT-CAT-005
Rationale  : Every active Variant of an Item carries one value of each attribute the Item uses (RULE-CAT-013).
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-131 — [REQ-CAT-129]
Given : a STANDARD Item whose 2 active Variants carry the attributes SIZE (values S, M) and COLOUR (value «أحمر»)
When  : the owner generates Variants with selections SIZE [L] only
Then  : 0 Variants are created and the response shows ar: «اختر قيمة واحدة على الأقل لكل خاصية يستخدمها الصنف بالفعل.» · en: "Select at least one value for every attribute the item already uses."

### REQ-CAT-130 — Reactivation of an Item with no active Variant
Pattern    : unwanted
Statement  : If an owner reactivates an Item that has no active Variant, then the system shall reject the reactivation with RULE-CAT-050 and keep the Item inactive.
Traces     : US-CAT-001
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Every transition into ACTIVE re-establishes the Item invariants of A7; this REQ holds invariant 1. The other three refusals on reactivation are REQ-CAT-142, REQ-CAT-143 and REQ-CAT-144 (split from this REQ at the seventh gate-analysis revise).
Source     : US-CAT-001; ADR-CAT-059
Priority   : HIGH

#### AC-CAT-132 — [REQ-CAT-130]
Given : an inactive Item whose only Variant was deactivated while the Item was inactive, so it has 0 active Variants
When  : the owner reactivates the Item
Then  : isActiveFl stays false and the response shows ar: «لا يمكن إعادة تفعيل صنف بلا متغيّر نشط. أنشئ متغيّرًا له أو أنشئ صنفًا جديدًا.» · en: "An item with no active variant cannot be reactivated. Generate a variant for it, or create a new item."

### REQ-CAT-131 — Operation without permission
Pattern    : unwanted
Statement  : If a user calls a catalogue operation without the permission of its page and action in the SRS B4 matrix, then the system shall refuse the request with CAT-403-FORBIDDEN and change no record, except for the catalogue search, which REQ-CAT-127 answers with no results.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : Only the roles that SRS B4 grants change the catalogue; the screens hide what the user may not do.
Source     : US-CAT-001; SRS B4; profile security_model; ADR-CAT-046
Priority   : HIGH

#### AC-CAT-137 — [REQ-CAT-131]
Given : a Seller/cashier who holds only PERM_CAT_ITEMS_VIEW, and an active Item with nameEn "Charger"
When  : the user creates an Item, sets the Item's nameEn to "Charger 25W", and deactivates the Item, in 3 requests
Then  : each of the 3 requests is refused with code CAT-403-FORBIDDEN, 0 Items are created, and the Item keeps nameEn "Charger" and isActiveFl true

#### AC-CAT-138 — [REQ-CAT-131]
Given : a Seller/cashier who holds only PERM_CAT_ITEMS_VIEW
When  : the user opens the Items entry of an existing Item
Then  : 0 Save actions and 0 Deactivate actions are shown, and every header field is read-only

### REQ-CAT-132 — Record of another tenant
Pattern    : unwanted
Statement  : If a request names a catalogue record of another tenant by its identifier, then the system shall answer with CAT-404-NOT-FOUND and change no record.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : One tenant's catalogue is invisible to every other tenant (contract §0, tenancy).
Source     : US-CAT-001; contract §0; given erp-core tenancy
Priority   : HIGH

#### AC-CAT-139 — [REQ-CAT-132]
Given : an Item with nameEn "Charger" in tenant A, and an Owner of tenant B holding every PERM_CAT_* permission
When  : the tenant B Owner reads that Item by its itemPk, then submits an update of its nameEn
Then  : both requests are answered with code CAT-404-NOT-FOUND, and the Item in tenant A keeps nameEn "Charger"

### REQ-CAT-133 — Stale version
Pattern    : unwanted
Statement  : If an update request carries a version older than the stored version of the record, then the system shall reject it with CAT-409-STALE-VERSION and keep the stored values.
Traces     : US-CAT-002
Entities   : ENT-CAT-001
Rationale  : A change made by one user is never silently overwritten by another.
Source     : US-CAT-002; ADR-CAT-044
Priority   : HIGH

#### AC-CAT-140 — [REQ-CAT-133]
Given : two owners read the same Item at the same version, and the first saves nameEn "Charger 25W"
When  : the second submits nameEn "Charger 30W" with the version read before the first save
Then  : the request is refused with code CAT-409-STALE-VERSION, and the Item keeps nameEn "Charger 25W"

### REQ-CAT-134 — Catalogue seeds of a new tenant
Pattern    : event
Statement  : When a tenant is provisioned, the system shall give it the CAT lookup values of A6, the number series CAT_ITEM, CAT_VARIANT and CAT_INTERNAL_BARCODE with their counters at 1, and the 8 seeded Unit e-invoice codes of D-12.
Traces     : US-CAT-009, US-CAT-021
Entities   : ENT-CAT-001, ENT-CAT-012
Rationale  : The first Item of a new tenant receives its codes, and its units carry their e-invoice units, without a setup step.
Source     : US-CAT-009; US-CAT-021; D-11; D-12; ADR-CAT-058
Priority   : HIGH

#### AC-CAT-141 — [REQ-CAT-134]
Given : a tenant provisioned from PLATFORM after CAT was installed, with no catalogue record yet
When  : its owner opens the catalogue settings, then creates an Item with nameAr «كوب زجاج»
Then  : the settings list 8 active Unit e-invoice codes, and the Item is created with code ITM-000001 and a default Variant with code VAR-000001

### REQ-CAT-135 — Change under an inactive parent
Pattern    : unwanted
Statement  : If a request changes an inactive Item other than by changing only its categoryFk, adds or updates an Item unit or adds or updates an image of an inactive Item, or adds a barcode or sets the e-invoice override of a Variant that is inactive or belongs to an inactive Item, then the system shall reject the request with RULE-CAT-053.
Traces     : US-CAT-001, US-CAT-016
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010
Rationale  : An inactive record has left every channel; a write under it creates data no channel can use and that the ACTIVE-state invariants (A7) never checked. Deactivating children stays allowed, so an inactive Item can always be cleaned up (ADR-CAT-063). The three repair operations that stay allowed on an inactive Item are REQ-CAT-145, REQ-CAT-146 and REQ-CAT-147 (split from this REQ at the seventh gate-analysis revise — ADR-CAT-068).
Source     : US-CAT-001; US-CAT-016; ADR-CAT-063; ADR-CAT-068
Priority   : HIGH

#### AC-CAT-144 — [REQ-CAT-135]
Given : an inactive Item with base unit PIECE and no Item unit CARTON
When  : the owner adds the Item unit CARTON with conversionFactor 12
Then  : no Item unit is created and the response shows ar: «هذا الصنف أو المتغيّر غير نشط ولا يمكن تعديله. أعد تفعيل الصنف أو اختر متغيّرًا نشطًا.» · en: "This item or variant is inactive and cannot be changed. Reactivate the item, or choose an active variant."

### REQ-CAT-136 — Reactivate a Category
Pattern    : event
Statement  : When an owner reactivates an inactive Category whose parent is empty or active and whose nameAr no active Category under the same parent carries, the system shall set its isActiveFl to true and offer it again for selection.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : A mistaken deactivation is undone without losing the Category or its code, which stays unique among active and inactive Categories (ADR-CAT-069).
Source     : US-CAT-013; ADR-CAT-069
Priority   : —

#### AC-CAT-157 — [REQ-CAT-136]
Given : an inactive top-level Category with code PHONES and nameAr «هواتف», and no active top-level Category named «هواتف»
When  : the owner reactivates it
Then  : isActiveFl is true, its code stays PHONES, and the Item entry's Category list offers it

#### AC-CAT-158 — [REQ-CAT-136]
Given : an inactive Category PHONES whose parent «إلكترونيات» is inactive
When  : the owner reactivates PHONES
Then  : PHONES stays inactive and the response shows ar: «الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات.» · en: "The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels."

### REQ-CAT-137 — Reactivate a Variant attribute
Pattern    : event
Statement  : When an owner reactivates an inactive Variant attribute whose nameAr no active Variant attribute carries, the system shall set its isActiveFl to true and offer it again to Variant generation.
Traces     : US-CAT-011
Entities   : ENT-CAT-003
Rationale  : A mistaken deactivation is undone without losing the attribute or its code (ADR-CAT-069).
Source     : US-CAT-011; ADR-CAT-069
Priority   : HIGH

#### AC-CAT-159 — [REQ-CAT-137]
Given : the inactive Variant attribute SIZE «المقاس», and no active attribute named «المقاس»
When  : the owner reactivates SIZE
Then  : isActiveFl is true, its code stays SIZE, and the Variant generation list offers SIZE with its active values

### REQ-CAT-138 — Reactivate a Variant attribute value
Pattern    : event
Statement  : When an owner reactivates an inactive value of an active Variant attribute whose nameAr no active value of that attribute carries, the system shall set its isActiveFl to true and offer it again to Variant generation.
Traces     : US-CAT-011
Entities   : ENT-CAT-004
Rationale  : A mistaken deactivation is undone without losing the value or its code (ADR-CAT-069).
Source     : US-CAT-011; ADR-CAT-069
Priority   : HIGH

#### AC-CAT-160 — [REQ-CAT-138]
Given : the active attribute SIZE with the inactive value XS «صغير جدًا», and no active value of SIZE named «صغير جدًا»
When  : the owner reactivates XS
Then  : isActiveFl is true, its code stays XS, and the Variant generation list for SIZE offers XS

### REQ-CAT-139 — Import past its time limit
Pattern    : unwanted
Statement  : If an item import does not complete within 120 seconds, then the system shall roll the whole import back, store no Item, Variant or barcode of it, publish no catalogue-change event for it, and answer with RULE-CAT-049.
Traces     : US-CAT-010
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Rationale  : An import is all or nothing, so a slow import never leaves half a catalogue behind; the codes already drawn stay as gaps in their series, and the owner imports the file again, split into smaller files. The target for a file of 5,000 valid rows is 60 seconds, a budget measured on the reference deployment by the test of this REQ (ADR-CAT-071, ADR-CAT-074); the 120-second limit is the guarantee.
Source     : US-CAT-010; ADR-CAT-071; ADR-CAT-074; RULE-CAT-049
Priority   : HIGH

#### AC-CAT-161 — [REQ-CAT-139]
Given : an import file of 5,000 valid rows, in a tenant with 0 Items, whose processing is held past 120 seconds
When  : the owner submits it
Then  : 0 Items, 0 Variants, 0 barcodes and 0 catalogue-change events are stored, and the response shows ar: «تعذّر حفظ التغيير ولم يُرسل أي إشعار بالتغيير. أعد المحاولة.» · en: "The change was not saved and no change notice was sent. Try again."

### REQ-CAT-140 — Default Variant moves on its deactivation
Pattern    : event
Statement  : When the default Variant of an Item is deactivated while another Variant of the Item is active, the system shall move isDefaultFl, in the same transaction, to the remaining active Variant with the lowest code.
Traces     : US-CAT-007, US-CAT-011
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Consumers always find one active default Variant on an active Item to fall back to (CON-CAT-002, A7 invariant 5). Split from REQ-CAT-025 at the seventh gate-analysis revise.
Source     : US-CAT-007; ADR-CAT-072
Priority   : HIGH

#### AC-CAT-163 — [REQ-CAT-140]
Given : an active Item with 3 active Variants VAR-000010 (isDefaultFl true), VAR-000011 and VAR-000012, none of them a Bundle component
When  : the owner deactivates VAR-000010
Then  : VAR-000011 has isDefaultFl true, VAR-000010 has isDefaultFl false, and the Item has exactly 1 Variant with isDefaultFl true

### REQ-CAT-141 — Default Variant moves on generation for an Item with no active Variant
Pattern    : event
Statement  : When Variants are generated for an Item with no active Variant, the system shall give isDefaultFl to the first Variant created and clear it on the inactive former default.
Traces     : US-CAT-007, US-CAT-011
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : An Item repaired by generation returns to ACTIVE with an active default Variant (A7 invariant 5). Split from REQ-CAT-025 at the seventh gate-analysis revise.
Source     : US-CAT-007; ADR-CAT-072
Priority   : HIGH

#### AC-CAT-164 — [REQ-CAT-141]
Given : `variants` is on, an inactive STANDARD Item whose only Variant V (isDefaultFl true) was deactivated, and the active attribute SIZE with the active value M
When  : the owner generates Variants for the Item with selections SIZE [M], then reactivates the Item
Then  : the new Variant carrying M has isDefaultFl true, V has isDefaultFl false, and the reactivated Item has exactly 1 Variant with isDefaultFl true, which is active

### REQ-CAT-142 — Reactivation of an Item whose Category is inactive
Pattern    : unwanted
Statement  : If an owner reactivates an Item whose categoryFk points to an inactive Category, then the system shall reject the reactivation with RULE-CAT-026 and keep the Item inactive.
Traces     : US-CAT-001, US-CAT-013
Entities   : ENT-CAT-001, ENT-CAT-006
Rationale  : A7 invariant 2. Split from REQ-CAT-130 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-059
Priority   : HIGH

#### AC-CAT-133 — [REQ-CAT-142]
Given : an inactive Item with categoryFk OLD, where the Category OLD was deactivated after the Item
When  : the owner reactivates the Item
Then  : isActiveFl stays false and the response shows ar: «الفئة المختارة غير نشطة. اختر فئة نشطة.» · en: "The selected category is inactive. Choose an active category."

### REQ-CAT-143 — Reactivation of a Bundle with an invalid component
Pattern    : unwanted
Statement  : If an owner reactivates a Bundle holding a component line that RULE-CAT-019 refuses, then the system shall reject the reactivation with RULE-CAT-019 and keep the Bundle inactive.
Traces     : US-CAT-001, US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-007
Rationale  : A7 invariant 3. Split from REQ-CAT-130 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-059
Priority   : HIGH

#### AC-CAT-134 — [REQ-CAT-143]
Given : an inactive Bundle with 2 component lines, one of whose component Variants was deactivated after the Bundle was deactivated
When  : the owner reactivates the Bundle
Then  : isActiveFl stays false and the response shows ar: «المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها.» · en: "A component must be an active variant of an item that is not a bundle, listed once per bundle."

### REQ-CAT-144 — Reactivation of an Item whose name another active Item carries
Pattern    : unwanted
Statement  : If an owner reactivates an Item whose normalised nameAr another active Item of the tenant carries, then the system shall reject the reactivation with RULE-CAT-003 and keep the Item inactive.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : A7 invariant 4; the repair is to rename or deactivate the other active Item, whose code the message names. Split from REQ-CAT-130 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-059; ADR-CAT-062
Priority   : HIGH

#### AC-CAT-162 — [REQ-CAT-144]
Given : an inactive Item named «كوب زجاج» with 1 active Variant and no Category, and another active Item named «كوب زجاج» with code ITM-000031, created while the first was inactive
When  : the owner reactivates the first Item
Then  : the first Item stays inactive and the response shows ar: «يوجد صنف نشط بالاسم نفسه: ITM-000031. افتح الصنف الموجود أو غيّر الاسم.» · en: "An active item already has this name: ITM-000031. Open the existing item or change the name."

### REQ-CAT-145 — Variant generation allowed on an inactive Item
Pattern    : state
Statement  : While an Item is inactive, the system shall accept a Variant generation for it under RULE-CAT-008, RULE-CAT-012, RULE-CAT-013, RULE-CAT-014, RULE-CAT-020 and RULE-CAT-052, and leave the Item inactive.
Traces     : US-CAT-001, US-CAT-011
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Rationale  : Generation is the repair of REQ-CAT-130, so an Item with no active Variant can return to ACTIVE (ADR-CAT-068). Split from REQ-CAT-135 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-068
Priority   : HIGH

#### AC-CAT-154 — [REQ-CAT-145]
Given : `variants` is on, and an inactive STANDARD Item with 0 active Variants, 0 Variant attributes in use and the active attribute SIZE with the active value M
When  : the owner generates Variants for the Item with selections SIZE [M], then reactivates the Item
Then  : the generation creates 1 active Variant carrying M, and the reactivation sets isActiveFl to true

### REQ-CAT-146 — Category-only change allowed on an inactive Item
Pattern    : state
Statement  : While an Item is inactive, the system shall accept an update whose only change is categoryFk under RULE-CAT-026, and leave the Item inactive.
Traces     : US-CAT-001, US-CAT-013
Entities   : ENT-CAT-001, ENT-CAT-006
Rationale  : The categoryFk-only change is the repair of REQ-CAT-142 (ADR-CAT-068); any other change stays refused by REQ-CAT-135. Split from REQ-CAT-135 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-068
Priority   : HIGH

#### AC-CAT-155 — [REQ-CAT-146]
Given : an inactive Item with nameEn "Glass cup" and categoryFk OLD, where the Category OLD is inactive, and an active Category NEW
When  : the owner saves the Item with categoryFk NEW and every other field unchanged, then saves it with nameEn "Glass cup 2", then reactivates it
Then  : the first save sets categoryFk to NEW, the second save is refused with code CAT-422-PARENT-INACTIVE and nameEn stays "Glass cup", and the reactivation sets isActiveFl to true

### REQ-CAT-147 — Bundle component replacement allowed on an inactive Bundle
Pattern    : state
Statement  : While a Bundle Item is inactive, the system shall accept the replacement of its component lines under RULE-CAT-008, RULE-CAT-018 and RULE-CAT-019, and leave the Bundle inactive.
Traces     : US-CAT-001, US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-007
Rationale  : Replacing the components is the repair of REQ-CAT-143 (ADR-CAT-068). Split from REQ-CAT-135 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-068
Priority   : HIGH

#### AC-CAT-156 — [REQ-CAT-147]
Given : `bundles` is on, an inactive Bundle with the component lines P × 1 and C × 1 where the Variant P was deactivated, and an active Variant Q of an active STANDARD Item
When  : the owner replaces the Bundle's component lines with C × 1 and Q × 1, then reactivates the Bundle
Then  : the Bundle holds exactly 2 active lines (C × 1 and Q × 1), and the reactivation sets isActiveFl to true

## A5 — Business rules

### RULE-CAT-001 — Arabic name required
Scope      : ENT-CAT-001, ENT-CAT-003, ENT-CAT-004, ENT-CAT-006, ENT-CAT-010
Trigger    : on create · on update · on import row
Statement  : The system shall require a nameAr of 1 to 200 characters that contains at least one Arabic letter when a record of scope is saved. An Arabic letter is a character in U+0621 to U+064A; the client, the service and the database check use this one set.
Message    : ar: أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل. · en: Enter the Arabic name (up to 200 characters) with at least one Arabic letter.
Traces     : REQ-CAT-027, REQ-CAT-030, REQ-CAT-042, REQ-CAT-045, REQ-CAT-064, REQ-CAT-105
Data source: ENT-CAT-001.nameAr, ENT-CAT-003.nameAr, ENT-CAT-004.nameAr, ENT-CAT-006.nameAr, ENT-CAT-010.nameAr
Source     : PRD P-3; D-07
Test-Hint  : Latin-only text and 201 characters both fail; «سامسونج A55» passes.

### RULE-CAT-002 — English name length
Scope      : ENT-CAT-001, ENT-CAT-003, ENT-CAT-004, ENT-CAT-006, ENT-CAT-010
Trigger    : on create · on update · on import row
Statement  : The system shall prevent saving a nameEn longer than 200 characters.
Message    : ar: الاسم بالإنجليزية لا يتجاوز 200 حرف. · en: The English name can have at most 200 characters.
Traces     : REQ-CAT-031
Data source: ENT-CAT-001.nameEn, ENT-CAT-003.nameEn, ENT-CAT-004.nameEn, ENT-CAT-006.nameEn, ENT-CAT-010.nameEn
Source     : PRD P-3

### RULE-CAT-003 — One active Item per name
Scope      : ENT-CAT-001
Trigger    : on create · on update · on reactivation · on import row
Statement  : The system shall prevent saving or reactivating an Item when its nameAr, after Arabic normalisation (tashkeel and tatweel removed; أ إ آ ٱ read as ا, ة as ه, ى as ي; Latin letters lower-cased; leading, trailing and repeated spaces removed — ADR-CAT-062), equals the normalised nameAr of another active Item of the tenant or of an earlier valid row of the same import file.
Message    : ar: يوجد صنف نشط بالاسم نفسه: {code}. افتح الصنف الموجود أو غيّر الاسم. · en: An active item already has this name: {code}. Open the existing item or change the name.
Traces     : REQ-CAT-007, REQ-CAT-041, REQ-CAT-144
Data source: ENT-CAT-001.nameAr, ENT-CAT-001.isActiveFl, ENT-CAT-001.code ({code} = the code of the other active Item; for an earlier row of the same import file, the code the import gave to that row's Item)
Source     : D-13; US-CAT-001; ADR-CAT-062
Test-Hint  : «شنطة سفر» and «شنطه سفر» are one name; «أحمر» and «احمر» are one name.

### RULE-CAT-004 — Codes are system-assigned
Scope      : ENT-CAT-001, ENT-CAT-002
Trigger    : on create · on update
Statement  : The system shall assign the code of an Item from series CAT_ITEM and of a Variant from series CAT_VARIANT on first save, and prevent any request from supplying or changing a code (NUMBERING: document numbers come from the core sequence module).
Message    : ar: يُعيَّن الرمز تلقائيًا ولا يمكن إدخاله أو تعديله. · en: The code is assigned automatically and cannot be entered or changed.
Traces     : REQ-CAT-032, REQ-CAT-033, REQ-CAT-034
Data source: ENT-CAT-001.code, ENT-CAT-002.code
Source     : US-CAT-009; profile NUMBERING rule; D-11

### RULE-CAT-005 — No record without a code
Scope      : ENT-CAT-001, ENT-CAT-002
Trigger    : on create
Statement  : The system shall prevent storing an Item or a Variant when the core number series returns no code.
Message    : ar: تعذّر إصدار رمز جديد الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات. · en: A new code could not be issued, and nothing was saved. Try again in a moment.
Traces     : REQ-CAT-035
Data source: ENT-CAT-001.code, ENT-CAT-002.code
Source     : US-CAT-009

### RULE-CAT-006 — Lookup-backed fields hold active values
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-008, ENT-CAT-009, ENT-CAT-011, ENT-CAT-012
Trigger    : on create · on update · on import row
Statement  : The system shall require every changed lookup-backed field to hold an active value of its lookup key (A6), and shall keep an unchanged value that was deactivated later.
Message    : ar: القيمة المختارة غير متاحة. اختر قيمة نشطة من القائمة. · en: The selected value is not available. Choose an active value from the list.
Traces     : REQ-CAT-073, REQ-CAT-074, REQ-CAT-075, REQ-CAT-076
Data source: ENT-CAT-001.brandCode, ENT-CAT-001.baseUomCode, ENT-CAT-001.taxClassCode, ENT-CAT-001.einvoiceCodeTypeCode, ENT-CAT-002.einvoiceCodeTypeCode, ENT-CAT-008.uomCode, ENT-CAT-009.uomCode, ENT-CAT-009.barcodeTypeCode, ENT-CAT-011.embeddedValueTypeCode, ENT-CAT-012.key, ENT-CAT-012.einvoiceUnitCode
Source     : profile LOOKUPS rule; D-02

### RULE-CAT-007 — Base unit fixed after first save
Scope      : ENT-CAT-001
Trigger    : on update
Statement  : The system shall prevent changing baseUomCode of a saved Item.
Message    : ar: لا يمكن تغيير وحدة القياس الأساسية بعد حفظ الصنف. أضف وحدة أخرى للصنف بدلًا من ذلك. · en: The base unit cannot be changed after the item is saved. Add another unit to the item instead.
Traces     : REQ-CAT-082
Data source: ENT-CAT-001.baseUomCode
Source     : D-14

### RULE-CAT-008 — Flag-bound operation refused while its flag is off
Scope      : ENT-CAT-001, ENT-CAT-003, ENT-CAT-004, ENT-CAT-005, ENT-CAT-007
Trigger    : on create of an Item of kind BUNDLE · on Variant generation · on Bundle component save · on create or update of a Variant attribute · on create or update of a Variant attribute value
Statement  : The system shall prevent creating an Item of kind BUNDLE and saving Bundle components while the `bundles` flag is off, and generating Variants and creating or updating Variant attributes and Variant attribute values while the `variants` flag is off, reading the flag through the FeatureFlags API. Reading, deactivating and reactivating records that already exist stay allowed while a flag is off, so data that depends on the flag can be cleaned up.
Message    : ar: هذه الوظيفة غير مفعّلة لنشاطك. فعّل مفتاح القدرة «{flag}» من الإعدادات لاستخدامها. · en: This function is not switched on for your business. Turn on the «{flag}» capability flag in settings to use it.
Traces     : REQ-CAT-014, REQ-CAT-015, REQ-CAT-016
Data source: ENT-CAT-001.itemKindCode, ENT-CAT-005.variantAttributeValueFk, ENT-CAT-007.componentVariantFk
Source     : POL-CAT-003; DP §5 G1, G2; ADR-CAT-041
Test-Hint  : Test each flag in both states (G2).

### RULE-CAT-009 — Unknown flag value refuses the operation
Scope      : ENT-CAT-001, ENT-CAT-003, ENT-CAT-004, ENT-CAT-005, ENT-CAT-007
Trigger    : on any operation RULE-CAT-008 refuses while its flag is off
Statement  : The system shall prevent an operation that RULE-CAT-008 binds to a flag when the FeatureFlags read of that flag returns no value.
Message    : ar: تعذّر التحقق من إعدادات القدرات الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات. · en: Capability settings could not be checked, and nothing was saved. Try again in a moment.
Traces     : REQ-CAT-013
Data source: ENT-CAT-001.itemKindCode, ENT-CAT-005.variantAttributeValueFk
Source     : ADR-CAT-041

### RULE-CAT-010 — Variants cannot be turned off while in use
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Trigger    : on turn-off check of `variants`
Statement  : The system shall answer "not allowed" when an active Variant attribute assignment exists on an active Variant of an active Item.
Message    : ar: لا يمكن إيقاف المتغيّرات لأن {count} صنفًا يستخدمها. أوقف متغيّرات هذه الأصناف أو أبقِ المتغيّرات مفعّلة. · en: Variants cannot be turned off because {count} items use them. Deactivate the variants of these items or keep variants on.
Traces     : REQ-CAT-021, REQ-CAT-022
Data source: ENT-CAT-005.variantFk, ENT-CAT-005.isActiveFl, ENT-CAT-002.isActiveFl, ENT-CAT-002.itemFk, ENT-CAT-001.isActiveFl
Source     : POL-CAT-005; DP §5 G3

### RULE-CAT-011 — Bundles cannot be turned off while in use
Scope      : ENT-CAT-001
Trigger    : on turn-off check of `bundles`
Statement  : The system shall answer "not allowed" when an active Item of kind BUNDLE exists.
Message    : ar: لا يمكن إيقاف الحزم لوجود {count} حزمة نشطة. أوقف هذه الحزم أو أبقِ الحزم مفعّلة. · en: Bundles cannot be turned off because {count} active bundles exist. Deactivate these bundles or keep bundles on.
Traces     : REQ-CAT-021, REQ-CAT-023
Data source: ENT-CAT-001.itemKindCode, ENT-CAT-001.isActiveFl
Source     : POL-CAT-005; DP §5 G3

### RULE-CAT-012 — Existing Variants keep their identity
Scope      : ENT-CAT-002, ENT-CAT-005, ENT-CAT-009
Trigger    : on Variant generation
Statement  : The system shall assign to the Item's existing active Variants the first selected value of every attribute they do not yet carry, keeping their variantPk, code and barcodes, before it creates Variants for the remaining combinations.
Message    : ar: احتفظت المتغيّرات الحالية بأرقامها وباركوداتها وأُسندت إليها القيم الأولى المختارة. · en: Existing variants kept their codes and barcodes and took the first selected values.
Traces     : REQ-CAT-018, REQ-CAT-019
Data source: ENT-CAT-002.isDefaultFl, ENT-CAT-002.itemFk, ENT-CAT-005.variantFk, ENT-CAT-005.variantAttributeFk, ENT-CAT-009.variantFk
Source     : POL-CAT-004; KB:raw-idea.md §6.8

### RULE-CAT-013 — One value per attribute and unique combinations
Scope      : ENT-CAT-002, ENT-CAT-005
Trigger    : on Variant generation
Statement  : The system shall require each active Variant of an Item to carry exactly one value of each attribute used by the Item, and prevent two active Variants of the same Item from carrying the same combination. A generation whose selections omit an attribute the Item already uses, or select no value for it, is refused under RULE-CAT-052 before any combination is checked.
Message    : ar: هذه التركيبة من القيم موجودة في متغيّر آخر لهذا الصنف. · en: This combination of values already exists on another variant of this item.
Traces     : REQ-CAT-049, REQ-CAT-051
Data source: ENT-CAT-005.variantFk, ENT-CAT-005.variantAttributeFk, ENT-CAT-005.variantAttributeValueFk, ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl
Source     : US-CAT-011

### RULE-CAT-014 — Variant limits per Item
Scope      : ENT-CAT-002, ENT-CAT-005
Trigger    : on Variant generation
Statement  : The system shall prevent a generation that would give an Item more than 3 Variant attributes or more than 300 active Variants.
Message    : ar: الحد الأقصى 3 خصائص و300 متغيّر نشط للصنف الواحد. · en: An item can have at most 3 attributes and 300 active variants.
Traces     : REQ-CAT-052
Data source: ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl, ENT-CAT-005.variantAttributeFk
Source     : D-10

### RULE-CAT-015 — Last active Variant stays active
Scope      : ENT-CAT-001, ENT-CAT-002
Trigger    : on Variant deactivation
Statement  : The system shall prevent deactivating the last active Variant of an active Item.
Message    : ar: لا يمكن إيقاف آخر متغيّر نشط لصنف نشط. أوقف الصنف نفسه بدلًا من ذلك. · en: The last active variant of an active item cannot be deactivated. Deactivate the item instead.
Traces     : REQ-CAT-053
Data source: ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl, ENT-CAT-001.isActiveFl
Source     : DP §8 P-04; D-01

### RULE-CAT-016 — Attribute and value codes and names unique
Scope      : ENT-CAT-003, ENT-CAT-004
Trigger    : on create · on update · on reactivation
Statement  : The system shall require a code of 1 to 30 Latin letters, digits or hyphens, unique per tenant for a Variant attribute and per attribute for a value, and a nameAr unique among the active records of the same scope; a record being reactivated keeps its code and must not carry the nameAr of another active record of its scope.
Message    : ar: الرمز أو الاسم مستخدم بالفعل. استخدم رمزًا (حتى 30 حرفًا لاتينيًا أو رقمًا أو شرطة) واسمًا مختلفين. · en: The code or name is already used. Use a different code (up to 30 Latin letters, digits or hyphens) and name.
Traces     : REQ-CAT-042, REQ-CAT-045, REQ-CAT-055, REQ-CAT-137, REQ-CAT-138
Data source: ENT-CAT-003.code, ENT-CAT-003.nameAr, ENT-CAT-003.isActiveFl, ENT-CAT-004.variantAttributeFk, ENT-CAT-004.code, ENT-CAT-004.nameAr, ENT-CAT-004.isActiveFl
Source     : US-CAT-011

### RULE-CAT-017 — Attribute or value in use stays active
Scope      : ENT-CAT-003, ENT-CAT-004
Trigger    : on deactivation
Statement  : The system shall prevent deactivating a Variant attribute or value that an active assignment of an active Variant carries.
Message    : ar: هذه الخاصية أو القيمة مستخدمة في {count} متغيّرًا نشطًا. أوقف هذه المتغيّرات أولًا. · en: This attribute or value is used by {count} active variants. Deactivate those variants first.
Traces     : REQ-CAT-044, REQ-CAT-047, REQ-CAT-054
Data source: ENT-CAT-005.variantAttributeFk, ENT-CAT-005.variantAttributeValueFk, ENT-CAT-005.isActiveFl, ENT-CAT-002.isActiveFl
Source     : US-CAT-011

### RULE-CAT-018 — Bundle has components with quantities
Scope      : ENT-CAT-001, ENT-CAT-007
Trigger    : on Bundle save
Statement  : The system shall require at least one active component line per BUNDLE Item, each with a quantity above zero and at most 3 decimals.
Message    : ar: أضف مكوّنًا واحدًا على الأقل بكمية أكبر من صفر (حتى 3 أرقام عشرية). · en: Add at least one component with a quantity above zero (up to 3 decimals).
Traces     : REQ-CAT-056, REQ-CAT-059
Data source: ENT-CAT-007.bundleItemFk, ENT-CAT-007.quantity, ENT-CAT-007.isActiveFl
Source     : US-CAT-012; D-04

### RULE-CAT-019 — Valid Bundle components
Scope      : ENT-CAT-002, ENT-CAT-007
Trigger    : on Bundle save · on component line change · on reactivation of the Bundle
Statement  : The system shall require each component Variant to be active, to belong to an active STANDARD Item, and to appear once among the active lines of the Bundle.
Message    : ar: المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها. · en: A component must be an active variant of an item that is not a bundle, listed once per bundle.
Traces     : REQ-CAT-057, REQ-CAT-060, REQ-CAT-143, REQ-CAT-147
Data source: ENT-CAT-007.componentVariantFk, ENT-CAT-007.bundleItemFk, ENT-CAT-007.isActiveFl, ENT-CAT-002.isActiveFl, ENT-CAT-002.itemFk, ENT-CAT-001.itemKindCode, ENT-CAT-001.isActiveFl
Source     : D-04; ADR-CAT-059

### RULE-CAT-020 — A Bundle has one Variant
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Trigger    : on Variant generation
Statement  : The system shall prevent generating Variants or assigning Variant attribute values for an Item of kind BUNDLE.
Message    : ar: الحزمة لها متغيّر واحد فقط ولا تقبل خصائص المتغيّرات. · en: A bundle has one variant only and takes no variant attributes.
Traces     : REQ-CAT-061
Data source: ENT-CAT-001.itemKindCode, ENT-CAT-002.itemFk
Source     : D-04

### RULE-CAT-021 — Component of an active Bundle stays active
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-007
Trigger    : on Variant deactivation · on Item deactivation
Statement  : The system shall prevent deactivating a Variant, or the Item that owns it, while the Variant is an active component of an active Bundle.
Message    : ar: هذا المتغيّر مكوّن في {count} حزمة نشطة. أزله من هذه الحزم أو أوقفها أولًا. · en: This variant is a component of {count} active bundles. Remove it from those bundles or deactivate them first.
Traces     : REQ-CAT-062
Data source: ENT-CAT-007.componentVariantFk, ENT-CAT-007.bundleItemFk, ENT-CAT-007.isActiveFl, ENT-CAT-001.isActiveFl
Source     : ARCH-5; US-CAT-012

### RULE-CAT-022 — Item kind fixed after first save
Scope      : ENT-CAT-001
Trigger    : on update
Statement  : The system shall prevent changing itemKindCode of a saved Item.
Message    : ar: لا يمكن تغيير نوع الصنف بعد حفظه. أنشئ صنفًا جديدًا بالنوع المطلوب. · en: The item kind cannot be changed after saving. Create a new item of the required kind.
Traces     : REQ-CAT-063
Data source: ENT-CAT-001.itemKindCode
Source     : D-04

### RULE-CAT-023 — Category hierarchy
Scope      : ENT-CAT-006
Trigger    : on create · on update · on reactivation
Statement  : The system shall require the parent Category to be active, to be neither the Category itself nor one of its sub-categories, and the resulting depth to be at most 5 levels; a Category being reactivated must have an empty or active parent.
Message    : ar: الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات. · en: The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels.
Traces     : REQ-CAT-069, REQ-CAT-136
Data source: ENT-CAT-006.parentCategoryFk, ENT-CAT-006.isActiveFl
Source     : D-03

### RULE-CAT-024 — Category code and sibling name unique
Scope      : ENT-CAT-006
Trigger    : on create · on update · on reactivation
Statement  : The system shall require a code of 1 to 30 Latin letters, digits or hyphens unique per tenant, and a nameAr unique among the active Categories of the same parent; a Category being reactivated keeps its code and must not carry the nameAr of another active Category of its parent.
Message    : ar: يوجد رمز الفئة نفسه، أو الاسم نفسه تحت الفئة الأم نفسها. استخدم رمزًا أو اسمًا مختلفًا. · en: The category code already exists, or the name already exists under the same parent. Use a different code or name.
Traces     : REQ-CAT-070, REQ-CAT-136
Data source: ENT-CAT-006.code, ENT-CAT-006.nameAr, ENT-CAT-006.parentCategoryFk, ENT-CAT-006.isActiveFl
Source     : US-CAT-013

### RULE-CAT-025 — Category in use stays active
Scope      : ENT-CAT-006
Trigger    : on deactivation
Statement  : The system shall prevent deactivating a Category that has active sub-categories or active Items.
Message    : ar: لا يمكن إيقاف الفئة لوجود {count} فئة فرعية أو صنف نشط فيها. انقلها أو أوقفها أولًا. · en: The category cannot be deactivated while it holds {count} active sub-categories or items. Move or deactivate them first.
Traces     : REQ-CAT-071
Data source: ENT-CAT-006.parentCategoryFk, ENT-CAT-006.isActiveFl, ENT-CAT-001.categoryFk, ENT-CAT-001.isActiveFl
Source     : ARCH-5; US-CAT-013

### RULE-CAT-026 — Item Category active
Scope      : ENT-CAT-001
Trigger    : on create · on update · on import row · on reactivation
Statement  : The system shall require a changed categoryFk of an Item, and the categoryFk of an Item being reactivated, to be empty or to point to an active Category.
Message    : ar: الفئة المختارة غير نشطة. اختر فئة نشطة. · en: The selected category is inactive. Choose an active category.
Traces     : REQ-CAT-072, REQ-CAT-142, REQ-CAT-146
Data source: ENT-CAT-001.categoryFk, ENT-CAT-006.isActiveFl
Source     : US-CAT-013; ADR-CAT-059

### RULE-CAT-027 — Valid Item unit
Scope      : ENT-CAT-008
Trigger    : on create
Statement  : The system shall require an Item unit's uomCode to differ from the Item's baseUomCode and from the uomCode of its other active Item units, and its conversionFactor to be above zero with at most 6 decimals.
Message    : ar: الوحدة يجب أن تختلف عن الوحدة الأساسية وألا تتكرر، ومعامل التحويل أكبر من صفر (حتى 6 أرقام عشرية). · en: The unit must differ from the base unit and appear once, with a conversion factor above zero (up to 6 decimals).
Traces     : REQ-CAT-077, REQ-CAT-080
Data source: ENT-CAT-008.itemFk, ENT-CAT-008.uomCode, ENT-CAT-008.conversionFactor, ENT-CAT-008.isActiveFl, ENT-CAT-001.baseUomCode
Source     : US-CAT-015

### RULE-CAT-028 — Conversion factor fixed after saving
Scope      : ENT-CAT-008
Trigger    : on update
Statement  : The system shall prevent changing conversionFactor of a saved Item unit.
Message    : ar: لا يمكن تعديل معامل التحويل بعد الحفظ. أوقف هذه الوحدة وأضفها من جديد بالمعامل الصحيح. · en: The conversion factor cannot be changed after saving. Deactivate this unit and add it again with the correct factor.
Traces     : REQ-CAT-081
Data source: ENT-CAT-008.conversionFactor
Source     : D-14

### RULE-CAT-029 — Barcode unique among active barcodes
Scope      : ENT-CAT-009
Trigger    : on create · on import row
Statement  : The system shall prevent saving a barcode whose barcodeValue equals the barcodeValue of an active barcode of the tenant or of an earlier valid row of the same import file, with SCALE barcodes compared only with SCALE barcodes.
Message    : ar: هذا الباركود مستخدم في الصنف {code}. افتح ذلك الصنف أو استخدم باركودًا آخر. · en: This barcode is already used by item {code}. Open that item or use another barcode.
Traces     : REQ-CAT-087
Data source: ENT-CAT-009.barcodeValue, ENT-CAT-009.barcodeTypeCode, ENT-CAT-009.isActiveFl, ENT-CAT-001.code ({code} = the code of the Item that owns the active barcode; for an earlier row of the same import file, the code the import gave to that row's Item)
Source     : US-CAT-016

### RULE-CAT-030 — GTIN format
Scope      : ENT-CAT-009
Trigger    : on create · on import row
Statement  : The system shall require a GTIN barcodeValue of 8, 12, 13 or 14 digits whose last digit is the GS1 check digit.
Message    : ar: باركود GTIN يجب أن يتكون من 8 أو 12 أو 13 أو 14 رقمًا برقم تحقق صحيح. · en: A GTIN barcode must have 8, 12, 13 or 14 digits with a valid check digit.
Traces     : REQ-CAT-088
Data source: ENT-CAT-009.barcodeValue, ENT-CAT-009.barcodeTypeCode
Source     : US-CAT-016; module-registry OI-10

### RULE-CAT-031 — Barcode unit belongs to the Item
Scope      : ENT-CAT-009
Trigger    : on create
Statement  : The system shall require a barcode's uomCode to equal the Item's baseUomCode or the uomCode of an active Item unit of the same Item.
Message    : ar: وحدة الباركود يجب أن تكون الوحدة الأساسية للصنف أو إحدى وحداته النشطة. · en: The barcode unit must be the item's base unit or one of its active units.
Traces     : REQ-CAT-089
Data source: ENT-CAT-009.uomCode, ENT-CAT-009.variantFk, ENT-CAT-008.uomCode, ENT-CAT-008.isActiveFl, ENT-CAT-001.baseUomCode
Source     : US-CAT-016

### RULE-CAT-032 — Barcodes avoid scale prefixes
Scope      : ENT-CAT-009
Trigger    : on create (GTIN, INTERNAL) · on scale label layout create
Statement  : The system shall prevent a GTIN or INTERNAL barcodeValue that begins with the key of an active scale label layout.
Message    : ar: الباركود يبدأ ببادئة ملصق ميزان نشط ({prefix}). استخدم باركودًا آخر أو عدّل إعداد الميزان. · en: The barcode starts with the prefix of an active scale label layout ({prefix}). Use another barcode or change the scale settings.
Traces     : REQ-CAT-091
Data source: ENT-CAT-009.barcodeValue, ENT-CAT-009.barcodeTypeCode, ENT-CAT-011.key, ENT-CAT-011.isActiveFl
Source     : D-05

### RULE-CAT-033 — Scale code length
Scope      : ENT-CAT-009
Trigger    : on create (SCALE)
Statement  : The system shall require a SCALE barcodeValue to consist of digits only, with a length equal to the scaleCodeLength of an active scale label layout.
Message    : ar: رمز الميزان يجب أن يتكون من {length} أرقام، ويلزم وجود إعداد ملصق ميزان نشط. · en: The scale code must have {length} digits, and an active scale label layout is required.
Traces     : REQ-CAT-096, REQ-CAT-099
Data source: ENT-CAT-009.barcodeValue, ENT-CAT-009.barcodeTypeCode, ENT-CAT-011.scaleCodeLength, ENT-CAT-011.isActiveFl
Source     : US-CAT-017; module-registry OI-10

### RULE-CAT-034 — Valid scale label layout
Scope      : ENT-CAT-011
Trigger    : on create · on update
Statement  : The system shall require a key of 02 or 20 to 29 that no other active layout uses, a scaleCodeLength of 4 to 6 and valueDecimals of 0 to 3. The key and the scaleCodeLength are set on create only; an update carries neither (ADR-CAT-064).
Message    : ar: البادئة رقمان (02 أو من 20 إلى 29) غير مستخدمة في إعداد نشط آخر، وطول رمز الميزان من 4 إلى 6، والأرقام العشرية من 0 إلى 3. · en: The prefix is two digits (02 or 20 to 29) not used by another active layout, the scale code length is 4 to 6, and the decimals are 0 to 3.
Traces     : REQ-CAT-092, REQ-CAT-093, REQ-CAT-098
Data source: ENT-CAT-011.key, ENT-CAT-011.scaleCodeLength, ENT-CAT-011.valueDecimals, ENT-CAT-011.isActiveFl
Source     : module-registry OI-10 (GS1 restricted-circulation prefixes)

### RULE-CAT-035 — Scale label decoding
Scope      : ENT-CAT-009, ENT-CAT-011
Trigger    : on barcode lookup of a 13-digit value with an active layout prefix
Statement  : The system shall require a valid EAN-13 check digit and an embedded scale code equal to an active SCALE barcodeValue before returning a Variant.
Message    : ar: تعذّر قراءة ملصق الميزان: رقم التحقق غير صحيح أو رمز الميزان غير معرَّف. اطلب الصنف بالاسم أو أضف رمز الميزان إليه. · en: The scale label could not be read: the check digit is wrong or the scale code is not defined. Find the item by name or add the scale code to it.
Traces     : REQ-CAT-097, REQ-CAT-100
Data source: ENT-CAT-011.key, ENT-CAT-011.scaleCodeLength, ENT-CAT-011.embeddedValueTypeCode, ENT-CAT-011.valueDecimals, ENT-CAT-009.barcodeValue, ENT-CAT-009.isActiveFl
Source     : US-CAT-017

### RULE-CAT-036 — Label copy limits
Scope      : ENT-CAT-002
Trigger    : on label request
Statement  : The system shall require 1 to 999 copies per Variant and at most 5,000 labels per request.
Message    : ar: عدد النسخ لكل متغيّر من 1 إلى 999، وإجمالي الملصقات لا يتجاوز 5000. · en: Copies per variant are 1 to 999, and the total cannot exceed 5,000 labels.
Traces     : REQ-CAT-103
Data source: ENT-CAT-002.variantPk (the copy counts are inputs of the label request, not stored data)
Source     : D-10

### RULE-CAT-037 — Labelled Variant carries a barcode
Scope      : ENT-CAT-002, ENT-CAT-009
Trigger    : on label request
Statement  : The system shall require every Variant of a label request to carry at least one active barcode.
Message    : ar: لا يوجد باركود نشط لـ {count} متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع. · en: {count} variants have no active barcode. Generate an internal barcode for them, then print.
Traces     : REQ-CAT-104
Data source: ENT-CAT-009.variantFk, ENT-CAT-009.isActiveFl
Source     : US-CAT-018

### RULE-CAT-038 — Image file and count
Scope      : ENT-CAT-010
Trigger    : on upload
Statement  : The system shall require a JPG, PNG or WEBP file of at most 5 MB, held in public storage, and at most 20 active media records per Item. The file part is enforced at upload by the FILE category CAT_ITEM_IMAGE that CAT registers (allowPublic, PNG / JPEG / WEBP, 5 MB), whose refusal is the file module's own; CAT refuses the 21st active media record with this message, and answers a file reference that is not available or has no public URL as not found (ADR-CAT-076).
Message    : ar: الصورة يجب أن تكون JPG أو PNG أو WEBP بحجم لا يتجاوز 5 ميجابايت، وبحد أقصى 20 صورة نشطة للصنف. · en: The image must be JPG, PNG or WEBP of at most 5 MB, with at most 20 active images per item.
Traces     : REQ-CAT-110
Data source: ENT-CAT-010.fileDocumentRef, ENT-CAT-010.itemFk, ENT-CAT-010.isActiveFl
Source     : D-10

### RULE-CAT-039 — Exactly one main image
Scope      : ENT-CAT-010
Trigger    : on upload · on update · on removal
Statement  : The system shall keep exactly one active media record with isPrimaryFl true for every Item that has active media, by marking the first image primary, clearing the former primary when another is chosen, and promoting the lowest sortOrder when the primary is removed.
Message    : ar: للصنف صورة رئيسية واحدة فقط؛ تم تعيين الصورة المختارة رئيسية بدل السابقة. · en: An item has exactly one main image; the selected image replaced the previous main image.
Traces     : REQ-CAT-106, REQ-CAT-108, REQ-CAT-111
Data source: ENT-CAT-010.isPrimaryFl, ENT-CAT-010.itemFk, ENT-CAT-010.sortOrder, ENT-CAT-010.isActiveFl
Source     : US-CAT-019; DP §8 P-12

### RULE-CAT-040 — Image Variant belongs to the Item
Scope      : ENT-CAT-010
Trigger    : on upload · on update
Statement  : The system shall require a media record's variantFk, when set, to point to a Variant of the same Item.
Message    : ar: المتغيّر المختار لا ينتمي إلى هذا الصنف. · en: The selected variant does not belong to this item.
Traces     : REQ-CAT-112
Data source: ENT-CAT-010.variantFk, ENT-CAT-010.itemFk, ENT-CAT-002.itemFk
Source     : US-CAT-019

### RULE-CAT-041 — Publish state follows the Item
Scope      : ENT-CAT-001
Trigger    : on publish · on Item deactivation
Statement  : The system shall prevent publishing an inactive Item or an Item with no active Variant, and set publishStateCode to UNPUBLISHED when an Item is deactivated.
Message    : ar: لا يمكن نشر صنف غير نشط أو بلا متغيّر نشط. فعّل الصنف أو أحد متغيّراته أولًا. · en: An inactive item, or one with no active variant, cannot be published. Activate the item or one of its variants first.
Traces     : REQ-CAT-005, REQ-CAT-113, REQ-CAT-116
Data source: ENT-CAT-001.isActiveFl, ENT-CAT-001.publishStateCode, ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl
Source     : POL-CAT-008

### RULE-CAT-042 — E-invoice code pair and format
Scope      : ENT-CAT-001, ENT-CAT-002
Trigger    : on create · on update · on import row
Statement  : The system shall require einvoiceCodeTypeCode and einvoiceCode to be both present or both empty, a GS1 code of 8 to 14 digits, and an EGS code that starts with "EG-" and has at most 100 characters.
Message    : ar: أدخل نوع رمز الفاتورة الإلكترونية والرمز معًا: GS1 من 8 إلى 14 رقمًا، وEGS يبدأ بـ EG- ولا يتجاوز 100 حرف. · en: Enter the e-invoice code type and code together: GS1 is 8 to 14 digits, EGS starts with EG- and has at most 100 characters.
Traces     : REQ-CAT-117, REQ-CAT-118, REQ-CAT-123
Data source: ENT-CAT-001.einvoiceCodeTypeCode, ENT-CAT-001.einvoiceCode, ENT-CAT-002.einvoiceCodeTypeCode, ENT-CAT-002.einvoiceCode
Source     : PRD P-4; Egypt ETA e-invoice SDK (itemType)

### RULE-CAT-043 — One e-invoice unit per unit of measure
Scope      : ENT-CAT-012
Trigger    : on create
Statement  : The system shall prevent creating a Unit e-invoice code for a key that already has an active one.
Message    : ar: لهذه الوحدة رمز فاتورة إلكترونية نشط بالفعل. عدّله بدلًا من إضافة رمز جديد. · en: This unit already has an active e-invoice unit. Change it instead of adding a new one.
Traces     : REQ-CAT-120, REQ-CAT-124
Data source: ENT-CAT-012.key, ENT-CAT-012.isActiveFl
Source     : US-CAT-021

### RULE-CAT-044 — Import file acceptance
Scope      : ENT-CAT-001
Trigger    : on import submission
Statement  : The system shall require an xlsx file of at most 10 MB with at most 5,000 data rows and a header row equal to the import sheet's 11 columns.
Message    : ar: الملف غير مقبول: يجب أن يكون بصيغة xlsx بحجم لا يتجاوز 10 ميجابايت وحتى 5000 صف، وبعناوين ورقة الاستيراد نفسها. نزّل ورقة الاستيراد واستخدمها. · en: The file was not accepted: it must be xlsx, at most 10 MB and 5,000 rows, with the import sheet headers. Download the import sheet and use it.
Traces     : REQ-CAT-040
Data source: ENT-CAT-001.nameAr, ENT-CAT-001.nameEn, ENT-CAT-001.baseUomCode, ENT-CAT-001.categoryFk, ENT-CAT-001.brandCode, ENT-CAT-001.taxClassCode, ENT-CAT-001.einvoiceCodeTypeCode, ENT-CAT-001.einvoiceCode, ENT-CAT-009.barcodeValue, ENT-CAT-001.descriptionAr, ENT-CAT-001.descriptionEn (the column set the header row is checked against; type, size and row count are read from the submitted file)
Source     : US-CAT-010; D-10

### RULE-CAT-045 — Import row validation
Scope      : ENT-CAT-001, ENT-CAT-009
Trigger    : on import row
Statement  : The system shall validate each data row against RULE-CAT-001, RULE-CAT-002, RULE-CAT-003, RULE-CAT-006, RULE-CAT-026, RULE-CAT-029, RULE-CAT-030 and RULE-CAT-042, skip every row that fails, and report its row number, column and reason. A category code that matches no active Category is a failure of column category with the RULE-CAT-026 message.
Message    : ar: الصف {row}، العمود {column}: {reason}. صحّح الصف وأعد استيراده. · en: Row {row}, column {column}: {reason}. Correct the row and import it again.
Traces     : REQ-CAT-037, REQ-CAT-041
Data source: ENT-CAT-001.nameAr, ENT-CAT-001.nameEn, ENT-CAT-001.baseUomCode, ENT-CAT-001.categoryFk, ENT-CAT-001.brandCode, ENT-CAT-001.taxClassCode, ENT-CAT-001.einvoiceCodeTypeCode, ENT-CAT-001.einvoiceCode, ENT-CAT-009.barcodeValue
Source     : US-CAT-010; KB:raw-idea.md §6.5

### RULE-CAT-046 — Search term minimum
Scope      : ENT-CAT-001
Trigger    : on catalogue search
Statement  : The system shall require a search term of at least 2 characters before searching.
Message    : ar: اكتب حرفين على الأقل للبحث. · en: Type at least 2 characters to search.
Traces     : REQ-CAT-126
Data source: ENT-CAT-001.nameAr, ENT-CAT-001.nameEn, ENT-CAT-001.code, ENT-CAT-009.barcodeValue
Source     : D-10

### RULE-CAT-047 — Barcode not found
Scope      : ENT-CAT-009
Trigger    : on barcode lookup
Statement  : The system shall return no Variant, with a next action, when no active barcode of an active Variant of an active Item and no active scale label layout matches the value.
Message    : ar: لا يوجد صنف بهذا الباركود. ابحث بالاسم أو أضف الباركود إلى الصنف. · en: No item has this barcode. Search by name or add the barcode to the item.
Traces     : REQ-CAT-090, REQ-CAT-086
Data source: ENT-CAT-009.barcodeValue, ENT-CAT-009.isActiveFl, ENT-CAT-002.isActiveFl, ENT-CAT-001.isActiveFl, ENT-CAT-011.key, ENT-CAT-011.isActiveFl
Source     : KB:raw-idea.md §6.5; ADR-CAT-059

### RULE-CAT-048 — Deactivation keeps records for consumers (ARCH-5)
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-006, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009
Trigger    : on deactivation
Statement  : The system shall deactivate a SHARED (owner) record by setting isActiveFl to false, never by deletion, keep it readable by identifier for consuming modules, exclude it from new selections and publish a catalogue-change event with change kind DEACTIVATED.
Message    : ar: تم الإيقاف. يبقى السجل متاحًا للمستندات السابقة ولا يظهر في الاختيارات الجديدة. · en: Deactivated. The record stays available to earlier documents and no longer appears in new selections.
Traces     : REQ-CAT-005, REQ-CAT-050, REQ-CAT-066, REQ-CAT-079, REQ-CAT-085
Data source: ENT-CAT-001.isActiveFl, ENT-CAT-002.isActiveFl, ENT-CAT-006.isActiveFl, ENT-CAT-007.isActiveFl, ENT-CAT-008.isActiveFl, ENT-CAT-009.isActiveFl
Source     : ARCH-5 (effect on SOFT-READ consumers: notify, never cascade)

### RULE-CAT-049 — Change events only after commit
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010
Trigger    : on commit or rollback of a catalogue change
Statement  : The system shall record each catalogue-change event in the same transaction as the change and deliver it only after commit.
Message    : ar: تعذّر حفظ التغيير ولم يُرسل أي إشعار بالتغيير. أعد المحاولة. · en: The change was not saved and no change notice was sent. Try again.
Traces     : REQ-CAT-008, REQ-CAT-009, REQ-CAT-139
Data source: ENT-CAT-001.itemPk, ENT-CAT-002.variantPk
Source     : US-CAT-002; given `events` outbox

### RULE-CAT-050 — A reactivated Item has an active Variant
Scope      : ENT-CAT-001, ENT-CAT-002
Trigger    : on reactivation
Statement  : The system shall prevent reactivating an Item that has no active Variant.
Message    : ar: لا يمكن إعادة تفعيل صنف بلا متغيّر نشط. أنشئ متغيّرًا له أو أنشئ صنفًا جديدًا. · en: An item with no active variant cannot be reactivated. Generate a variant for it, or create a new item.
Traces     : REQ-CAT-130
Data source: ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl
Source     : A7 ACTIVE-state invariants; ADR-CAT-059

### RULE-CAT-051 — Item unit used by active barcodes stays active
Scope      : ENT-CAT-008, ENT-CAT-009
Trigger    : on Item unit deactivation
Statement  : The system shall prevent deactivating an Item unit while active barcodes of the Variants of the same Item are bound to its uomCode.
Message    : ar: هذه الوحدة مستخدمة في {count} باركود نشط. أوقف هذه الباركودات أولًا. · en: This unit is used by {count} active barcodes. Deactivate those barcodes first.
Traces     : REQ-CAT-128
Data source: ENT-CAT-008.itemFk, ENT-CAT-008.uomCode, ENT-CAT-009.uomCode, ENT-CAT-009.variantFk, ENT-CAT-009.isActiveFl, ENT-CAT-002.itemFk
Source     : US-CAT-016; ADR-CAT-060

### RULE-CAT-052 — Generation names every attribute the Item uses
Scope      : ENT-CAT-002, ENT-CAT-005
Trigger    : on Variant generation
Statement  : The system shall require a generation request to select at least one active value of every attribute that the Item's active Variants already carry.
Message    : ar: اختر قيمة واحدة على الأقل لكل خاصية يستخدمها الصنف بالفعل. · en: Select at least one value for every attribute the item already uses.
Traces     : REQ-CAT-129
Data source: ENT-CAT-005.variantFk, ENT-CAT-005.variantAttributeFk, ENT-CAT-005.isActiveFl, ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl
Source     : US-CAT-011; RULE-CAT-013

### RULE-CAT-053 — An inactive parent refuses child writes
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010
Trigger    : on Item update that changes more than categoryFk · on Item unit create or update · on image create or update · on barcode create · on Variant e-invoice override
Statement  : The system shall prevent changing any field of an inactive Item other than categoryFk, creating or changing an Item unit or image of an inactive Item, and adding a barcode to, or setting the e-invoice override of, a Variant that is inactive or belongs to an inactive Item. Three repair operations stay allowed on an inactive Item, each under its own rules: Variant generation (RULE-CAT-008, 012, 013, 014, 020, 052), an update whose only change is categoryFk (RULE-CAT-026) and the replacement of the Bundle components (RULE-CAT-008, 018, 019) — ADR-CAT-068. Deactivating a child record stays allowed.
Message    : ar: هذا الصنف أو المتغيّر غير نشط ولا يمكن تعديله. أعد تفعيل الصنف أو اختر متغيّرًا نشطًا. · en: This item or variant is inactive and cannot be changed. Reactivate the item, or choose an active variant.
Traces     : REQ-CAT-135, REQ-CAT-145, REQ-CAT-146, REQ-CAT-147
Data source: ENT-CAT-001.isActiveFl, ENT-CAT-001.categoryFk, ENT-CAT-002.isActiveFl, ENT-CAT-002.itemFk, ENT-CAT-008.itemFk, ENT-CAT-009.variantFk, ENT-CAT-010.itemFk
Source     : US-CAT-001; ADR-CAT-063; ADR-CAT-068
Test-Hint  : Each refused write on an inactive Item is refused; each child deactivation and each of the three repairs on it succeeds.

## A6 — Lookups

```yaml name=lookups
lookups:
  - {key: CAT_ITEM_KIND, seeded: [STANDARD, BUNDLE], open: false, values: [STANDARD, BUNDLE], fields: [itemKindCode], owner: CAT, entity: ENT-CAT-001, control: lookup, source: "US-CAT-012; D-04"}
  - {key: CAT_PUBLISH_STATE, seeded: [UNPUBLISHED, PUBLISHED], open: false, values: [UNPUBLISHED, PUBLISHED], fields: [publishStateCode], owner: CAT, entity: ENT-CAT-001, control: lookup, source: "POL-CAT-008; US-CAT-020"}
  - {key: CAT_BRAND, seeded: [], open: true, values: [OLDBRAND, XYZ, BRAND_068, BRAND1_137, BRAND2_137, BRAND3_137, BRAND4_137, BRAND5_137, BRAND6_137, RETIRED_177], fields: [brandCode], owner: CAT, entity: ENT-CAT-001, control: lookup, source: "US-CAT-014; module-registry LOOKUPS OWNED (brand); DP §8 P-05"}
  - {key: CAT_UOM, seeded: [PIECE, CARTON, BOX, DOZEN, KILOGRAM, GRAM, METRE, LITRE], open: true, values: [PIECE, CARTON, BOX, DOZEN, KILOGRAM, GRAM, METRE, LITRE, BAG], fields: [baseUomCode, uomCode, key], owner: CAT, entity: "ENT-CAT-001, ENT-CAT-008, ENT-CAT-009, ENT-CAT-012", control: lookup, source: "US-CAT-014, US-CAT-015; module-registry LOOKUPS OWNED (unit-of-measure); D-12"}
  - {key: CAT_TAX_CLASS, seeded: [STANDARD, ZERO_RATED, EXEMPT], open: true, values: [STANDARD, ZERO_RATED, EXEMPT], fields: [taxClassCode], owner: CAT, entity: ENT-CAT-001, control: lookup, source: "US-CAT-021; ADR-CAT-040"}
  - {key: CAT_EINVOICE_ITEM_CODE_TYPE, seeded: [GS1, EGS], open: false, values: [GS1, EGS], fields: [einvoiceCodeTypeCode], owner: CAT, entity: "ENT-CAT-001, ENT-CAT-002", control: lookup, source: "PRD P-4; Egypt ETA e-invoice SDK itemType"}
  - {key: CAT_EINVOICE_UNIT, seeded: [EA, KGM, GRM, MTR, LTR, CT, BX, DZN], open: true, values: [EA, KGM, GRM, MTR, LTR, CT, BX, DZN, H87], fields: [einvoiceUnitCode], owner: CAT, entity: ENT-CAT-012, control: lookup, source: "US-CAT-021; Egypt ETA e-invoice SDK unitType; D-12"}
  - {key: CAT_BARCODE_TYPE, seeded: [GTIN, INTERNAL, SCALE], open: false, values: [GTIN, INTERNAL, SCALE], fields: [barcodeTypeCode], owner: CAT, entity: ENT-CAT-009, control: lookup, source: "module-registry OI-10; D-05"}
  - {key: CAT_SCALE_VALUE_TYPE, seeded: [WEIGHT, PRICE], open: false, values: [WEIGHT, PRICE], fields: [embeddedValueTypeCode], owner: CAT, entity: ENT-CAT-011, control: lookup, source: "US-CAT-017; module-registry OI-10"}
  - {key: CAT_CHANGE_KIND, seeded: [CREATED, UPDATED, DEACTIVATED, REACTIVATED], open: false, values: [CREATED, UPDATED, DEACTIVATED, REACTIVATED], fields: [], owner: CAT, entity: "catalogue-change event (REQ-CAT-008)", control: event payload, source: "US-CAT-002"}
```

Labels per language:

| Key | Value | ar | en |
|---|---|---|---|
| CAT_ITEM_KIND | STANDARD · BUNDLE | صنف عادي · حزمة | Standard item · Bundle |
| CAT_PUBLISH_STATE | UNPUBLISHED · PUBLISHED | غير منشور · منشور | Not published · Published |
| CAT_UOM | PIECE · CARTON · BOX · DOZEN · KILOGRAM · GRAM · METRE · LITRE | قطعة · كرتونة · علبة · دستة · كيلوجرام · جرام · متر · لتر | Piece · Carton · Box · Dozen · Kilogram · Gram · Metre · Litre |
| CAT_TAX_CLASS | STANDARD · ZERO_RATED · EXEMPT | خاضع للنسبة العامة · خاضع لنسبة صفر · معفى | Standard rate · Zero-rated · Exempt |
| CAT_EINVOICE_ITEM_CODE_TYPE | GS1 · EGS | GS1 · EGS | GS1 · EGS |
| CAT_EINVOICE_UNIT | EA · KGM · GRM · MTR · LTR · CT · BX · DZN | وحدة · كيلوجرام · جرام · متر · لتر · كرتونة · علبة · دستة | Each · Kilogram · Gram · Metre · Litre · Carton · Box · Dozen |
| CAT_BARCODE_TYPE | GTIN · INTERNAL · SCALE | GTIN · داخلي · ميزان | GTIN · Internal · Scale |
| CAT_SCALE_VALUE_TYPE | WEIGHT · PRICE | وزن · سعر | Weight · Price |
| CAT_CHANGE_KIND | CREATED · UPDATED · DEACTIVATED · REACTIVATED | إنشاء · تعديل · إيقاف · إعادة تفعيل | Created · Updated · Deactivated · Reactivated |

Rationale:
- All keys are held in the core lookup module (`mdl`) and loaded at runtime [DP §5 G6].
- CAT_BRAND starts empty because the list belongs to the tenant. Its `values` list every brand the analysis names: OLDBRAND and XYZ from the acceptance criteria, and BRAND_068, BRAND1_137 … BRAND6_137 and RETIRED_177, the per-case values of the test plans (named `<VALUE>_<TC seq>` so that no two cases share one).
- BAG (CAT_UOM) and H87 (CAT_EINVOICE_UNIT) are named in acceptance criteria as values added later; they are not seeded.
- A value beyond the seeded ones is host data. It is created through the given mdl call `POST /api/v1/mdl/lookup-types/{id}/values` and deactivated through `DELETE /api/v1/mdl/lookup-values/{id}`, where `{id}` is the lookup type found by `POST /api/v1/mdl/lookup-types/search`; CAT never seeds it.
- The screens read the active values of a key through CAT's own read of its keys (`GET /api/v1/cat/lookups/{key}`, ADR-CAT-065), never through a pass-through to arbitrary mdl keys.

Consumed lookups: none.

## A7 — Status lifecycle

Only ENT-CAT-001 Item has more than two transitions; it combines isActiveFl and publishStateCode. No other entity has a status field with more than two states.

```
          create
            │
            ▼
   ┌──────────────────────┐   publish (RULE-CAT-041)  ┌──────────────────────┐
   │ ACTIVE · UNPUBLISHED │ ────────────────────────▶ │ ACTIVE · PUBLISHED   │
   │                      │ ◀──────────────────────── │                      │
   └──────────────────────┘        unpublish          └──────────────────────┘
        │        ▲                                            │
 deactivate      │ reactivate                                 │ deactivate
 (RULE-CAT-021,  │ (stays UNPUBLISHED)                        │ (RULE-CAT-041 sets
  RULE-CAT-048)  │                                            │  UNPUBLISHED)
        ▼        │                                            ▼
   ┌──────────────────────┐ ◀─────────────────────────────────┘
   │ INACTIVE·UNPUBLISHED │
   └──────────────────────┘
```

**ACTIVE-state invariants (ADR-CAT-059).** An Item in ACTIVE (·UNPUBLISHED or ·PUBLISHED) always has:
1. at least one active Variant (RULE-CAT-015 while active; RULE-CAT-050 on reactivation);
2. an empty categoryFk, or one that points to an active Category (RULE-CAT-026);
3. when its kind is BUNDLE, active component lines whose Variants are active and belong to active STANDARD Items (RULE-CAT-019, RULE-CAT-021);
4. a normalised nameAr that no other active Item carries (RULE-CAT-003);
5. exactly one default Variant (isDefaultFl true), and it is active.

The default Variant moves with the Item's active Variants (REQ-CAT-140, REQ-CAT-141, ADR-CAT-072). When the default Variant is deactivated while another Variant of the Item is active, isDefaultFl moves in the same transaction, under the Item lock, to the remaining active Variant with the lowest code. When Variants are generated for an Item with no active Variant, the first Variant created takes isDefaultFl and the inactive former default loses it. An Item therefore holds at most one Variant with isDefaultFl true at any time, and while it has an active Variant, that Variant is active.

Every transition into ACTIVE (create, import row, reactivate) re-establishes these invariants, not only the guard of its own trigger. While the Item is inactive, the records it points to may still change (its Category, its component Variants), and its own children may be deactivated, but no child of it may be created or changed (RULE-CAT-053, ADR-CAT-063). Reactivation is therefore refused (REQ-CAT-130, REQ-CAT-142, REQ-CAT-143, REQ-CAT-144) until the invariants hold again. Each broken invariant has a repair that stays allowed on the inactive Item, so an inactive Item can always return to ACTIVE (ADR-CAT-068):
- invariant 1 (no active Variant): Variant generation creates an active Variant (REQ-CAT-049, REQ-CAT-145; it needs `variants` on — while the flag is off, the next action is to turn it on or to create a new Item, as RULE-CAT-050's message says);
- invariant 2 (inactive Category): an update whose only change is categoryFk points the Item to an active Category or to none (REQ-CAT-072, REQ-CAT-146), or the Category itself is reactivated (REQ-CAT-136, ADR-CAT-069);
- invariant 3 (invalid component): the Bundle components are replaced with valid lines (REQ-CAT-057, REQ-CAT-147; it needs `bundles` on);
- invariant 4 (name taken): the other active Item is renamed or deactivated; the Item's own name cannot change while it is inactive.

Deactivating an Item leaves its Variants and their barcodes active, so that the Item can be reactivated with them (REQ-CAT-005). While the Item is inactive, those barcodes no longer resolve (REQ-CAT-086, ADR-CAT-059) but still hold their values under RULE-CAT-029; the owner releases a GTIN by deactivating the barcode (REQ-CAT-085, API-CAT-016), which stays allowed on an inactive Item. Deactivating a Variant, by contrast, deactivates its barcodes and releases their values (REQ-CAT-050, ADR-CAT-063).

The other records that can be deactivated also have a way back: Categories, Variant attributes and Variant attribute values are reactivated under their own uniqueness and hierarchy rules (REQ-CAT-136 … REQ-CAT-138), and a Variant is replaced by regenerating its combination. The channel reads of an Item's children (barcode lookup, Variant search, labels) also require an active Item (REQ-CAT-086, REQ-CAT-101). Only the reads by identifier return inactive records (RULE-CAT-048).

No module-specific approval flow: no story asks for one, and the profile forbids a workflow engine.

## A8 — Module dependencies

```yaml name=module-dependencies
consumes: []
```

CAT is first in the fixed module order and consumes no entity of another business module [KB:raw-idea.md §5]. Its uses of erp-core are given dependencies, listed in A9; they create no XM edge.

| External service | Purpose | Integration kind |
|---|---|---|
| — | CAT calls no external service; e-invoice submission to tax authorities belongs to TAX | — |

## A9 — Given usage

```yaml name=given-usage
rows:
  - {given: erp-core, module: modules.mdl, via: com.erp.mdl.crossmodule.MdlLookupApi, purpose: "load the values of the CAT_* lookup keys (brand, unit of measure, tax class, e-invoice codes, barcode type, scale value type, item kind, publish state) at runtime"}
  - {given: erp-core, module: modules.mdl, via: com.erp.mdl.crossmodule.MdlLookupApi, purpose: "serve CAT's own lookup keys to the screens (API-CAT-052) through the common owned-lookups capability, com.erp.common.lookup.OwnedLookups.read(key, the 10 CAT keys, CAT-404-LOOKUP-KEY-UNKNOWN, MdlLookupApi::readActiveValuesByKey) — never a pass-through for other keys (ADR-CAT-065); erp-core's manifest lists owned-lookups under common but publishes no interface for it, so the row names the mdl interface the call goes through"}
  - {given: erp-core, module: modules.cu, via: com.erp.cu.crossmodule.SettingsApi, purpose: "read the `variants` and `bundles` capability flags behind the FeatureFlags API (ADR-CAT-041)"}
  - {given: erp-core, module: modules.sequence, via: com.erp.sequence.crossmodule.NumberSeriesApi, purpose: "issue Item codes (CAT_ITEM), Variant codes (CAT_VARIANT) and internal barcode digits (CAT_INTERNAL_BARCODE)"}
  - {given: erp-core, module: modules.file, via: docs/api-docs/file/index.md, purpose: "the client uploads Item images to the FILE category CAT_ITEM_IMAGE (allowPublic = true, PNG / JPEG / WEBP, at most 5 MB — the FILE endpoint refuses any other file, ADR-CAT-076) and import files to the private storage through the given FILE upload endpoints, and passes the returned FILE_DOCUMENT.ID to CAT; CAT has no multipart endpoint (ADR-CAT-056)"}
  - {given: erp-core, module: modules.file, via: com.erp.file.crossmodule.FileDocumentLookupApi, purpose: "the published methods only: isAvailable (a referenced file exists and is available in the caller's tenant — API-CAT-018, API-CAT-049), publicUrl and publicUrls (an image has a public URL, and image URLs for reads); the type, size and bytes of an import file are not published and wait on CORE-GAP-file-document-metadata-read (ADR-CAT-076)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.OutboxPublisher, purpose: "record catalogue-change events in the change transaction and deliver them after commit (RULE-CAT-049)"}
  - {given: erp-core, module: modules.report, via: com.erp.report.crossmodule.PrintApi, purpose: "produce the printable barcode label document (REQ-CAT-102) and the xlsx item import sheet (REQ-CAT-036)"}
  - {given: erp-core, module: modules.report, via: com.erp.report.ReportProvider, purpose: "register the catalogue reports CAT_BARCODE_LABEL and CAT_ITEM_IMPORT_SHEET (ADR-CAT-057)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.permission.PermissionContributor, purpose: "register the CAT page codes so the security module derives their VIEW/CREATE/UPDATE/DELETE permissions"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantProvisioningContributor, purpose: "copy the 8 seeded Unit e-invoice codes into each newly provisioned tenant and register the FILE category CAT_ITEM_IMAGE there (ADR-CAT-076); the given copies the CAT lookup values and number series itself (ADR-CAT-058, REQ-CAT-134)"}
```

No ENT re-creates an entity that a given provides. Tenant, User, Role, LookupType/LookupValue, FileDocument and NumberSeries are used, never re-declared [C:C-GIVEN-1].

# PART B — SCREEN REQUIREMENTS

## SCR-REQ-CAT-001 — Items / الأصناف

### B1 — Definition
Purpose      : Find, create and maintain Items with their Variants, units, barcodes, Bundle components, images, e-invoice data and publish state.
Entities     : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010, ENT-CAT-006 (reference)
Operations   : search, list, create, read, update, deactivate, reactivate, publish, unpublish, generate Variants, deactivate Variant, e-invoice override, add/deactivate Item unit, add GTIN / generate internal / add scale barcode, deactivate barcode, add/update/remove image, maintain Bundle components
Users        : Owner (all), Seller/cashier (read), Warehouse staff (read)
Navigation   : Catalog / الكتالوج → Items / الأصناف; from: home, global search result, Categories (Items of a Category); to: Variant attributes, Barcode labels, Item import
Content shape: header + repeating lines (Variants, Item units, barcodes, Bundle components, images)
Traces       : REQ-CAT-001 … REQ-CAT-007, REQ-CAT-014 … REQ-CAT-020, REQ-CAT-024 … REQ-CAT-035, REQ-CAT-049 … REQ-CAT-063, REQ-CAT-072 … REQ-CAT-091, REQ-CAT-096, REQ-CAT-105 … REQ-CAT-119, REQ-CAT-128 … REQ-CAT-133, REQ-CAT-135, REQ-CAT-140 … REQ-CAT-147
Composite    : Search + Entry — one screen requirement
Inactive Item: the Entry of an inactive Item is read-only except Reactivate, the line deactivations and the three repairs — the Category field, "Generate variants" (while `variants` is on) and the Bundle components (while `bundles` is on) (REQ-CAT-135, REQ-CAT-145 … REQ-CAT-147, RULE-CAT-053, ADR-CAT-068).

### B2 — Search / list
| Filter | Source | Result column |
|---|---|---|
| Text (nameAr, nameEn, code, barcode) | ENT-CAT-001.nameAr / nameEn / code; ENT-CAT-009.barcodeValue | code, nameAr, nameEn |
| Category | ENT-CAT-001.categoryFk → ENT-CAT-006 | Category nameAr |
| Brand | ENT-CAT-001.brandCode — CAT_BRAND | Brand |
| Item kind (shown only while `bundles` is on — REQ-CAT-015) | ENT-CAT-001.itemKindCode — CAT_ITEM_KIND | Item kind |
| Publish state | ENT-CAT-001.publishStateCode — CAT_PUBLISH_STATE | Publish state |
| Active | ENT-CAT-001.isActiveFl | Active |

Sorted by nameAr (REQ-CAT-003). Applies REQ-CAT-014 and REQ-CAT-015 to filters.

### B3 — Input
- **Header (mandatory, outside Advanced — 4 fields, REQ-CAT-026):** ENT-CAT-001.nameAr, baseUomCode (pre-filled PIECE), taxClassCode (pre-filled STANDARD), itemKindCode (pre-filled STANDARD; shown while `bundles` is on).
- **Header (read-only):** ENT-CAT-001.code, publishStateCode, isActiveFl.
- **Advanced:** ENT-CAT-001.nameEn, categoryFk, brandCode, descriptionAr, descriptionEn, einvoiceCodeTypeCode, einvoiceCode.
- **Variants section (while `variants` is on — REQ-CAT-014):** ENT-CAT-002 lines (code, derived names, attribute values from ENT-CAT-005, e-invoice override, isActiveFl). The action "Generate variants" selects values of ENT-CAT-003/ENT-CAT-004.
- **Units section:** ENT-CAT-008 lines (uomCode, conversionFactor, isPurchaseFl, isSalesFl).
- **Barcodes section:** ENT-CAT-009 lines per Variant (barcodeValue, barcodeTypeCode, uomCode). SCALE is offered per REQ-CAT-096.
- **Bundle components section (kind BUNDLE, while `bundles` is on):** ENT-CAT-007 lines (componentVariantFk, quantity, sortOrder).
- **Images section:** ENT-CAT-010 lines (image file — uploaded through the core FILE endpoints, then referenced by fileDocumentRef (ADR-CAT-056) — nameAr, nameEn, variantFk, sortOrder, isPrimaryFl).

| Action | Operation | RULEs applied |
|---|---|---|
| Save | create (REQ-CAT-024, REQ-CAT-025) / update (REQ-CAT-004) | RULE-CAT-001, 002, 003, 004, 005, 006, 007, 022, 026, 042, 053 |
| Deactivate / Reactivate | REQ-CAT-005 / REQ-CAT-006, REQ-CAT-130, REQ-CAT-142, REQ-CAT-143, REQ-CAT-144 | RULE-CAT-021, 041, 048 / RULE-CAT-003, 019, 026, 050 |
| Publish / Unpublish | REQ-CAT-113 / REQ-CAT-114 | RULE-CAT-041 |
| Generate variants (also on an inactive Item — ADR-CAT-068) | REQ-CAT-049, REQ-CAT-129, REQ-CAT-141, REQ-CAT-145 | RULE-CAT-008, 012, 013, 014, 020, 052 |
| Deactivate variant (its barcodes and assignments with it) | REQ-CAT-050, REQ-CAT-140 | RULE-CAT-015, 021, 048 |
| Save variant e-invoice override | REQ-CAT-118 | RULE-CAT-042, 053 |
| Add unit / change flags / deactivate unit | REQ-CAT-077 / 078 / 079, REQ-CAT-128 | RULE-CAT-027, 028, 048, 051, 053 |
| Add GTIN / Generate internal barcode / Add scale code | REQ-CAT-083 / 084 / 096 | RULE-CAT-029, 030, 031, 032, 033, 053 |
| Deactivate barcode | REQ-CAT-085 | RULE-CAT-048 |
| Save Bundle components (also on an inactive Bundle — ADR-CAT-068) | REQ-CAT-056, REQ-CAT-057, REQ-CAT-147 | RULE-CAT-008, 018, 019 |
| Upload / update / remove image | REQ-CAT-105 / 106 / 107 | RULE-CAT-038, 039, 040, 053 |
| Import items | opens SCR-REQ-CAT-004 | — |
| Print labels | opens SCR-REQ-CAT-005 | — |

### B4 — Access
Page code: CAT_ITEMS (one SEC_PAGES row)

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | ✓ | ✓ |
| Seller/cashier | ✓ | — | — | — |
| Warehouse staff | ✓ | — | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| create Item | POST | /api/v1/cat/items | Item fields | Item with default Variant | RULE-CAT-001, 002, 003, 004, 005, 006, 008, 026, 042 | REQ-CAT-024, 025, 032, 033 |
| search Items | GET | /api/v1/cat/items | filters of B2, paging | Page of Item | — | REQ-CAT-003 |
| read Item | GET | /api/v1/cat/items/{id} | id | Item with lines | — | REQ-CAT-002, 058, 119 |
| update Item | PUT | /api/v1/cat/items/{id} | Item fields | Item | RULE-CAT-001, 002, 003, 004, 006, 007, 022, 026, 042, 053 | REQ-CAT-004, 117, 135, 146 |
| deactivate Item | DELETE | /api/v1/cat/items/{id} | id | confirmation | RULE-CAT-021, 041, 048 | REQ-CAT-005 |
| reactivate / publish / unpublish Item | PATCH | /api/v1/cat/items/{id} | isActiveFl or publishStateCode | Item | RULE-CAT-003, 019, 026, 041, 050 | REQ-CAT-006, 113, 114, 130, 142, 143, 144 |
| generate Variants | POST | /api/v1/cat/items/{id}/variants | selected attribute values | list of Variant | RULE-CAT-008, 012, 013, 014, 020, 052 (allowed on an inactive Item — ADR-CAT-068) | REQ-CAT-049, 129, 141, 145 |
| update Variant override | PUT | /api/v1/cat/variants/{id} | einvoiceCodeTypeCode, einvoiceCode | Variant | RULE-CAT-004, 042, 053 | REQ-CAT-118, 135 |
| deactivate Variant | DELETE | /api/v1/cat/variants/{id} | id | confirmation | RULE-CAT-015, 021, 048 | REQ-CAT-050, 140 |
| add Item unit | POST | /api/v1/cat/items/{id}/units | uomCode, conversionFactor, flags | Item unit | RULE-CAT-006, 027, 053 | REQ-CAT-077, 135 |
| update Item unit | PUT | /api/v1/cat/item-units/{id} | isPurchaseFl, isSalesFl | Item unit | RULE-CAT-028, 053 | REQ-CAT-078, 135 |
| deactivate Item unit | DELETE | /api/v1/cat/item-units/{id} | id | confirmation | RULE-CAT-048, 051 | REQ-CAT-079, 128 |
| add barcode | POST | /api/v1/cat/variants/{id}/barcodes | barcodeValue, barcodeTypeCode, uomCode | Barcode | RULE-CAT-006, 029, 030, 031, 032, 033, 053 | REQ-CAT-083, 096, 135 |
| generate internal barcode | POST | /api/v1/cat/variants/{id}/barcodes/internal | uomCode | Barcode | RULE-CAT-005, 031, 053 | REQ-CAT-084, 135 |
| deactivate barcode | DELETE | /api/v1/cat/barcodes/{id} | id | confirmation | RULE-CAT-048 | REQ-CAT-085 |
| barcode lookup (incl. scale label) | GET | /api/v1/cat/barcodes/lookup | value | Variant, Item, unit, embedded value | RULE-CAT-035, 047 | REQ-CAT-086, 090, 097, 100 |
| save Bundle components | PUT | /api/v1/cat/items/{id}/bundle-components | component lines | list of Bundle component | RULE-CAT-008, 018, 019 (allowed on an inactive Item — ADR-CAT-068) | REQ-CAT-056, 057, 147 |
| add image | POST | /api/v1/cat/items/{id}/media | fileDocumentRef (the file uploaded through the core FILE endpoints — ADR-CAT-056), nameAr, nameEn, variantFk | Item media | RULE-CAT-038, 039, 040, 053 | REQ-CAT-105, 108, 135 |
| update image | PUT | /api/v1/cat/media/{id} | sortOrder, isPrimaryFl, names, variantFk | Item media | RULE-CAT-039, 040, 053 | REQ-CAT-106, 135 |
| remove image | DELETE | /api/v1/cat/media/{id} | id | confirmation | RULE-CAT-039 | REQ-CAT-107, 111 |
| images of a Variant | GET | /api/v1/cat/variants/{id}/media | id | list of Item media | — | REQ-CAT-109 |
| active values of a CAT lookup key | GET | /api/v1/cat/lookups/{key} | one of the 10 CAT keys of A6 | list of code + ar/en label | — | REQ-CAT-073, 074, 076 |

Cross-module read operations (contract stage P1.5, not HTTP): read Item / Variant by identifier (REQ-CAT-001, 010, 119), storefront catalogue read (REQ-CAT-115), Items of a Category (REQ-CAT-068), barcode lookup (REQ-CAT-086).

## SCR-REQ-CAT-002 — Categories / الفئات

### B1 — Definition
Purpose      : Organise Items into a tree of Categories and sub-categories.
Entities     : ENT-CAT-006, ENT-CAT-001 (Items of a Category)
Operations   : list (tree), create, read, update (move), deactivate, reactivate, list Items of a Category
Users        : Owner (all), Seller/cashier and Warehouse staff (read)
Navigation   : Catalog / الكتالوج → Categories / الفئات; from: Items; to: Items (filtered by Category)
Content shape: true hierarchy (parent/child)
Traces       : REQ-CAT-064 … REQ-CAT-072, REQ-CAT-136
Composite    : Master + Detail (tree + node entry) — one screen requirement

### B2 — Search / list
| Filter | Source | Result column |
|---|---|---|
| Text (code, nameAr, nameEn) | ENT-CAT-006.code / nameAr / nameEn | code, nameAr, nameEn |
| Active | ENT-CAT-006.isActiveFl | Active |

Tree ordered by sortOrder under each parent (REQ-CAT-067).

### B3 — Input
- Mandatory (2 fields): ENT-CAT-006.code, nameAr.
- Advanced: nameEn, parentCategoryFk, sortOrder.

| Action | Operation | RULEs applied |
|---|---|---|
| Save | REQ-CAT-064 / REQ-CAT-065 | RULE-CAT-001, 002, 023, 024 |
| Deactivate | REQ-CAT-066 | RULE-CAT-025, 048 |
| Reactivate | REQ-CAT-136 | RULE-CAT-023, 024 |
| Show Items | REQ-CAT-068 | — |

### B4 — Access
Page code: CAT_CATEGORIES

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | ✓ | ✓ |
| Seller/cashier | ✓ | — | — | — |
| Warehouse staff | ✓ | — | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| create Category | POST | /api/v1/cat/categories | code, nameAr, nameEn, parentCategoryFk, sortOrder | Category | RULE-CAT-001, 002, 023, 024 | REQ-CAT-064 |
| list Category tree | GET | /api/v1/cat/categories | text, isActiveFl | tree of Category | — | REQ-CAT-067 |
| read Category | GET | /api/v1/cat/categories/{id} | id | Category | — | REQ-CAT-067 |
| update Category | PUT | /api/v1/cat/categories/{id} | names, parentCategoryFk, sortOrder | Category | RULE-CAT-023, 024 (code fixed) | REQ-CAT-065 |
| deactivate Category | DELETE | /api/v1/cat/categories/{id} | id | confirmation | RULE-CAT-025, 048 | REQ-CAT-066 |
| reactivate Category | PATCH | /api/v1/cat/categories/{id} | isActiveFl true | Category | RULE-CAT-023, 024 | REQ-CAT-136 |
| Items of a Category | GET | /api/v1/cat/categories/{id}/items | id, paging | Page of Item | — | REQ-CAT-068 |

## SCR-REQ-CAT-003 — Variant attributes / خصائص المتغيّرات

### B1 — Definition
Purpose      : Keep the tenant's Variant attributes (size, colour) and their values.
Entities     : ENT-CAT-003, ENT-CAT-004
Operations   : list, create, read, update, deactivate, reactivate (attribute and value)
Users        : Owner (all), Seller/cashier and Warehouse staff (read)
Navigation   : Catalog / الكتالوج → Variant attributes / خصائص المتغيّرات (menu entry present only while `variants` is on — REQ-CAT-014); from: Items; to: Items
Content shape: header + repeating lines (attribute + its values)
Traces       : REQ-CAT-014, REQ-CAT-016, REQ-CAT-042 … REQ-CAT-048, REQ-CAT-054, REQ-CAT-055, REQ-CAT-137, REQ-CAT-138
Composite    : Master + Detail — one screen requirement

### B2 — Search / list
| Filter | Source | Result column |
|---|---|---|
| Text (code, nameAr) | ENT-CAT-003.code / nameAr | code, nameAr, nameEn |
| Active | ENT-CAT-003.isActiveFl | Active |

Ordered by sortOrder (REQ-CAT-048).

### B3 — Input
- Attribute, mandatory (2 fields): ENT-CAT-003.code, nameAr. Advanced: nameEn, sortOrder.
- Value lines, mandatory (2 fields): ENT-CAT-004.code, nameAr. Advanced: nameEn, sortOrder.

| Action | Operation | RULEs applied |
|---|---|---|
| Save attribute / value | REQ-CAT-042, 043 / REQ-CAT-045, 046 | RULE-CAT-001, 002, 008, 016 |
| Deactivate attribute / value (allowed while `variants` is off — RULE-CAT-008) | REQ-CAT-044 / REQ-CAT-047 | RULE-CAT-017 |
| Reactivate attribute / value (allowed while `variants` is off — RULE-CAT-008) | REQ-CAT-137 / REQ-CAT-138 | RULE-CAT-016 |

### B4 — Access
Page code: CAT_VARIANT_ATTRIBUTES

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | ✓ | ✓ |
| Seller/cashier | ✓ | — | — | — |
| Warehouse staff | ✓ | — | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| list attributes with values | GET | /api/v1/cat/variant-attributes | text, isActiveFl | list of Variant attribute with values | — (a read stays allowed while `variants` is off — RULE-CAT-008) | REQ-CAT-048 |
| create attribute | POST | /api/v1/cat/variant-attributes | code, nameAr, nameEn, sortOrder | Variant attribute | RULE-CAT-001, 002, 008, 016 | REQ-CAT-042 |
| update attribute | PUT | /api/v1/cat/variant-attributes/{id} | nameAr, nameEn, sortOrder | Variant attribute | RULE-CAT-001, 002, 008, 016 | REQ-CAT-043 |
| deactivate attribute | DELETE | /api/v1/cat/variant-attributes/{id} | id | confirmation | RULE-CAT-017 | REQ-CAT-044 |
| reactivate attribute | PATCH | /api/v1/cat/variant-attributes/{id} | isActiveFl true | Variant attribute | RULE-CAT-016 | REQ-CAT-137 |
| create value | POST | /api/v1/cat/variant-attributes/{id}/values | code, nameAr, nameEn, sortOrder | Variant attribute value | RULE-CAT-001, 002, 008, 016 | REQ-CAT-045 |
| update value | PUT | /api/v1/cat/variant-attribute-values/{id} | nameAr, nameEn, sortOrder | Variant attribute value | RULE-CAT-001, 002, 008, 016 | REQ-CAT-046 |
| deactivate value | DELETE | /api/v1/cat/variant-attribute-values/{id} | id | confirmation | RULE-CAT-017 | REQ-CAT-047 |
| reactivate value | PATCH | /api/v1/cat/variant-attribute-values/{id} | isActiveFl true | Variant attribute value | RULE-CAT-016 | REQ-CAT-138 |

## SCR-REQ-CAT-004 — Item import / استيراد الأصناف

### B1 — Definition
Purpose      : Bring an existing item list in from Excel and learn what to fix for every row that could not come in.
Entities     : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Operations   : download import sheet, submit file (custom), read result
Users        : Owner
Navigation   : Setup wizard step 5 "import items" (wizard owner: P-02) → Item import / استيراد الأصناف; also Catalog / الكتالوج → Items → Import; to: Items
Content shape: other (upload + result list)
Traces       : REQ-CAT-036 … REQ-CAT-041, REQ-CAT-139
Composite    : Wizard step — one screen requirement

### B2 — Search / list
Not applicable. The result list shows rejected rows (row number, column, reason ar/en) from REQ-CAT-039.

### B3 — Input
- Mandatory (1 field): the xlsx file, uploaded through the core FILE endpoints to the private storage and passed to CAT as fileDocumentRef (ADR-CAT-056).

| Action | Operation | RULEs applied |
|---|---|---|
| Download import sheet | REQ-CAT-036 | — |
| Import | REQ-CAT-037, 038, 039 | RULE-CAT-044, 045 |

### B4 — Access
Page code: CAT_ITEM_IMPORT

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| download import sheet | GET | /api/v1/cat/item-imports/sheet | — | xlsx file (report CAT_ITEM_IMPORT_SHEET rendered by the core print service — ADR-CAT-057) | — | REQ-CAT-036 |
| import items | POST | /api/v1/cat/item-imports | fileDocumentRef of the uploaded xlsx file | import result (counts, rejected rows) | RULE-CAT-044, 045, 049 | REQ-CAT-037, 038, 039, 040, 041, 139 |

## SCR-REQ-CAT-005 — Barcode labels / ملصقات الباركود

### B1 — Definition
Purpose      : Print barcode labels for chosen Variants.
Entities     : ENT-CAT-002, ENT-CAT-009
Operations   : search Variants, create label request (print)
Users        : Owner, Warehouse staff
Navigation   : Catalog / الكتالوج → Barcode labels / ملصقات الباركود; from: Items; to: Items (to generate a missing internal barcode)
Content shape: header + repeating lines (Variant, copies)
Traces       : REQ-CAT-101 … REQ-CAT-104
Composite    : Search + Entry — one screen requirement

### B2 — Search / list
| Filter | Source | Result column |
|---|---|---|
| Text (Variant code, Item nameAr, barcode) | ENT-CAT-002.code; ENT-CAT-001.nameAr; ENT-CAT-009.barcodeValue | Variant code, Variant nameAr, barcode |

### B3 — Input
- Lines, mandatory (2 fields): Variant (ENT-CAT-002), copies.

| Action | Operation | RULEs applied |
|---|---|---|
| Print | REQ-CAT-102 | RULE-CAT-036, 037 |

### B4 — Access
Page code: CAT_BARCODE_LABELS

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | — | — |
| Warehouse staff | ✓ | ✓ | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search Variants | GET | /api/v1/cat/variants | text, paging | Page of Variant with first barcode | — | REQ-CAT-101 |
| print labels | POST | /api/v1/cat/barcode-labels | list of variantPk + copies | printable document | RULE-CAT-036, 037 | REQ-CAT-102, 103, 104 |

## SCR-REQ-CAT-006 — Catalogue settings / إعدادات الكتالوج

### B1 — Definition
Purpose      : Maintain the scale label layouts and the e-invoice unit of each unit of measure, and see what blocks turning a catalogue capability flag off.
Entities     : ENT-CAT-011, ENT-CAT-012, ENT-CAT-001, ENT-CAT-002, ENT-CAT-005 (turn-off check)
Operations   : read, create, update, deactivate (layouts and Unit e-invoice codes); turn-off check (custom)
Users        : Owner
Navigation   : Catalog / الكتالوج → Catalogue settings / إعدادات الكتالوج; from: Items; to: Items (dependent Items of a turn-off check)
Content shape: other (two flat lists + check panel)
Traces       : REQ-CAT-021 … REQ-CAT-023, REQ-CAT-092 … REQ-CAT-095, REQ-CAT-098, REQ-CAT-120 … REQ-CAT-124, REQ-CAT-134
Composite    : Master + Detail per list — one screen requirement

### B2 — Search / list
Not applicable: both lists are short and shown in full (REQ-CAT-095).

### B3 — Input
- Scale label layout, mandatory (4 fields): ENT-CAT-011.key, scaleCodeLength (pre-filled 5), embeddedValueTypeCode (pre-filled WEIGHT), valueDecimals (pre-filled 3). Advanced: valueAr, valueEn. key and scaleCodeLength are read-only after creation (ADR-CAT-064).
- Unit e-invoice code, mandatory (2 fields): ENT-CAT-012.key, einvoiceUnitCode.
- Turn-off check panel: flag (`variants`, `bundles`). Result: allowed, count, up to 20 Items.

| Action | Operation | RULEs applied |
|---|---|---|
| Save layout / deactivate layout | REQ-CAT-092, 093 / REQ-CAT-094 | RULE-CAT-006, 034 |
| Save / deactivate Unit e-invoice code | REQ-CAT-120, 121 / REQ-CAT-122 | RULE-CAT-006, 043 |
| Check turn-off | REQ-CAT-021 | RULE-CAT-010, 011 |

### B4 — Access
Page code: CAT_SETTINGS

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | ✓ | ✓ |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| read catalogue settings | GET | /api/v1/cat/settings | — | layouts + Unit e-invoice codes | — | REQ-CAT-095 |
| create layout | POST | /api/v1/cat/scale-label-layouts | key, scaleCodeLength, embeddedValueTypeCode, valueDecimals, valueAr, valueEn | Scale label layout | RULE-CAT-006, 034 | REQ-CAT-092 |
| update layout | PUT | /api/v1/cat/scale-label-layouts/{id} | fields except key and scaleCodeLength | Scale label layout | RULE-CAT-034 | REQ-CAT-093 |
| deactivate layout | DELETE | /api/v1/cat/scale-label-layouts/{id} | id | confirmation | — | REQ-CAT-094 |
| create Unit e-invoice code | POST | /api/v1/cat/unit-einvoice-codes | key, einvoiceUnitCode | Unit e-invoice code | RULE-CAT-006, 043 | REQ-CAT-120 |
| update Unit e-invoice code | PUT | /api/v1/cat/unit-einvoice-codes/{id} | einvoiceUnitCode | Unit e-invoice code | RULE-CAT-006 | REQ-CAT-121 |
| deactivate Unit e-invoice code | DELETE | /api/v1/cat/unit-einvoice-codes/{id} | id | confirmation | — | REQ-CAT-122 |
| capability turn-off check | GET | /api/v1/cat/capability-checks/turn-off | flag | allowed, count, Items | RULE-CAT-010, 011 | REQ-CAT-021, 022, 023 |

# STANDALONE

## Traceability matrix

| US | REQ | AC | RULE | ENT | SCR-REQ |
|---|---|---|---|---|---|
| US-CAT-001 | REQ-CAT-001 … 007, 130, 131, 132, 135, 142 … 147 | AC-CAT-001 … 007, 132 … 134, 137 … 139, 144, 149, 154 … 156, 162 | RULE-CAT-003, 019, 026, 041, 048, 050, 053 | ENT-CAT-001, 002, 005, 006, 007, 008, 009, 010 | SCR-REQ-CAT-001 |
| US-CAT-002 | REQ-CAT-008 … 010, 133 | AC-CAT-008 … 010, 140 | RULE-CAT-049 | ENT-CAT-001, 002, 005, 007, 008, 009, 010 | SCR-REQ-CAT-001 |
| US-CAT-003 | REQ-CAT-011 … 013 | AC-CAT-011 … 013 | RULE-CAT-009 | ENT-CAT-001, 002, 003, 005, 007 | SCR-REQ-CAT-001, 003 |
| US-CAT-004 | REQ-CAT-014 … 016 | AC-CAT-014 … 017, 143 | RULE-CAT-008 | ENT-CAT-001, 002, 003, 004, 005, 007 | SCR-REQ-CAT-001, 003 |
| US-CAT-005 | REQ-CAT-017 … 020 | AC-CAT-018 … 021 | RULE-CAT-012 | ENT-CAT-001, 002, 005, 007, 009 | SCR-REQ-CAT-001 |
| US-CAT-006 | REQ-CAT-021 … 023 | AC-CAT-022 … 024 | RULE-CAT-010, 011 | ENT-CAT-001, 002, 005 | SCR-REQ-CAT-006 |
| US-CAT-007 | REQ-CAT-024 … 027, 140, 141 | AC-CAT-025 … 028, 163, 164 | RULE-CAT-001 | ENT-CAT-001, 002 | SCR-REQ-CAT-001 |
| US-CAT-008 | REQ-CAT-028 … 031 | AC-CAT-029 … 032 | RULE-CAT-001, 002 | ENT-CAT-001, 002 | SCR-REQ-CAT-001 |
| US-CAT-009 | REQ-CAT-032 … 035, 134 | AC-CAT-033 … 036, 141 | RULE-CAT-004, 005 | ENT-CAT-001, 002, 012 | SCR-REQ-CAT-001, 006 |
| US-CAT-010 | REQ-CAT-036 … 041, 139 | AC-CAT-037 … 043, 161 | RULE-CAT-003, 044, 045, 049 | ENT-CAT-001, 002, 009 | SCR-REQ-CAT-004 |
| US-CAT-011 | REQ-CAT-042 … 055, 129, 137, 138, 140, 141, 145 | AC-CAT-044 … 057, 131, 145, 146, 147, 154, 159, 160, 163, 164 | RULE-CAT-001, 013, 014, 015, 016, 017, 048, 052 | ENT-CAT-001, 002, 003, 004, 005 | SCR-REQ-CAT-001, 003 |
| US-CAT-012 | REQ-CAT-056 … 063, 143, 147 | AC-CAT-058 … 065, 134, 151, 156 | RULE-CAT-018, 019, 020, 021, 022 | ENT-CAT-001, 002, 005, 007 | SCR-REQ-CAT-001 |
| US-CAT-013 | REQ-CAT-064 … 072, 136, 142, 146 | AC-CAT-066 … 074, 133, 142, 153, 155, 157, 158 | RULE-CAT-001, 023, 024, 025, 026, 048 | ENT-CAT-001, 006 | SCR-REQ-CAT-002 |
| US-CAT-014 | REQ-CAT-073 … 076 | AC-CAT-075 … 078 | RULE-CAT-006 | ENT-CAT-001, 008, 009, 011, 012 | SCR-REQ-CAT-001 |
| US-CAT-015 | REQ-CAT-077 … 082 | AC-CAT-079 … 084 | RULE-CAT-007, 027, 028, 048 | ENT-CAT-001, 008 | SCR-REQ-CAT-001 |
| US-CAT-016 | REQ-CAT-083 … 091, 128, 135 | AC-CAT-085 … 093, 130, 135, 150 | RULE-CAT-029, 030, 031, 032, 047, 048, 051, 053 | ENT-CAT-001, 002, 008, 009, 011 | SCR-REQ-CAT-001 |
| US-CAT-017 | REQ-CAT-092 … 100 | AC-CAT-094 … 102, 152 | RULE-CAT-033, 034, 035 | ENT-CAT-009, 011, 012 | SCR-REQ-CAT-001, 006 |
| US-CAT-018 | REQ-CAT-101 … 104 | AC-CAT-103 … 106, 136 | RULE-CAT-036, 037 | ENT-CAT-002, 009 | SCR-REQ-CAT-005 |
| US-CAT-019 | REQ-CAT-105 … 112 | AC-CAT-107 … 114, 148 | RULE-CAT-001, 038, 039, 040 | ENT-CAT-002, 010 | SCR-REQ-CAT-001 |
| US-CAT-020 | REQ-CAT-113 … 116 | AC-CAT-115 … 118 | RULE-CAT-041 | ENT-CAT-001, 002 | SCR-REQ-CAT-001 |
| US-CAT-021 | REQ-CAT-095, 117 … 124, 134 | AC-CAT-097, 119 … 126, 141 | RULE-CAT-042, 043 | ENT-CAT-001, 002, 012 | SCR-REQ-CAT-001, 006 |
| US-CAT-022 | REQ-CAT-125 … 127 | AC-CAT-127 … 129 | RULE-CAT-046 | ENT-CAT-001, 009 | — (the global search box sits outside CAT and calls the catalogue search — ADR-CAT-043) |

Every story maps to ≥ 1 REQ, including ≥ 1 REQ with pattern `unwanted`. Every REQ has ≥ 1 AC. Every RULE traces to ≥ 1 REQ. Every SCR-REQ traces to REQs.

### Flag × activity coverage (CM-7, CAT cells of KB §4.2)
| Flag | Activity cells (✓ required · ○ optional) | Requirement (flag on) | Requirement (flag off) |
|---|---|---|---|
| `variants` | Electronics & appliances ○ · Apparel & footwear ✓ · Household/cosmetics ○ · Furniture & lighting ✓ · Sanitary & plumbing ✓ · Fabrics & furnishings ✓ | REQ-CAT-049, REQ-CAT-017, REQ-CAT-018 | REQ-CAT-014, REQ-CAT-016, REQ-CAT-022 |
| `bundles` | Electronics & appliances ✓ · Household/cosmetics ✓ · Power tools & hardware ✓ · Furniture & lighting ✓ | REQ-CAT-056, REQ-CAT-020 | REQ-CAT-015, REQ-CAT-016, REQ-CAT-023 |
| `dualQuantity` | Grocery ○ · Fabrics ✓ (metre) · Building materials & steel ✓ · Produce by weight ✓ | catalogue side only: REQ-CAT-077 (Item units), REQ-CAT-097 (scale labels); the flag's quantity behaviour is traced by INV | — (CAT has no flag-off behaviour; the flag is read by INV) |
| `serialTracking`, `lotExpiry`, `b2bCredit` | — | no CAT requirement; traced by SWR, INV, PRC/PTY | — |

## Decisions applied

| DEFAULT / ADR | What | Source | Override / status |
|---|---|---|---|
| D-01 | Every Item has ≥ 1 Variant (a default Variant). Stock, price, barcode and e-invoice override attach to the Variant | DP §8 P-04; registry OI-07 | Change if P-04 is decided otherwise |
| D-02 | Brand and unit of measure are mdl lookup keys CAT_BRAND / CAT_UOM owned by CAT | DP §8 P-05; PRD US-CAT-014 Source (module-registry LOOKUPS OWNED) | Make them CAT entities if attributes per brand are needed |
| D-03 | Category is a CAT tree, at most 5 levels; Items attach at any level | DP §8 P-05; US-CAT-013 | Change the depth limit |
| D-04 | Bundle composition lives in CAT (Item kind BUNDLE + component lines). No nested Bundles; a Bundle has one Variant; the kind is fixed after first save | DP §8 P-06; US-CAT-012 | Allow nesting through a later delta |
| D-05 | Barcodes: 0..n per Variant, each bound to a unit. Internal barcodes are EAN-13 with GS1 restricted-circulation prefix 04 and digits from series CAT_INTERNAL_BARCODE. Scale codes are SCALE barcodes | DP §8 P-07; module-registry OI-10 | Change the internal prefix |
| D-06 | E-invoice code type and code on the Item, with an optional override on the Variant | US-CAT-021; DP §8 P-08 | Move to Variant-only |
| D-07 | nameAr mandatory with ≥ 1 Arabic letter; nameEn optional, with nameAr used in its place | PRD P-3; DP §8 P-09 | Drop the Arabic-letter check |
| D-08 | Image files are kept in the given `file` public image storage; CAT holds order, main image, Variant binding and descriptions | DP §8 P-12; registry OI-15 | — |
| D-09 | New Item defaults: kind STANDARD, base unit PIECE, tax class STANDARD, publish state UNPUBLISHED | KB:raw-idea.md §6.1; US-CAT-007 | Change the default values |
| D-10 | Limits: 3 attributes and 300 active Variants per Item; 20 images of ≤ 5 MB; import ≤ 10 MB and ≤ 5,000 rows; search ≥ 2 characters and ≤ 20 results; 1–999 copies and ≤ 5,000 labels per request | KB:raw-idea.md §6.1, §6.10 (bounded, fast setup) | Raise any limit |
| D-11 | Number series CAT_ITEM (pattern ITM-000001), CAT_VARIANT (VAR-000001), CAT_INTERNAL_BARCODE (10 digits) | profile NUMBERING rule; US-CAT-009 | Change the patterns in the core sequence module |
| D-12 | CAT_UOM seeds PIECE, CARTON, BOX, DOZEN, KILOGRAM, GRAM, METRE, LITRE, mapped to e-invoice units EA, CT, BX, DZN, KGM, GRM, MTR, LTR | US-CAT-021 Source (Egypt ETA SDK unitType) | Re-map against the authority's published code table |
| D-13 | Two active Items cannot share one nameAr | KB:raw-idea.md §6.5 (next action: open the existing Item); US-CAT-001 | Turn into a warning |
| D-14 | Base unit and Item unit conversion factor are fixed after first save | US-CAT-015 (purchasing, selling and stock agree on quantities) | Allow change while no downstream document exists (needs a downstream check) |
| D-15 | Variant names are derived from the Item name and the value names, never entered | US-CAT-011 | Add an owner override name |
| ADR-CAT-038 | The project-registry open items affecting CAT (OI-06 … OI-12, OI-15) are applied per the approved PRD sources and DP §8 recommendations | project-registry §8; PRD | ACCEPTED (non-breaking) |
| ADR-CAT-039 | Dependent records take their parent's kind and carry the audit fields and isActiveFl | profile entity kinds | ACCEPTED (non-breaking) |
| ADR-CAT-040 | Tax class is the CAT-owned lookup CAT_TAX_CLASS; TAX maps it to rates later | KB:raw-idea.md §5 (fixed order) | ACCEPTED (non-breaking) |
| ADR-CAT-041 | Capability flags are read through SettingsApi behind FeatureFlags; CAT offers a turn-off check for the flag owner (P-02) | DP §4 #15; KB:raw-idea.md §6.2 | ACCEPTED (non-breaking) |
| ADR-CAT-042 | Scale label layout and Unit e-invoice code are CAT config entities | module-registry OI-10; US-CAT-021 | ACCEPTED (non-breaking) |
| ADR-CAT-043 | CAT provides the item search; the global search box sits outside CAT | KB:raw-idea.md §6.6 | ACCEPTED (non-breaking) |
| ADR-CAT-058 | The given copies the CAT lookup values and number series into each new tenant; `CatTenantProvisioningContributor` copies the 8 Unit e-invoice codes (REQ-CAT-134, A9) | given erp-core `modules.tenant` / `mdl` / `sequence` | ACCEPTED (non-breaking) |
| ADR-CAT-059 | Every transition into ACTIVE re-establishes the Item invariants (A7, REQ-CAT-130, RULE-CAT-050); channel reads see only active Items (REQ-CAT-086, REQ-CAT-101) | gate-analysis G6 | ACCEPTED (non-breaking) |
| ADR-CAT-060 | An Item unit used by active barcodes stays active (REQ-CAT-128, RULE-CAT-051) | gate-analysis G7 | ACCEPTED (non-breaking) |
| ADR-CAT-062 | Arabic normalisation for RULE-CAT-003 and for search | gate-analysis G18 | ACCEPTED (non-breaking) |
| ADR-CAT-063 | An inactive parent refuses child writes (REQ-CAT-135, RULE-CAT-053); deactivating a Variant also deactivates its barcodes (REQ-CAT-050) | gate-analysis round 2, G2 | ACCEPTED (non-breaking) |
| ADR-CAT-064 | The scale code length of a layout is fixed after creation; type and decimals change only how the embedded value is read; a deactivated layout leaves its SCALE barcodes stored (REQ-CAT-093, REQ-CAT-094) | gate-analysis round 2, G3 | ACCEPTED (non-breaking) |
| ADR-CAT-065 | CAT serves its own lookup keys to the screens through `OwnedLookups` (`GET /api/v1/cat/lookups/{key}`); amends ADR-CAT-050 §1 and §2 | gate-analysis round 2, G5; erp-core checklist C6 | ACCEPTED (non-breaking) |
| ADR-CAT-068 | Three repair operations stay allowed on an inactive Item — Variant generation, a categoryFk-only update and the replacement of the Bundle components (REQ-CAT-135, REQ-CAT-145 … REQ-CAT-147, RULE-CAT-053, A7); amends ADR-CAT-063 | gate-analysis round 3, G1 | ACCEPTED (non-breaking) |
| ADR-CAT-069 | Categories, Variant attributes and Variant attribute values can be reactivated (REQ-CAT-136 … REQ-CAT-138) | gate-analysis round 3, G10 | ACCEPTED (non-breaking) |
| ADR-CAT-070 | Variant attributes and their values are CAT tables, not mdl lookup types (erp-core checklist C5 / F3); CORE-GAP-tenant-lookup-types raised | gate-analysis round 3, G11 | ACCEPTED (non-breaking) |
| ADR-CAT-072 | The default Variant moves: on deactivation of the default to the remaining active Variant with the lowest code, and on generation for an Item with no active Variant to the first Variant created; an active Item always has exactly one active default Variant (REQ-CAT-025, REQ-CAT-140, REQ-CAT-141, A7, CON-CAT-002) | gate-analysis round 6, G3 | ACCEPTED (non-breaking) |
| ADR-CAT-074 | The item import saves entities (no JDBC batching); the 60-second figure for 5,000 rows is a target measured on the reference deployment, the 120-second limit is the guarantee (REQ-CAT-139); amends ADR-CAT-071 | gate-analysis round 7, G3 | ACCEPTED (non-breaking) |
| ADR-CAT-076 | Image type, size and public storage are enforced at upload by the FILE category CAT_ITEM_IMAGE; CAT checks a file reference's availability and public URL and the 20-image count (RULE-CAT-038, REQ-CAT-110); the import's byte read and type / size check (RULE-CAT-044) wait on CORE-GAP-file-document-metadata-read; amends ADR-CAT-056 §2 | gate-analysis round 8, G1; erp-core conventions §Files rule 3, modules.file | ACCEPTED (non-breaking) |

No ADR is BLOCKED. No question was raised.

## Access summary

| Page code | Screen | Owner | Seller/cashier | Warehouse staff |
|---|---|---|---|---|
| CAT_ITEMS | SCR-REQ-CAT-001 Items / الأصناف | VIEW, CREATE, UPDATE, DELETE | VIEW | VIEW |
| CAT_CATEGORIES | SCR-REQ-CAT-002 Categories / الفئات | VIEW, CREATE, UPDATE, DELETE | VIEW | VIEW |
| CAT_VARIANT_ATTRIBUTES | SCR-REQ-CAT-003 Variant attributes / خصائص المتغيّرات | VIEW, CREATE, UPDATE, DELETE | VIEW | VIEW |
| CAT_ITEM_IMPORT | SCR-REQ-CAT-004 Item import / استيراد الأصناف | VIEW, CREATE | — | — |
| CAT_BARCODE_LABELS | SCR-REQ-CAT-005 Barcode labels / ملصقات الباركود | VIEW, CREATE | — | VIEW, CREATE |
| CAT_SETTINGS | SCR-REQ-CAT-006 Catalogue settings / إعدادات الكتالوج | VIEW, CREATE, UPDATE, DELETE | — | — |

Custom roles receive actions through the given `sec` module. Permission names are derived from these page codes by the security module, and none is seeded here.
══════════════════════════════════════════════════════════════════

<<<END INPUT>>>

<<<INPUT: registry-srs>>>
## REGISTRY — P1 — CAT v2

### Entities
| ENT id | Name (en / ar) | Kind | Ownership | Status |
|---|---|---|---|---|
| ENT-CAT-001 | Item / صنف | master | SHARED (owner) | REGISTERED |
| ENT-CAT-002 | Variant / متغيّر الصنف | master | SHARED (owner) | REGISTERED |
| ENT-CAT-003 | Variant attribute / خاصية المتغيّر | lookup | PRIVATE | REGISTERED |
| ENT-CAT-004 | Variant attribute value / قيمة خاصية المتغيّر | lookup | PRIVATE | REGISTERED |
| ENT-CAT-005 | Variant attribute assignment / إسناد قيمة خاصية للمتغيّر | master (dependent) | PRIVATE | REGISTERED |
| ENT-CAT-006 | Category / فئة | master | SHARED (owner) | REGISTERED |
| ENT-CAT-007 | Bundle component / مكوّن الحزمة | master (dependent) | SHARED (owner) | REGISTERED |
| ENT-CAT-008 | Item unit / وحدة الصنف | master (dependent) | SHARED (owner) | REGISTERED |
| ENT-CAT-009 | Barcode / باركود | master (dependent) | SHARED (owner) | REGISTERED |
| ENT-CAT-010 | Item media / وسائط الصنف | master (dependent) | PRIVATE | REGISTERED |
| ENT-CAT-011 | Scale label layout / إعداد ملصق الميزان | config | PRIVATE | REGISTERED |
| ENT-CAT-012 | Unit e-invoice code / رمز الوحدة في الفاتورة الإلكترونية | config | PRIVATE | REGISTERED |

### Consumed
`module-dependencies` block: `consumes: []`. CAT consumes no business-module entity. Given uses (erp-core mdl, cu, sequence, file, events, report, sec, tenant) are listed in the SRS A9 `given-usage` block and create no graph edge.

### Lookups owned
| Key | ENT | Values count |
|---|---|---|
| CAT_ITEM_KIND | ENT-CAT-001 | 2 |
| CAT_PUBLISH_STATE | ENT-CAT-001 | 2 |
| CAT_BRAND | ENT-CAT-001 | 0 (open) |
| CAT_UOM | ENT-CAT-001, ENT-CAT-008, ENT-CAT-009, ENT-CAT-012 | 8 seeded (open) |
| CAT_TAX_CLASS | ENT-CAT-001 | 3 (open) |
| CAT_EINVOICE_ITEM_CODE_TYPE | ENT-CAT-001, ENT-CAT-002 | 2 |
| CAT_EINVOICE_UNIT | ENT-CAT-012 | 8 seeded (open) |
| CAT_BARCODE_TYPE | ENT-CAT-009 | 3 |
| CAT_SCALE_VALUE_TYPE | ENT-CAT-011 | 2 |
| CAT_CHANGE_KIND | catalogue-change event | 4 |

Lookups consumed: none.

### Screens
| SCR-REQ id | Name (en / ar) | Page code |
|---|---|---|
| SCR-REQ-CAT-001 | Items / الأصناف | CAT_ITEMS |
| SCR-REQ-CAT-002 | Categories / الفئات | CAT_CATEGORIES |
| SCR-REQ-CAT-003 | Variant attributes / خصائص المتغيّرات | CAT_VARIANT_ATTRIBUTES |
| SCR-REQ-CAT-004 | Item import / استيراد الأصناف | CAT_ITEM_IMPORT |
| SCR-REQ-CAT-005 | Barcode labels / ملصقات الباركود | CAT_BARCODE_LABELS |
| SCR-REQ-CAT-006 | Catalogue settings / إعدادات الكتالوج | CAT_SETTINGS |

### Requirements
- REQ count 147 · AC count 164 · RULE count 53 · ENT count 12 · SCR-REQ count 6
- Last sequence per atom: REQ: 147, AC: 164, ENT: 12, RULE: 53, SCR-REQ: 6

REQ ids: REQ-CAT-001, REQ-CAT-002, REQ-CAT-003, REQ-CAT-004, REQ-CAT-005, REQ-CAT-006, REQ-CAT-007, REQ-CAT-008, REQ-CAT-009, REQ-CAT-010, REQ-CAT-011, REQ-CAT-012, REQ-CAT-013, REQ-CAT-014, REQ-CAT-015, REQ-CAT-016, REQ-CAT-017, REQ-CAT-018, REQ-CAT-019, REQ-CAT-020, REQ-CAT-021, REQ-CAT-022, REQ-CAT-023, REQ-CAT-024, REQ-CAT-025, REQ-CAT-026, REQ-CAT-027, REQ-CAT-028, REQ-CAT-029, REQ-CAT-030, REQ-CAT-031, REQ-CAT-032, REQ-CAT-033, REQ-CAT-034, REQ-CAT-035, REQ-CAT-036, REQ-CAT-037, REQ-CAT-038, REQ-CAT-039, REQ-CAT-040, REQ-CAT-041, REQ-CAT-042, REQ-CAT-043, REQ-CAT-044, REQ-CAT-045, REQ-CAT-046, REQ-CAT-047, REQ-CAT-048, REQ-CAT-049, REQ-CAT-050, REQ-CAT-051, REQ-CAT-052, REQ-CAT-053, REQ-CAT-054, REQ-CAT-055, REQ-CAT-056, REQ-CAT-057, REQ-CAT-058, REQ-CAT-059, REQ-CAT-060, REQ-CAT-061, REQ-CAT-062, REQ-CAT-063, REQ-CAT-064, REQ-CAT-065, REQ-CAT-066, REQ-CAT-067, REQ-CAT-068, REQ-CAT-069, REQ-CAT-070, REQ-CAT-071, REQ-CAT-072, REQ-CAT-073, REQ-CAT-074, REQ-CAT-075, REQ-CAT-076, REQ-CAT-077, REQ-CAT-078, REQ-CAT-079, REQ-CAT-080, REQ-CAT-081, REQ-CAT-082, REQ-CAT-083, REQ-CAT-084, REQ-CAT-085, REQ-CAT-086, REQ-CAT-087, REQ-CAT-088, REQ-CAT-089, REQ-CAT-090, REQ-CAT-091, REQ-CAT-092, REQ-CAT-093, REQ-CAT-094, REQ-CAT-095, REQ-CAT-096, REQ-CAT-097, REQ-CAT-098, REQ-CAT-099, REQ-CAT-100, REQ-CAT-101, REQ-CAT-102, REQ-CAT-103, REQ-CAT-104, REQ-CAT-105, REQ-CAT-106, REQ-CAT-107, REQ-CAT-108, REQ-CAT-109, REQ-CAT-110, REQ-CAT-111, REQ-CAT-112, REQ-CAT-113, REQ-CAT-114, REQ-CAT-115, REQ-CAT-116, REQ-CAT-117, REQ-CAT-118, REQ-CAT-119, REQ-CAT-120, REQ-CAT-121, REQ-CAT-122, REQ-CAT-123, REQ-CAT-124, REQ-CAT-125, REQ-CAT-126, REQ-CAT-127, REQ-CAT-128, REQ-CAT-129, REQ-CAT-130, REQ-CAT-131, REQ-CAT-132, REQ-CAT-133, REQ-CAT-134, REQ-CAT-135, REQ-CAT-136, REQ-CAT-137, REQ-CAT-138, REQ-CAT-139, REQ-CAT-140, REQ-CAT-141, REQ-CAT-142, REQ-CAT-143, REQ-CAT-144, REQ-CAT-145, REQ-CAT-146, REQ-CAT-147

AC ids: AC-CAT-001, AC-CAT-002, AC-CAT-003, AC-CAT-004, AC-CAT-005, AC-CAT-006, AC-CAT-007, AC-CAT-008, AC-CAT-009, AC-CAT-010, AC-CAT-011, AC-CAT-012, AC-CAT-013, AC-CAT-014, AC-CAT-015, AC-CAT-016, AC-CAT-017, AC-CAT-018, AC-CAT-019, AC-CAT-020, AC-CAT-021, AC-CAT-022, AC-CAT-023, AC-CAT-024, AC-CAT-025, AC-CAT-026, AC-CAT-027, AC-CAT-028, AC-CAT-029, AC-CAT-030, AC-CAT-031, AC-CAT-032, AC-CAT-033, AC-CAT-034, AC-CAT-035, AC-CAT-036, AC-CAT-037, AC-CAT-038, AC-CAT-039, AC-CAT-040, AC-CAT-041, AC-CAT-042, AC-CAT-043, AC-CAT-044, AC-CAT-045, AC-CAT-046, AC-CAT-047, AC-CAT-048, AC-CAT-049, AC-CAT-050, AC-CAT-051, AC-CAT-052, AC-CAT-053, AC-CAT-054, AC-CAT-055, AC-CAT-056, AC-CAT-057, AC-CAT-058, AC-CAT-059, AC-CAT-060, AC-CAT-061, AC-CAT-062, AC-CAT-063, AC-CAT-064, AC-CAT-065, AC-CAT-066, AC-CAT-067, AC-CAT-068, AC-CAT-069, AC-CAT-070, AC-CAT-071, AC-CAT-072, AC-CAT-073, AC-CAT-074, AC-CAT-075, AC-CAT-076, AC-CAT-077, AC-CAT-078, AC-CAT-079, AC-CAT-080, AC-CAT-081, AC-CAT-082, AC-CAT-083, AC-CAT-084, AC-CAT-085, AC-CAT-086, AC-CAT-087, AC-CAT-088, AC-CAT-089, AC-CAT-090, AC-CAT-091, AC-CAT-092, AC-CAT-093, AC-CAT-094, AC-CAT-095, AC-CAT-096, AC-CAT-097, AC-CAT-098, AC-CAT-099, AC-CAT-100, AC-CAT-101, AC-CAT-102, AC-CAT-103, AC-CAT-104, AC-CAT-105, AC-CAT-106, AC-CAT-107, AC-CAT-108, AC-CAT-109, AC-CAT-110, AC-CAT-111, AC-CAT-112, AC-CAT-113, AC-CAT-114, AC-CAT-115, AC-CAT-116, AC-CAT-117, AC-CAT-118, AC-CAT-119, AC-CAT-120, AC-CAT-121, AC-CAT-122, AC-CAT-123, AC-CAT-124, AC-CAT-125, AC-CAT-126, AC-CAT-127, AC-CAT-128, AC-CAT-129, AC-CAT-130, AC-CAT-131, AC-CAT-132, AC-CAT-133, AC-CAT-134, AC-CAT-135, AC-CAT-136, AC-CAT-137, AC-CAT-138, AC-CAT-139, AC-CAT-140, AC-CAT-141, AC-CAT-142, AC-CAT-143, AC-CAT-144, AC-CAT-145, AC-CAT-146, AC-CAT-147, AC-CAT-148, AC-CAT-149, AC-CAT-150, AC-CAT-151, AC-CAT-152, AC-CAT-153, AC-CAT-154, AC-CAT-155, AC-CAT-156, AC-CAT-157, AC-CAT-158, AC-CAT-159, AC-CAT-160, AC-CAT-161, AC-CAT-162, AC-CAT-163, AC-CAT-164

RULE ids: RULE-CAT-001, RULE-CAT-002, RULE-CAT-003, RULE-CAT-004, RULE-CAT-005, RULE-CAT-006, RULE-CAT-007, RULE-CAT-008, RULE-CAT-009, RULE-CAT-010, RULE-CAT-011, RULE-CAT-012, RULE-CAT-013, RULE-CAT-014, RULE-CAT-015, RULE-CAT-016, RULE-CAT-017, RULE-CAT-018, RULE-CAT-019, RULE-CAT-020, RULE-CAT-021, RULE-CAT-022, RULE-CAT-023, RULE-CAT-024, RULE-CAT-025, RULE-CAT-026, RULE-CAT-027, RULE-CAT-028, RULE-CAT-029, RULE-CAT-030, RULE-CAT-031, RULE-CAT-032, RULE-CAT-033, RULE-CAT-034, RULE-CAT-035, RULE-CAT-036, RULE-CAT-037, RULE-CAT-038, RULE-CAT-039, RULE-CAT-040, RULE-CAT-041, RULE-CAT-042, RULE-CAT-043, RULE-CAT-044, RULE-CAT-045, RULE-CAT-046, RULE-CAT-047, RULE-CAT-048, RULE-CAT-049, RULE-CAT-050, RULE-CAT-051, RULE-CAT-052, RULE-CAT-053

### Decisions
ADR-CAT-038, ADR-CAT-039, ADR-CAT-040, ADR-CAT-041, ADR-CAT-042, ADR-CAT-043; at the gate-analysis revise ADR-CAT-058, ADR-CAT-059, ADR-CAT-060, ADR-CAT-062; at the second gate-analysis revise ADR-CAT-063, ADR-CAT-064, ADR-CAT-065; at the third gate-analysis revise ADR-CAT-068, ADR-CAT-069, ADR-CAT-070; at the sixth gate-analysis revise ADR-CAT-072; at the seventh gate-analysis revise ADR-CAT-074 (ADR-CAT-073 and ADR-CAT-075 were raised by the same revise for P3.1 / P3.2); at the eighth gate-analysis revise ADR-CAT-076 (RULE-CAT-038, REQ-CAT-110, A9). None BLOCKED. Next free ADR: ADR-CAT-077.

### Event
"P1 completed: CAT v2 — REQ 147 · AC 164 · ENT 12 · RULE 53 · SCR-REQ 6 · ADR 19 (038–043, 058–060, 062–065, 068–070, 072, 074, 076; none BLOCKED)"

<<<END INPUT>>>

<<<INPUT: contract>>>
# CONTRACT — Catalog / الكتالوج (CAT) — outbound promise
══════════════════════════════════════════════════════════════════
Module : CAT   Version : v2   Profile : commerce   Stage : P1.5
Inputs : srs (P1, CAT v2 — REQ 147 · AC 164 · ENT 12 · RULE 53), registry-srs (re-stamped from the current registries at the eighth gate-analysis revise; CON-CAT-002 wording amended by ADR-CAT-072; §4 given-usage repeated from SRS A9 as amended by ADR-CAT-076)
Counts : CON 18 (entity items 6 · operation items 11 · event item 1)
Status : DRAFT — P1.5 pass 1 · no question raised · no ADR raised
══════════════════════════════════════════════════════════════════

## 0 — Scope of this promise

This contract states what other modules may depend on in CAT before CAT is built. It is a list of promises. It contains no implementation, no table, no path and no verb. The backend stage (P3.1) chooses those and marks each implementing block with `Honours: CON-CAT-…`.

| Item | Decided by |
|---|---|
| Consumers named in the SRS | PRC, INV, PUR, SLS, SFR, INS and TAX (A3 "Cross-module" column). Also the capability-flag owner (P-02, ADR-CAT-041) and the global search host (ADR-CAT-043) |
| Entities promised | Every SHARED (owner) ENT of the SRS: ENT-CAT-001, 002, 006, 007, 008, 009 |
| Entities not promised | The PRIVATE ENTs ENT-CAT-003, 004, 005, 010, 011, 012. Consumers see their data only through the operations below, as the SRS A3 states ("read through Variant reads", "SFR reads images through Item and Variant reads", "read through Variant and Item unit reads by SLS and TAX") |
| Operations promised | The cross-module reads listed in SRS SCR-REQ-CAT-001 B5 (read Item / Variant by identifier, storefront catalogue read, Items of a Category, barcode lookup). Also the reads that the SRS A3 gives to consumers (Category for SFR/INS, Item unit for INV/PUR/SLS, Variant images for SFR), the catalogue search (ADR-CAT-043) and the turn-off check (ADR-CAT-041) |
| Event promised | The catalogue-change event of REQ-CAT-008 / RULE-CAT-049. Downstream modules react to it ("Downstream modules react to catalogue changes without CAT reading them"). The SRS states this, so it is not an open point |

**Promises that apply to every item:**
- **Identifiers.** Every identifier is a BIGINT identity key that the database assigns (stack.db.pk_generation = identity). A consumer's HARD-FK column to a CAT entity is `BIGINT`. An identifier is never reused or re-assigned.
- **Deactivation (RULE-CAT-048, ARCH-5).** A SHARED record is never deleted. Deactivation sets `isActiveFl` to false. The record stays readable by its identifier, leaves new selections, and raises a catalogue-change event with change kind DEACTIVATED. CAT notifies consumers and never cascades into them.
- **Tenancy.** The given core resolves the tenant. Every operation answers within the caller's tenant, and a record of another tenant reads as *not found*.
- **Names.** `nameAr` is always present. Where `nameEn` is empty, the English display returns `nameAr` (REQ-CAT-029).
- **Lookup codes.** Lookup-backed values are returned as codes of the CAT-owned `mdl` keys (SRS A6). Consumers resolve labels through the core lookup module. A code that is deactivated later stays on existing records (REQ-CAT-076).
- **Authorization of in-process calls (ADR-CAT-061).** The operation items are in-process calls. CAT does not check a CAT permission on them: the **calling module authorizes its own caller** against its own permissions before it calls CAT (for example SFR for a storefront shopper, SLS for a cashier). The one exception is the catalogue search (CON-CAT-016), which answers an empty result to a user without VIEW on CAT_ITEMS. Tenancy still applies to every call. Checklist C13 states no exception for an in-process read, so this rule stands on condition: CAT has raised `CORE-GAP-in-process-authorization.md`, and ADR-CAT-066 follows the core's answer (for example a core-provided caller authority for in-process reads) without changing these signatures.
- **Obligation of the capability-flag writer (POL-CAT-005).** A write that turns `variants` or `bundles` off must first call CON-CAT-017 and must refuse the write when `allowed` is false, showing the returned message and dependent Items. CAT answers the check and never writes a flag. The writer (owner OPEN, P-02) does not yet honour this, so it is recorded as an OPEN platform finding in `business-policies-cat.md` (PLATFORM FINDINGS, row 2).

---

## 1 — Entity items

### CON-CAT-001 — Item (صنف / Item)

Entity    : ENT-CAT-001 — key columns: code (business key, series CAT_ITEM, read-only), itemPk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-001, REQ-CAT-001, REQ-CAT-032, REQ-CAT-034

Consumers store `itemPk`. `code` is unique per tenant and never changes (RULE-CAT-004). The values of `itemKindCode` (STANDARD / BUNDLE) and `baseUomCode` are fixed after first save (RULE-CAT-022, RULE-CAT-007).

### CON-CAT-002 — Variant (متغيّر الصنف / Variant)

Entity    : ENT-CAT-002 — key columns: code (business key, series CAT_VARIANT, read-only), variantPk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-002, REQ-CAT-025, REQ-CAT-140, REQ-CAT-141, REQ-CAT-033, REQ-CAT-017

Stock, price, barcode and the e-invoice override attach to the Variant (D-01). Every Item has at least one Variant, and an active Item always has exactly one active default Variant (`isDefaultFl` true), the Variant stock and price consumers fall back to. When the default Variant is deactivated, the flag moves to the Item's remaining active Variant with the lowest code; when Variants are generated for an Item with no active Variant, the first Variant created becomes the default (REQ-CAT-025, ADR-CAT-072). A consumer therefore reads the default from the Variant reads and never assumes it is the Variant created with the Item. Turning `variants` on never changes an existing `variantPk`, code or barcode (REQ-CAT-017).

### CON-CAT-003 — Category (فئة / Category)

Entity    : ENT-CAT-006 — key columns: code (business key, unique per tenant, fixed after creation), categoryPk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-006, REQ-CAT-064, REQ-CAT-067

The Category tree is at most 5 levels deep and has no cycles (RULE-CAT-023).

### CON-CAT-004 — Item unit (وحدة الصنف / Item unit)

Entity    : ENT-CAT-008 — key columns: itemFk + uomCode (business key among the Item's active units), itemUnitPk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-008, REQ-CAT-077, REQ-CAT-081

`conversionFactor` is the number of base units in one unit of this kind (decimal, 6 places). It is fixed after saving (RULE-CAT-028), so a quantity that a consumer recorded keeps its meaning.

### CON-CAT-005 — Barcode (باركود / Barcode)

Entity    : ENT-CAT-009 — key columns: barcodeValue (business key, unique among the tenant's active barcodes, SCALE compared only with SCALE), barcodePk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-009, REQ-CAT-083, REQ-CAT-087

Every barcode identifies one Variant in one unit (`uomCode`).

### CON-CAT-006 — Bundle component (مكوّن الحزمة / Bundle component)

Entity    : ENT-CAT-007 — key columns: bundleItemFk + componentVariantFk (business key among the Bundle's active lines), bundleComponentPk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-007, REQ-CAT-056, REQ-CAT-058

A Bundle keeps no stock of its own; consumers count stock on the component Variants (DP §8 P-06). Component Variants always belong to STANDARD Items, and Bundles are never nested (RULE-CAT-019).

---

## 2 — Operation items

### CON-CAT-007 — Read an Item (قراءة صنف / Read an Item)
Signature : getItem(itemPk: BIGINT) → Item {itemPk, code, nameAr, nameEn, itemKindCode, baseUomCode, categoryFk, brandCode, taxClassCode, einvoiceCodeTypeCode, einvoiceCode, publishStateCode, isActiveFl; active Variants with their attribute values; Item units; barcodes; Bundle component lines (componentVariantFk, Variant code, quantity); active media} · errors: not found
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, REQ-CAT-001, REQ-CAT-002, REQ-CAT-010, REQ-CAT-058

An inactive Item is still returned, with `isActiveFl` false. Every read that starts after a commit returns that commit's state (REQ-CAT-010).

### CON-CAT-008 — Read a Variant (قراءة متغيّر / Read a Variant)
Signature : getVariant(variantPk: BIGINT) → Variant {variantPk, itemFk, code, nameAr, nameEn (derived, D-15), isDefaultFl, isActiveFl, Item baseUomCode, Item taxClassCode, effective einvoiceCodeTypeCode and einvoiceCode (Variant override, else Item), einvoiceUnitCode of the base unit (empty when the unit has no active Unit e-invoice code)} · errors: not found
Entity    : ENT-CAT-002
Traces    : ENT-CAT-002, REQ-CAT-050, REQ-CAT-119, REQ-CAT-122, REQ-CAT-029

An inactive Variant is still returned, with `isActiveFl` false (REQ-CAT-050).

### CON-CAT-009 — Read an Item unit (قراءة وحدة صنف / Read an Item unit)
Signature : getItemUnit(itemUnitPk: BIGINT) → Item unit {itemUnitPk, itemFk, uomCode, conversionFactor, isPurchaseFl, isSalesFl, isActiveFl, einvoiceUnitCode (empty when none is active)} · errors: not found
Entity    : ENT-CAT-008
Traces    : ENT-CAT-008, REQ-CAT-079, REQ-CAT-122

### CON-CAT-010 — Look up a barcode or scale label (البحث بالباركود أو ملصق الميزان / Barcode and scale label lookup)
Signature : lookupBarcode(value: text) → {variantPk, itemPk, uomCode, embeddedValue (weight or price, with the layout's decimals; empty for a plain barcode), embeddedValueTypeCode (WEIGHT / PRICE; empty for a plain barcode)} · errors: not found — no active barcode or scale layout matches (RULE-CAT-047); scale label not readable — wrong check digit or unknown scale code (RULE-CAT-035)
Entity    : ENT-CAT-009
Traces    : ENT-CAT-009, REQ-CAT-086, REQ-CAT-090, REQ-CAT-097, REQ-CAT-100

Only active barcodes of active Variants of active Items, and active scale label layouts, match (REQ-CAT-085, REQ-CAT-094, REQ-CAT-086 — ADR-CAT-059).

### CON-CAT-011 — Read a Category (قراءة فئة / Read a Category)
Signature : getCategory(categoryPk: BIGINT) → Category {categoryPk, code, nameAr, nameEn, parentCategoryFk, sortOrder, isActiveFl} · errors: not found
Entity    : ENT-CAT-006
Traces    : ENT-CAT-006, REQ-CAT-066, REQ-CAT-067

### CON-CAT-012 — Category tree (شجرة الفئات / Category tree)
Signature : getCategoryTree(activeOnly: flag) → tree of Category, each node under its parent and ordered by sortOrder · errors: none
Entity    : ENT-CAT-006
Traces    : ENT-CAT-006, REQ-CAT-067

### CON-CAT-013 — Items of a Category (أصناف الفئة / Items of a Category)
Signature : listCategoryItems(categoryPk: BIGINT, page, size) → page of active Items of the Category and of its active sub-categories · errors: not found
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, ENT-CAT-006, REQ-CAT-068

### CON-CAT-014 — Storefront catalogue read (كتالوج المتجر الإلكتروني / Storefront catalogue)
Signature : listStorefrontItems(page, size) → page of Items with isActiveFl true and publishStateCode PUBLISHED · errors: none
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, REQ-CAT-115, REQ-CAT-113, REQ-CAT-114

### CON-CAT-015 — Images of a Variant (صور المتغيّر / Images of a Variant)
Signature : getVariantImages(variantPk: BIGINT) → list of image {fileDocumentRef, nameAr, nameEn, isPrimaryFl, sortOrder} — the active images bound to the Variant, or else the Item's active images bound to no Variant, primary first · errors: not found
Entity    : ENT-CAT-002
Traces    : ENT-CAT-002, REQ-CAT-109

`fileDocumentRef` points to the given `file` public image storage (`erp-core:modules.file`).

### CON-CAT-016 — Catalogue search for the global search (بحث الكتالوج / Catalogue search)
Signature : searchCatalogue(term: text) → at most 20 active Items {itemPk, code, nameAr, nameEn} whose nameAr, nameEn, code or active barcode contains the term, exact code or barcode matches first · errors: term too short — fewer than 2 characters (RULE-CAT-046); forbidden — the caller lacks VIEW on CAT_ITEMS, answered as an empty result
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, REQ-CAT-125, REQ-CAT-126, REQ-CAT-127

The global search box sits outside CAT (ADR-CAT-043).

### CON-CAT-017 — Capability turn-off check (فحص إيقاف القدرة / Capability turn-off check)
Signature : checkCapabilityTurnOff(flag: `variants` | `bundles`) → {allowed: flag, dependentCount: number, dependentItems: at most 20 {itemPk, code, nameAr}, messageAr and messageEn (the RULE-CAT-010 or RULE-CAT-011 message with {count} filled when not allowed; empty when allowed)} · errors: invalid input — flag other than `variants` or `bundles`
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, REQ-CAT-021, REQ-CAT-022, REQ-CAT-023

This check is offered to the capability-flag owner (owner OPEN, P-02 — ADR-CAT-041). CAT does not own or change the flags.

---

## 3 — Event item

### CON-CAT-018 — Catalogue-change event (إشعار تغيير الكتالوج / Catalogue-change event)
Signature : catalogueChanged(entity: ENT id of the changed record — ENT-CAT-001, 002, 005, 007, 008, 009 or 010; recordPk: BIGINT; itemPk: BIGINT; changeKind: CAT_CHANGE_KIND — CREATED / UPDATED / DEACTIVATED / REACTIVATED) → one event per committed change, delivered after commit · errors: none — a rolled-back change publishes no event (RULE-CAT-049)
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, ENT-CAT-002, REQ-CAT-008, REQ-CAT-009

The event is recorded in the same transaction as the change through the given `events` outbox, and it is delivered only after commit. Consumers read the current state through CON-CAT-007 … CON-CAT-015; the event carries no field values.

---

## 4 — Given usage (repeated verbatim from SRS A9)

```yaml name=given-usage
rows:
  - {given: erp-core, module: modules.mdl, via: com.erp.mdl.crossmodule.MdlLookupApi, purpose: "load the values of the CAT_* lookup keys (brand, unit of measure, tax class, e-invoice codes, barcode type, scale value type, item kind, publish state) at runtime"}
  - {given: erp-core, module: modules.mdl, via: com.erp.mdl.crossmodule.MdlLookupApi, purpose: "serve CAT's own lookup keys to the screens (API-CAT-052) through the common owned-lookups capability, com.erp.common.lookup.OwnedLookups.read(key, the 10 CAT keys, CAT-404-LOOKUP-KEY-UNKNOWN, MdlLookupApi::readActiveValuesByKey) — never a pass-through for other keys (ADR-CAT-065); erp-core's manifest lists owned-lookups under common but publishes no interface for it, so the row names the mdl interface the call goes through"}
  - {given: erp-core, module: modules.cu, via: com.erp.cu.crossmodule.SettingsApi, purpose: "read the `variants` and `bundles` capability flags behind the FeatureFlags API (ADR-CAT-041)"}
  - {given: erp-core, module: modules.sequence, via: com.erp.sequence.crossmodule.NumberSeriesApi, purpose: "issue Item codes (CAT_ITEM), Variant codes (CAT_VARIANT) and internal barcode digits (CAT_INTERNAL_BARCODE)"}
  - {given: erp-core, module: modules.file, via: docs/api-docs/file/index.md, purpose: "the client uploads Item images to the FILE category CAT_ITEM_IMAGE (allowPublic = true, PNG / JPEG / WEBP, at most 5 MB — the FILE endpoint refuses any other file, ADR-CAT-076) and import files to the private storage through the given FILE upload endpoints, and passes the returned FILE_DOCUMENT.ID to CAT; CAT has no multipart endpoint (ADR-CAT-056)"}
  - {given: erp-core, module: modules.file, via: com.erp.file.crossmodule.FileDocumentLookupApi, purpose: "the published methods only: isAvailable (a referenced file exists and is available in the caller's tenant — API-CAT-018, API-CAT-049), publicUrl and publicUrls (an image has a public URL, and image URLs for reads); the type, size and bytes of an import file are not published and wait on CORE-GAP-file-document-metadata-read (ADR-CAT-076)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.OutboxPublisher, purpose: "record catalogue-change events in the change transaction and deliver them after commit (RULE-CAT-049)"}
  - {given: erp-core, module: modules.report, via: com.erp.report.crossmodule.PrintApi, purpose: "produce the printable barcode label document (REQ-CAT-102) and the xlsx item import sheet (REQ-CAT-036)"}
  - {given: erp-core, module: modules.report, via: com.erp.report.ReportProvider, purpose: "register the catalogue reports CAT_BARCODE_LABEL and CAT_ITEM_IMPORT_SHEET (ADR-CAT-057)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.permission.PermissionContributor, purpose: "register the CAT page codes so the security module derives their VIEW/CREATE/UPDATE/DELETE permissions"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantProvisioningContributor, purpose: "copy the 8 seeded Unit e-invoice codes into each newly provisioned tenant and register the FILE category CAT_ITEM_IMAGE there (ADR-CAT-076); the given copies the CAT lookup values and number series itself (ADR-CAT-058, REQ-CAT-134)"}
```

The contract does not promise any entity that a given provides. Tenant, User, Role, LookupType/LookupValue, FileDocument and NumberSeries stay with `erp-core` [C:C-GIVEN-1]. A new tenant receives the CAT seeds through the given provisioning (REQ-CAT-134, ADR-CAT-058), so every operation item works in every tenant from its first request.

---

## 5 — Stability

- The items above are ADDITIVE in CS-CAT-001. Later versions may add items freely.
- Changing or removing any CON-CAT item, a key column, or the BIGINT identifier type is legal only in a BREAKING change set with an ADR. `gov.py graph` then raises a resolution event on every consumer edge that cites the item.

## 6 — Self-check

- [x] Every SHARED ENT of the SRS has an entity item (ENT-CAT-001, 002, 006, 007, 008, 009). No consumer's A8 block exists yet that names any other CAT entity, and no PRIVATE entity is promised.
- [x] Every operation item has a `Signature` line and names no path or verb.
- [x] Every item traces to at least one ENT and one REQ (C13.5).
- [x] No question was raised. The CON sequence starts at 001 because the current state holds no earlier contract, and it runs 001 … 018 without gaps (next free: CON-CAT-019). The contract itself raised no ADR; ADR-CAT-072 (gate-analysis round 6) amends the wording of CON-CAT-002 without changing its key columns or identifier type. Per the current registries the next free ADR is ADR-CAT-077.
- [x] §4 carries the SRS `given-usage` rows verbatim, in the same order.
- [x] Every item title carries ar and en.

<<<END INPUT>>>

<<<INPUT: db-script (baseline — continue it, never restart it)>>>
﻿# DB SCRIPT — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module : CAT   Version : v2   Profile : commerce   Stage : P2
Inputs : srs (P1, CAT v2 — REQ 147 · AC 164 · ENT 12 · RULE 53), registry-srs, contract (P1.5, CON 18) — re-stamped from the current registries at the eighth gate-analysis revise
Counts : tables 12 · DBF 161 · XM 0 · intra-module FK 13 · tenant FK 12 (given CORE_TENANT — ADR-CAT-055) · lookup keys 10 (34 seeded values)
Status : DRAFT — P2 pass 1 · no question raised · no ADR raised
══════════════════════════════════════════════════════════════════

## 1 — Header

| Item | Value |
|---|---|
| Module | CAT — Catalog / الكتالوج |
| Version | v2 — change set CS-CAT-001 (ADDITIVE). The current state holds no earlier db-script, so this migration creates every CAT object. The DBF sequence starts at 001, and no XM exists (SRS A8 `consumes: []`) |
| Dialect | postgresql16 (profile `stack.db.target_dialect`) |
| Schema prefix | none. No object is schema-qualified (S-7) |
| PK generation | `identity`: every PK column is `BIGINT GENERATED ALWAYS AS IDENTITY`. No sequence, no trigger, no PK default |
| Identifier transformation | The logical camelCase name (SRS A3) becomes UPPER_SNAKE_CASE: an underscore goes before each capital and the result is upper case, unquoted. Examples: `itemPk` → `ITEM_PK`, `isActiveFl` → `IS_ACTIVE_FL`, `einvoiceCodeTypeCode` → `EINVOICE_CODE_TYPE_CODE`. PostgreSQL folds unquoted names to lower case, which gives the same physical name as the given's UPPER_SNAKE convention and the default JPA physical naming. Table name = `CAT_` + entity abbreviation. Every identifier is at most 63 characters, and none is a reserved word (`KEY` and `VERSION` are non-reserved in PostgreSQL 16) |
| Type declared here (not in the syntax map) | logical `number` → `INTEGER` (sortOrder, scaleCodeLength, valueDecimals). The profile `syntax_map` has no integer entry, so it is declared once here |
| Date | 2026-10-26 |
| Prepared by | claude:opus (analysis lane) |
| Decisions | 18 DEFAULT (D-P2-01 … D-P2-18, §5). ADR-CAT-054 (SUPERSEDED by ADR-CAT-055), ADR-CAT-055, ADR-CAT-058, ADR-CAT-062, ADR-CAT-067, ADR-CAT-072 (the default Variant moves; `UQ_CAT_VARIANT_DEFAULT` kept, comments only). The next free ADR sequence, per the current registries, is 077 (ADR-CAT-076, gate-analysis round 8, changes no database object) |
| Migration file | `V1000__cat_catalog.sql` in the application's `classpath:db/migration` (erp-core conventions § Migrations: `V1000+`, `V<N>__<module>_<snake_case>.sql`). It is the first CAT script; a later change is a new `V1001+` script, never an edit |

## 2 — DB field traceability matrix

```yaml name=dbf-matrix
rows:
  # ── CAT_ITEM — ENT-CAT-001 Item (master) ──
  - {id: DBF-CAT-001, table: CAT_ITEM, column: ITEM_PK, type: "BIGINT", entity_field: ENT-CAT-001.itemPk, traces: [ENT-CAT-001, REQ-CAT-001, REQ-CAT-002], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-002, table: CAT_ITEM, column: CODE, type: "VARCHAR(20)", entity_field: ENT-CAT-001.code, traces: [ENT-CAT-001, REQ-CAT-032, REQ-CAT-034], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-003, table: CAT_ITEM, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-001.nameAr, traces: [ENT-CAT-001, REQ-CAT-024, REQ-CAT-027, REQ-CAT-028, REQ-CAT-030, REQ-CAT-007], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-004, table: CAT_ITEM, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-001.nameEn, traces: [ENT-CAT-001, REQ-CAT-029, REQ-CAT-031], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-005, table: CAT_ITEM, column: ITEM_KIND_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.itemKindCode, traces: [ENT-CAT-001, REQ-CAT-024, REQ-CAT-063, REQ-CAT-015], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-006, table: CAT_ITEM, column: BASE_UOM_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.baseUomCode, traces: [ENT-CAT-001, REQ-CAT-024, REQ-CAT-074, REQ-CAT-082], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-007, table: CAT_ITEM, column: BRAND_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.brandCode, traces: [ENT-CAT-001, REQ-CAT-004, REQ-CAT-073, REQ-CAT-076], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-008, table: CAT_ITEM, column: TAX_CLASS_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.taxClassCode, traces: [ENT-CAT-001, REQ-CAT-024, REQ-CAT-119], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-009, table: CAT_ITEM, column: EINVOICE_CODE_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.einvoiceCodeTypeCode, traces: [ENT-CAT-001, REQ-CAT-117, REQ-CAT-123], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-010, table: CAT_ITEM, column: EINVOICE_CODE, type: "VARCHAR(100)", entity_field: ENT-CAT-001.einvoiceCode, traces: [ENT-CAT-001, REQ-CAT-117, REQ-CAT-123], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-011, table: CAT_ITEM, column: DESCRIPTION_AR, type: "VARCHAR(2000)", entity_field: ENT-CAT-001.descriptionAr, traces: [ENT-CAT-001, REQ-CAT-004], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-012, table: CAT_ITEM, column: DESCRIPTION_EN, type: "VARCHAR(2000)", entity_field: ENT-CAT-001.descriptionEn, traces: [ENT-CAT-001, REQ-CAT-004], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-013, table: CAT_ITEM, column: PUBLISH_STATE_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-001.publishStateCode, traces: [ENT-CAT-001, REQ-CAT-005, REQ-CAT-113, REQ-CAT-114, REQ-CAT-115], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-014, table: CAT_ITEM, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-001.isActiveFl, traces: [ENT-CAT-001, REQ-CAT-005, REQ-CAT-006], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-015, table: CAT_ITEM, column: CATEGORY_FK, type: "BIGINT", entity_field: ENT-CAT-001.categoryFk, traces: [ENT-CAT-001, REQ-CAT-068, REQ-CAT-072], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-016, table: CAT_ITEM, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-001.tenantId, traces: [ENT-CAT-001, REQ-CAT-001], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-017, table: CAT_ITEM, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-001.version, traces: [ENT-CAT-001, REQ-CAT-010], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-018, table: CAT_ITEM, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-001.createdBy, traces: [ENT-CAT-001, REQ-CAT-024], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-019, table: CAT_ITEM, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-001.createdAt, traces: [ENT-CAT-001, REQ-CAT-024], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-020, table: CAT_ITEM, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-001.updatedBy, traces: [ENT-CAT-001, REQ-CAT-004], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-021, table: CAT_ITEM, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-001.updatedAt, traces: [ENT-CAT-001, REQ-CAT-017], nullable: "NULL", default: "—"}
  # ── CAT_VARIANT — ENT-CAT-002 Variant (master; nameAr / nameEn derived at read, D-P2-08) ──
  - {id: DBF-CAT-022, table: CAT_VARIANT, column: VARIANT_PK, type: "BIGINT", entity_field: ENT-CAT-002.variantPk, traces: [ENT-CAT-002, REQ-CAT-025, REQ-CAT-050], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-023, table: CAT_VARIANT, column: CODE, type: "VARCHAR(20)", entity_field: ENT-CAT-002.code, traces: [ENT-CAT-002, REQ-CAT-033, REQ-CAT-034], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-024, table: CAT_VARIANT, column: IS_DEFAULT_FL, type: "BOOLEAN", entity_field: ENT-CAT-002.isDefaultFl, traces: [ENT-CAT-002, REQ-CAT-025, REQ-CAT-140, REQ-CAT-141, REQ-CAT-018], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-CAT-025, table: CAT_VARIANT, column: EINVOICE_CODE_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-002.einvoiceCodeTypeCode, traces: [ENT-CAT-002, REQ-CAT-118, REQ-CAT-119, REQ-CAT-123], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-026, table: CAT_VARIANT, column: EINVOICE_CODE, type: "VARCHAR(100)", entity_field: ENT-CAT-002.einvoiceCode, traces: [ENT-CAT-002, REQ-CAT-118, REQ-CAT-119, REQ-CAT-123], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-027, table: CAT_VARIANT, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-002.isActiveFl, traces: [ENT-CAT-002, REQ-CAT-050, REQ-CAT-053, REQ-CAT-062], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-028, table: CAT_VARIANT, column: ITEM_FK, type: "BIGINT", entity_field: ENT-CAT-002.itemFk, traces: [ENT-CAT-002, REQ-CAT-025, REQ-CAT-049], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-029, table: CAT_VARIANT, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-002.tenantId, traces: [ENT-CAT-002, REQ-CAT-001], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-030, table: CAT_VARIANT, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-002.version, traces: [ENT-CAT-002, REQ-CAT-010], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-031, table: CAT_VARIANT, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-002.createdBy, traces: [ENT-CAT-002, REQ-CAT-025], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-032, table: CAT_VARIANT, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-002.createdAt, traces: [ENT-CAT-002, REQ-CAT-025], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-033, table: CAT_VARIANT, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-002.updatedBy, traces: [ENT-CAT-002, REQ-CAT-118], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-034, table: CAT_VARIANT, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-002.updatedAt, traces: [ENT-CAT-002, REQ-CAT-017, REQ-CAT-020], nullable: "NULL", default: "—"}
  # ── CAT_VAR_ATTR — ENT-CAT-003 Variant attribute (lookup) ──
  - {id: DBF-CAT-035, table: CAT_VAR_ATTR, column: VARIANT_ATTRIBUTE_PK, type: "BIGINT", entity_field: ENT-CAT-003.variantAttributePk, traces: [ENT-CAT-003, REQ-CAT-042, REQ-CAT-048], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-036, table: CAT_VAR_ATTR, column: CODE, type: "VARCHAR(30)", entity_field: ENT-CAT-003.code, traces: [ENT-CAT-003, REQ-CAT-042, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-037, table: CAT_VAR_ATTR, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-003.nameAr, traces: [ENT-CAT-003, REQ-CAT-042, REQ-CAT-043, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-038, table: CAT_VAR_ATTR, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-003.nameEn, traces: [ENT-CAT-003, REQ-CAT-043], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-039, table: CAT_VAR_ATTR, column: SORT_ORDER, type: "INTEGER", entity_field: ENT-CAT-003.sortOrder, traces: [ENT-CAT-003, REQ-CAT-043, REQ-CAT-048], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-040, table: CAT_VAR_ATTR, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-003.isActiveFl, traces: [ENT-CAT-003, REQ-CAT-044, REQ-CAT-054], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-041, table: CAT_VAR_ATTR, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-003.tenantId, traces: [ENT-CAT-003, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-042, table: CAT_VAR_ATTR, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-003.version, traces: [ENT-CAT-003, REQ-CAT-043], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-043, table: CAT_VAR_ATTR, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-003.createdBy, traces: [ENT-CAT-003, REQ-CAT-042], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-044, table: CAT_VAR_ATTR, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-003.createdAt, traces: [ENT-CAT-003, REQ-CAT-042], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-045, table: CAT_VAR_ATTR, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-003.updatedBy, traces: [ENT-CAT-003, REQ-CAT-043], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-046, table: CAT_VAR_ATTR, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-003.updatedAt, traces: [ENT-CAT-003, REQ-CAT-043], nullable: "NULL", default: "—"}
  # ── CAT_VAR_ATTR_VALUE — ENT-CAT-004 Variant attribute value (lookup) ──
  - {id: DBF-CAT-047, table: CAT_VAR_ATTR_VALUE, column: VARIANT_ATTRIBUTE_VALUE_PK, type: "BIGINT", entity_field: ENT-CAT-004.variantAttributeValuePk, traces: [ENT-CAT-004, REQ-CAT-045, REQ-CAT-048], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-048, table: CAT_VAR_ATTR_VALUE, column: CODE, type: "VARCHAR(30)", entity_field: ENT-CAT-004.code, traces: [ENT-CAT-004, REQ-CAT-045, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-049, table: CAT_VAR_ATTR_VALUE, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-004.nameAr, traces: [ENT-CAT-004, REQ-CAT-045, REQ-CAT-046, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-050, table: CAT_VAR_ATTR_VALUE, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-004.nameEn, traces: [ENT-CAT-004, REQ-CAT-046], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-051, table: CAT_VAR_ATTR_VALUE, column: SORT_ORDER, type: "INTEGER", entity_field: ENT-CAT-004.sortOrder, traces: [ENT-CAT-004, REQ-CAT-046, REQ-CAT-048], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-052, table: CAT_VAR_ATTR_VALUE, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-004.isActiveFl, traces: [ENT-CAT-004, REQ-CAT-047, REQ-CAT-054], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-053, table: CAT_VAR_ATTR_VALUE, column: VARIANT_ATTRIBUTE_FK, type: "BIGINT", entity_field: ENT-CAT-004.variantAttributeFk, traces: [ENT-CAT-004, REQ-CAT-045, REQ-CAT-048], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-054, table: CAT_VAR_ATTR_VALUE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-004.tenantId, traces: [ENT-CAT-004, REQ-CAT-055], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-055, table: CAT_VAR_ATTR_VALUE, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-004.version, traces: [ENT-CAT-004, REQ-CAT-046], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-056, table: CAT_VAR_ATTR_VALUE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-004.createdBy, traces: [ENT-CAT-004, REQ-CAT-045], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-057, table: CAT_VAR_ATTR_VALUE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-004.createdAt, traces: [ENT-CAT-004, REQ-CAT-045], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-058, table: CAT_VAR_ATTR_VALUE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-004.updatedBy, traces: [ENT-CAT-004, REQ-CAT-046], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-059, table: CAT_VAR_ATTR_VALUE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-004.updatedAt, traces: [ENT-CAT-004, REQ-CAT-046], nullable: "NULL", default: "—"}
  # ── CAT_VAR_ATTR_ASSIGN — ENT-CAT-005 Variant attribute assignment (master, dependent) ──
  - {id: DBF-CAT-060, table: CAT_VAR_ATTR_ASSIGN, column: VARIANT_ATTRIBUTE_ASSIGNMENT_PK, type: "BIGINT", entity_field: ENT-CAT-005.variantAttributeAssignmentPk, traces: [ENT-CAT-005, REQ-CAT-049, REQ-CAT-018], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-061, table: CAT_VAR_ATTR_ASSIGN, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-005.isActiveFl, traces: [ENT-CAT-005, REQ-CAT-022, REQ-CAT-054], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-062, table: CAT_VAR_ATTR_ASSIGN, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-CAT-005.variantFk, traces: [ENT-CAT-005, REQ-CAT-049, REQ-CAT-018], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-063, table: CAT_VAR_ATTR_ASSIGN, column: VARIANT_ATTRIBUTE_FK, type: "BIGINT", entity_field: ENT-CAT-005.variantAttributeFk, traces: [ENT-CAT-005, REQ-CAT-049, REQ-CAT-051, REQ-CAT-052], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-064, table: CAT_VAR_ATTR_ASSIGN, column: VARIANT_ATTRIBUTE_VALUE_FK, type: "BIGINT", entity_field: ENT-CAT-005.variantAttributeValueFk, traces: [ENT-CAT-005, REQ-CAT-049, REQ-CAT-051], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-065, table: CAT_VAR_ATTR_ASSIGN, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-005.tenantId, traces: [ENT-CAT-005, REQ-CAT-049], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-066, table: CAT_VAR_ATTR_ASSIGN, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-005.version, traces: [ENT-CAT-005, REQ-CAT-018], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-067, table: CAT_VAR_ATTR_ASSIGN, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-005.createdBy, traces: [ENT-CAT-005, REQ-CAT-049], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-068, table: CAT_VAR_ATTR_ASSIGN, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-005.createdAt, traces: [ENT-CAT-005, REQ-CAT-049], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-069, table: CAT_VAR_ATTR_ASSIGN, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-005.updatedBy, traces: [ENT-CAT-005, REQ-CAT-018], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-070, table: CAT_VAR_ATTR_ASSIGN, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-005.updatedAt, traces: [ENT-CAT-005, REQ-CAT-018], nullable: "NULL", default: "—"}
  # ── CAT_CATEGORY — ENT-CAT-006 Category (master) ──
  - {id: DBF-CAT-071, table: CAT_CATEGORY, column: CATEGORY_PK, type: "BIGINT", entity_field: ENT-CAT-006.categoryPk, traces: [ENT-CAT-006, REQ-CAT-064, REQ-CAT-067], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-072, table: CAT_CATEGORY, column: CODE, type: "VARCHAR(30)", entity_field: ENT-CAT-006.code, traces: [ENT-CAT-006, REQ-CAT-064, REQ-CAT-070], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-073, table: CAT_CATEGORY, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-006.nameAr, traces: [ENT-CAT-006, REQ-CAT-064, REQ-CAT-065, REQ-CAT-070], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-074, table: CAT_CATEGORY, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-006.nameEn, traces: [ENT-CAT-006, REQ-CAT-065], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-075, table: CAT_CATEGORY, column: SORT_ORDER, type: "INTEGER", entity_field: ENT-CAT-006.sortOrder, traces: [ENT-CAT-006, REQ-CAT-065, REQ-CAT-067], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-076, table: CAT_CATEGORY, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-006.isActiveFl, traces: [ENT-CAT-006, REQ-CAT-066, REQ-CAT-071], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-077, table: CAT_CATEGORY, column: PARENT_CATEGORY_FK, type: "BIGINT", entity_field: ENT-CAT-006.parentCategoryFk, traces: [ENT-CAT-006, REQ-CAT-064, REQ-CAT-065, REQ-CAT-069], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-078, table: CAT_CATEGORY, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-006.tenantId, traces: [ENT-CAT-006, REQ-CAT-070], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-079, table: CAT_CATEGORY, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-006.version, traces: [ENT-CAT-006, REQ-CAT-065], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-080, table: CAT_CATEGORY, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-006.createdBy, traces: [ENT-CAT-006, REQ-CAT-064], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-081, table: CAT_CATEGORY, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-006.createdAt, traces: [ENT-CAT-006, REQ-CAT-064], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-082, table: CAT_CATEGORY, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-006.updatedBy, traces: [ENT-CAT-006, REQ-CAT-065], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-083, table: CAT_CATEGORY, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-006.updatedAt, traces: [ENT-CAT-006, REQ-CAT-065], nullable: "NULL", default: "—"}
  # ── CAT_BUNDLE_COMP — ENT-CAT-007 Bundle component (master, dependent) ──
  - {id: DBF-CAT-084, table: CAT_BUNDLE_COMP, column: BUNDLE_COMPONENT_PK, type: "BIGINT", entity_field: ENT-CAT-007.bundleComponentPk, traces: [ENT-CAT-007, REQ-CAT-056, REQ-CAT-058], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-085, table: CAT_BUNDLE_COMP, column: QUANTITY, type: "NUMERIC(18,3)", entity_field: ENT-CAT-007.quantity, traces: [ENT-CAT-007, REQ-CAT-056, REQ-CAT-057, REQ-CAT-059], nullable: "NOT NULL", default: "1"}
  - {id: DBF-CAT-086, table: CAT_BUNDLE_COMP, column: SORT_ORDER, type: "INTEGER", entity_field: ENT-CAT-007.sortOrder, traces: [ENT-CAT-007, REQ-CAT-056, REQ-CAT-058], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-087, table: CAT_BUNDLE_COMP, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-007.isActiveFl, traces: [ENT-CAT-007, REQ-CAT-057, REQ-CAT-058, REQ-CAT-062], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-088, table: CAT_BUNDLE_COMP, column: BUNDLE_ITEM_FK, type: "BIGINT", entity_field: ENT-CAT-007.bundleItemFk, traces: [ENT-CAT-007, REQ-CAT-056, REQ-CAT-058], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-089, table: CAT_BUNDLE_COMP, column: COMPONENT_VARIANT_FK, type: "BIGINT", entity_field: ENT-CAT-007.componentVariantFk, traces: [ENT-CAT-007, REQ-CAT-056, REQ-CAT-060, REQ-CAT-062], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-090, table: CAT_BUNDLE_COMP, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-007.tenantId, traces: [ENT-CAT-007, REQ-CAT-056], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-091, table: CAT_BUNDLE_COMP, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-007.version, traces: [ENT-CAT-007, REQ-CAT-057], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-092, table: CAT_BUNDLE_COMP, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-007.createdBy, traces: [ENT-CAT-007, REQ-CAT-056], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-093, table: CAT_BUNDLE_COMP, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-007.createdAt, traces: [ENT-CAT-007, REQ-CAT-056], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-094, table: CAT_BUNDLE_COMP, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-007.updatedBy, traces: [ENT-CAT-007, REQ-CAT-057], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-095, table: CAT_BUNDLE_COMP, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-007.updatedAt, traces: [ENT-CAT-007, REQ-CAT-057], nullable: "NULL", default: "—"}
  # ── CAT_ITEM_UNIT — ENT-CAT-008 Item unit (master, dependent) ──
  - {id: DBF-CAT-096, table: CAT_ITEM_UNIT, column: ITEM_UNIT_PK, type: "BIGINT", entity_field: ENT-CAT-008.itemUnitPk, traces: [ENT-CAT-008, REQ-CAT-077, REQ-CAT-079], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-097, table: CAT_ITEM_UNIT, column: UOM_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-008.uomCode, traces: [ENT-CAT-008, REQ-CAT-074, REQ-CAT-077, REQ-CAT-080], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-098, table: CAT_ITEM_UNIT, column: CONVERSION_FACTOR, type: "NUMERIC(18,6)", entity_field: ENT-CAT-008.conversionFactor, traces: [ENT-CAT-008, REQ-CAT-077, REQ-CAT-080, REQ-CAT-081], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-099, table: CAT_ITEM_UNIT, column: IS_PURCHASE_FL, type: "BOOLEAN", entity_field: ENT-CAT-008.isPurchaseFl, traces: [ENT-CAT-008, REQ-CAT-077, REQ-CAT-078], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-100, table: CAT_ITEM_UNIT, column: IS_SALES_FL, type: "BOOLEAN", entity_field: ENT-CAT-008.isSalesFl, traces: [ENT-CAT-008, REQ-CAT-077, REQ-CAT-078], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-101, table: CAT_ITEM_UNIT, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-008.isActiveFl, traces: [ENT-CAT-008, REQ-CAT-079, REQ-CAT-080], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-102, table: CAT_ITEM_UNIT, column: ITEM_FK, type: "BIGINT", entity_field: ENT-CAT-008.itemFk, traces: [ENT-CAT-008, REQ-CAT-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-103, table: CAT_ITEM_UNIT, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-008.tenantId, traces: [ENT-CAT-008, REQ-CAT-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-104, table: CAT_ITEM_UNIT, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-008.version, traces: [ENT-CAT-008, REQ-CAT-078], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-105, table: CAT_ITEM_UNIT, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-008.createdBy, traces: [ENT-CAT-008, REQ-CAT-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-106, table: CAT_ITEM_UNIT, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-008.createdAt, traces: [ENT-CAT-008, REQ-CAT-077], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-107, table: CAT_ITEM_UNIT, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-008.updatedBy, traces: [ENT-CAT-008, REQ-CAT-078], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-108, table: CAT_ITEM_UNIT, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-008.updatedAt, traces: [ENT-CAT-008, REQ-CAT-078], nullable: "NULL", default: "—"}
  # ── CAT_BARCODE — ENT-CAT-009 Barcode (master, dependent) ──
  - {id: DBF-CAT-109, table: CAT_BARCODE, column: BARCODE_PK, type: "BIGINT", entity_field: ENT-CAT-009.barcodePk, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-086], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-110, table: CAT_BARCODE, column: BARCODE_VALUE, type: "VARCHAR(48)", entity_field: ENT-CAT-009.barcodeValue, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-084, REQ-CAT-086, REQ-CAT-087, REQ-CAT-088], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-111, table: CAT_BARCODE, column: BARCODE_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-009.barcodeTypeCode, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-084, REQ-CAT-096], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-112, table: CAT_BARCODE, column: UOM_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-009.uomCode, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-086, REQ-CAT-089], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-113, table: CAT_BARCODE, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-009.isActiveFl, traces: [ENT-CAT-009, REQ-CAT-085, REQ-CAT-086], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-114, table: CAT_BARCODE, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-CAT-009.variantFk, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-086, REQ-CAT-019], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-115, table: CAT_BARCODE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-009.tenantId, traces: [ENT-CAT-009, REQ-CAT-087], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-116, table: CAT_BARCODE, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-009.version, traces: [ENT-CAT-009, REQ-CAT-085], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-117, table: CAT_BARCODE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-009.createdBy, traces: [ENT-CAT-009, REQ-CAT-083], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-118, table: CAT_BARCODE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-009.createdAt, traces: [ENT-CAT-009, REQ-CAT-083, REQ-CAT-101], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-119, table: CAT_BARCODE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-009.updatedBy, traces: [ENT-CAT-009, REQ-CAT-085], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-120, table: CAT_BARCODE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-009.updatedAt, traces: [ENT-CAT-009, REQ-CAT-085], nullable: "NULL", default: "—"}
  # ── CAT_ITEM_MEDIA — ENT-CAT-010 Item media (master, dependent) ──
  - {id: DBF-CAT-121, table: CAT_ITEM_MEDIA, column: ITEM_MEDIA_PK, type: "BIGINT", entity_field: ENT-CAT-010.itemMediaPk, traces: [ENT-CAT-010, REQ-CAT-105, REQ-CAT-109], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-122, table: CAT_ITEM_MEDIA, column: FILE_DOCUMENT_REF, type: "BIGINT", entity_field: ENT-CAT-010.fileDocumentRef, traces: [ENT-CAT-010, REQ-CAT-105, REQ-CAT-110], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-123, table: CAT_ITEM_MEDIA, column: NAME_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-010.nameAr, traces: [ENT-CAT-010, REQ-CAT-105, REQ-CAT-106], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-124, table: CAT_ITEM_MEDIA, column: NAME_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-010.nameEn, traces: [ENT-CAT-010, REQ-CAT-106], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-125, table: CAT_ITEM_MEDIA, column: SORT_ORDER, type: "INTEGER", entity_field: ENT-CAT-010.sortOrder, traces: [ENT-CAT-010, REQ-CAT-105, REQ-CAT-106, REQ-CAT-111], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-126, table: CAT_ITEM_MEDIA, column: IS_PRIMARY_FL, type: "BOOLEAN", entity_field: ENT-CAT-010.isPrimaryFl, traces: [ENT-CAT-010, REQ-CAT-106, REQ-CAT-108, REQ-CAT-111], nullable: "NOT NULL", default: "FALSE"}
  - {id: DBF-CAT-127, table: CAT_ITEM_MEDIA, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-010.isActiveFl, traces: [ENT-CAT-010, REQ-CAT-107, REQ-CAT-110], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-128, table: CAT_ITEM_MEDIA, column: ITEM_FK, type: "BIGINT", entity_field: ENT-CAT-010.itemFk, traces: [ENT-CAT-010, REQ-CAT-105], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-129, table: CAT_ITEM_MEDIA, column: VARIANT_FK, type: "BIGINT", entity_field: ENT-CAT-010.variantFk, traces: [ENT-CAT-010, REQ-CAT-106, REQ-CAT-109, REQ-CAT-112], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-130, table: CAT_ITEM_MEDIA, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-010.tenantId, traces: [ENT-CAT-010, REQ-CAT-105], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-131, table: CAT_ITEM_MEDIA, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-010.version, traces: [ENT-CAT-010, REQ-CAT-106], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-132, table: CAT_ITEM_MEDIA, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-010.createdBy, traces: [ENT-CAT-010, REQ-CAT-105], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-133, table: CAT_ITEM_MEDIA, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-010.createdAt, traces: [ENT-CAT-010, REQ-CAT-105], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-134, table: CAT_ITEM_MEDIA, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-010.updatedBy, traces: [ENT-CAT-010, REQ-CAT-106], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-135, table: CAT_ITEM_MEDIA, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-010.updatedAt, traces: [ENT-CAT-010, REQ-CAT-106], nullable: "NULL", default: "—"}
  # ── CAT_SCALE_LABEL_LAYOUT — ENT-CAT-011 Scale label layout (config) ──
  - {id: DBF-CAT-136, table: CAT_SCALE_LABEL_LAYOUT, column: SCALE_LABEL_LAYOUT_PK, type: "BIGINT", entity_field: ENT-CAT-011.scaleLabelLayoutPk, traces: [ENT-CAT-011, REQ-CAT-092, REQ-CAT-095], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-137, table: CAT_SCALE_LABEL_LAYOUT, column: KEY, type: "VARCHAR(2)", entity_field: ENT-CAT-011.key, traces: [ENT-CAT-011, REQ-CAT-092, REQ-CAT-097, REQ-CAT-098], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-138, table: CAT_SCALE_LABEL_LAYOUT, column: VALUE_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-011.valueAr, traces: [ENT-CAT-011, REQ-CAT-093], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-139, table: CAT_SCALE_LABEL_LAYOUT, column: VALUE_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-011.valueEn, traces: [ENT-CAT-011, REQ-CAT-093], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-140, table: CAT_SCALE_LABEL_LAYOUT, column: SCALE_CODE_LENGTH, type: "INTEGER", entity_field: ENT-CAT-011.scaleCodeLength, traces: [ENT-CAT-011, REQ-CAT-092, REQ-CAT-093, REQ-CAT-098, REQ-CAT-099], nullable: "NOT NULL", default: "5"}
  - {id: DBF-CAT-141, table: CAT_SCALE_LABEL_LAYOUT, column: EMBEDDED_VALUE_TYPE_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-011.embeddedValueTypeCode, traces: [ENT-CAT-011, REQ-CAT-092, REQ-CAT-093, REQ-CAT-097], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-142, table: CAT_SCALE_LABEL_LAYOUT, column: VALUE_DECIMALS, type: "INTEGER", entity_field: ENT-CAT-011.valueDecimals, traces: [ENT-CAT-011, REQ-CAT-092, REQ-CAT-093, REQ-CAT-098], nullable: "NOT NULL", default: "3"}
  - {id: DBF-CAT-143, table: CAT_SCALE_LABEL_LAYOUT, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-011.isActiveFl, traces: [ENT-CAT-011, REQ-CAT-094, REQ-CAT-095], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-144, table: CAT_SCALE_LABEL_LAYOUT, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-011.tenantId, traces: [ENT-CAT-011, REQ-CAT-098], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-145, table: CAT_SCALE_LABEL_LAYOUT, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-011.version, traces: [ENT-CAT-011, REQ-CAT-093], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-146, table: CAT_SCALE_LABEL_LAYOUT, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-011.createdBy, traces: [ENT-CAT-011, REQ-CAT-092], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-147, table: CAT_SCALE_LABEL_LAYOUT, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-011.createdAt, traces: [ENT-CAT-011, REQ-CAT-092], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-148, table: CAT_SCALE_LABEL_LAYOUT, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-011.updatedBy, traces: [ENT-CAT-011, REQ-CAT-093], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-149, table: CAT_SCALE_LABEL_LAYOUT, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-011.updatedAt, traces: [ENT-CAT-011, REQ-CAT-093], nullable: "NULL", default: "—"}
  # ── CAT_UNIT_EINVOICE_CODE — ENT-CAT-012 Unit e-invoice code (config) ──
  - {id: DBF-CAT-150, table: CAT_UNIT_EINVOICE_CODE, column: UNIT_EINVOICE_CODE_PK, type: "BIGINT", entity_field: ENT-CAT-012.unitEinvoiceCodePk, traces: [ENT-CAT-012, REQ-CAT-120, REQ-CAT-095], nullable: "NOT NULL", default: "identity"}
  - {id: DBF-CAT-151, table: CAT_UNIT_EINVOICE_CODE, column: KEY, type: "VARCHAR(50)", entity_field: ENT-CAT-012.key, traces: [ENT-CAT-012, REQ-CAT-120, REQ-CAT-124], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-152, table: CAT_UNIT_EINVOICE_CODE, column: VALUE_AR, type: "VARCHAR(200)", entity_field: ENT-CAT-012.valueAr, traces: [ENT-CAT-012, REQ-CAT-095], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-153, table: CAT_UNIT_EINVOICE_CODE, column: VALUE_EN, type: "VARCHAR(200)", entity_field: ENT-CAT-012.valueEn, traces: [ENT-CAT-012, REQ-CAT-095], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-154, table: CAT_UNIT_EINVOICE_CODE, column: EINVOICE_UNIT_CODE, type: "VARCHAR(50)", entity_field: ENT-CAT-012.einvoiceUnitCode, traces: [ENT-CAT-012, REQ-CAT-120, REQ-CAT-121], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-155, table: CAT_UNIT_EINVOICE_CODE, column: IS_ACTIVE_FL, type: "BOOLEAN", entity_field: ENT-CAT-012.isActiveFl, traces: [ENT-CAT-012, REQ-CAT-122, REQ-CAT-124], nullable: "NOT NULL", default: "TRUE"}
  - {id: DBF-CAT-156, table: CAT_UNIT_EINVOICE_CODE, column: TENANT_ID, type: "BIGINT", entity_field: ENT-CAT-012.tenantId, traces: [ENT-CAT-012, REQ-CAT-124], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-157, table: CAT_UNIT_EINVOICE_CODE, column: VERSION, type: "BIGINT", entity_field: ENT-CAT-012.version, traces: [ENT-CAT-012, REQ-CAT-121], nullable: "NOT NULL", default: "0"}
  - {id: DBF-CAT-158, table: CAT_UNIT_EINVOICE_CODE, column: CREATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-012.createdBy, traces: [ENT-CAT-012, REQ-CAT-120], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-159, table: CAT_UNIT_EINVOICE_CODE, column: CREATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-012.createdAt, traces: [ENT-CAT-012, REQ-CAT-120], nullable: "NOT NULL", default: "—"}
  - {id: DBF-CAT-160, table: CAT_UNIT_EINVOICE_CODE, column: UPDATED_BY, type: "VARCHAR(100)", entity_field: ENT-CAT-012.updatedBy, traces: [ENT-CAT-012, REQ-CAT-121], nullable: "NULL", default: "—"}
  - {id: DBF-CAT-161, table: CAT_UNIT_EINVOICE_CODE, column: UPDATED_AT, type: "TIMESTAMPTZ", entity_field: ENT-CAT-012.updatedAt, traces: [ENT-CAT-012, REQ-CAT-121], nullable: "NULL", default: "—"}
```

Total: 161 DBF ids across 12 tables.

| Table | ENT | Kind | DBF range |
|---|---|---|---|
| CAT_ITEM | ENT-CAT-001 | master | DBF-CAT-001 … DBF-CAT-021 |
| CAT_VARIANT | ENT-CAT-002 | master | DBF-CAT-022 … DBF-CAT-034 |
| CAT_VAR_ATTR | ENT-CAT-003 | lookup | DBF-CAT-035 … DBF-CAT-046 |
| CAT_VAR_ATTR_VALUE | ENT-CAT-004 | lookup | DBF-CAT-047 … DBF-CAT-059 |
| CAT_VAR_ATTR_ASSIGN | ENT-CAT-005 | master (dependent) | DBF-CAT-060 … DBF-CAT-070 |
| CAT_CATEGORY | ENT-CAT-006 | master | DBF-CAT-071 … DBF-CAT-083 |
| CAT_BUNDLE_COMP | ENT-CAT-007 | master (dependent) | DBF-CAT-084 … DBF-CAT-095 |
| CAT_ITEM_UNIT | ENT-CAT-008 | master (dependent) | DBF-CAT-096 … DBF-CAT-108 |
| CAT_BARCODE | ENT-CAT-009 | master (dependent) | DBF-CAT-109 … DBF-CAT-120 |
| CAT_ITEM_MEDIA | ENT-CAT-010 | master (dependent) | DBF-CAT-121 … DBF-CAT-135 |
| CAT_SCALE_LABEL_LAYOUT | ENT-CAT-011 | config | DBF-CAT-136 … DBF-CAT-149 |
| CAT_UNIT_EINVOICE_CODE | ENT-CAT-012 | config | DBF-CAT-150 … DBF-CAT-161 |

Origin of the standard columns. Each table carries the SRS fields of its kind (A3). It also carries the given's entity column set: `tenantId`, `version`, `createdBy`, `createdAt`, `updatedBy`, `updatedAt` (erp-core `AuditableEntity` and the tenancy convention; D-P2-02). Each such column traces to its ENT and to a REQ that reads it. Examples: AC-CAT-004 reads `updatedBy`, and AC-CAT-018 and AC-CAT-021 read `updatedAt`.

RULE data sources. Every `ENT.field` named in a RULE `Data source` line of SRS A5 is bound to a DBF above (C6.9). The two exceptions are `ENT-CAT-002.nameAr` and `ENT-CAT-002.nameEn`. No RULE names them, and they are derived at read (D-P2-08).

## 3 — XM register

```yaml name=xm-register
records: []
```

CAT consumes no entity of another business module (SRS A8 `module-dependencies`: `consumes: []`). It is first in the fixed module order [KB:raw-idea.md §5]. So the module has no HARD-FK and no SOFT-READ edge, and the script creates no cross-module column.

Given references create no XM:
- `CAT_ITEM_MEDIA.FILE_DOCUMENT_REF` (DBF-CAT-122) holds the given `FILE_DOCUMENT` identifier. It is a given soft read with no constraint (SRS A3 "GIVEN, no XM edge").
- Every `TENANT_ID` column holds the given tenant identifier, with `FK_<TABLE>_TENANT → CORE_TENANT(ID)` (erp-core checklist C1). `CORE_TENANT` is a given table, so the FK is a permitted given reference and not an XM (ADR-CAT-055).
- Lookup codes are given lookup values read through `MdlLookupApi`.

## 4 — FULL_DATABASE_SCRIPT

```sql
-- ══════════════════════════════════════════════════════════════════
-- CAT v2 — P2 — FULL_DATABASE_SCRIPT — PostgreSQL 16
-- Migration for CS-CAT-001 (ADDITIVE): the current state holds no earlier CAT script,
-- so this migration creates every CAT object. No schema prefix. Identifiers are UPPER_SNAKE_CASE, unquoted.
-- PK generation: GENERATED ALWAYS AS IDENTITY on every PK column (profile stack.db.pk_generation).
-- File: V1000__cat_catalog.sql (erp-core conventions § Migrations; the given core chain runs first).
-- ══════════════════════════════════════════════════════════════════

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 1 — SEQUENCES, EXTENSION AND FUNCTION used by later blocks
-- Sequences: none. Every PK uses the identity clause. Item / Variant codes and internal
-- barcode digits come from the core number series (BLOCK 8c), never from a CAT sequence.
-- Extension pg_trgm: trigram indexes of BLOCK 7 (ADR-CAT-062). DEPLOYMENT PRECONDITION: the role that runs
-- Flyway holds the CREATE privilege on the database (pg_trgm is a trusted extension from PostgreSQL 13, so
-- CREATE is enough and no superuser is needed); where that grant is withheld, a DBA runs
-- `CREATE EXTENSION pg_trgm;` once before the first deployment and the statement below is a no-op.
-- Function CAT_NORMALIZE_AR: Arabic normalisation of RULE-CAT-003 and of search (ADR-CAT-062);
-- created here because UQ_CAT_ITEM_NAME_AR (BLOCK 5b) and the trigram indexes (BLOCK 7) use it.
-- ──────────────────────────────────────────────────────────────────

CREATE EXTENSION IF NOT EXISTS pg_trgm;

CREATE OR REPLACE FUNCTION CAT_NORMALIZE_AR(P_TEXT TEXT) RETURNS TEXT
    LANGUAGE sql IMMUTABLE STRICT PARALLEL SAFE AS
$$ SELECT btrim(regexp_replace(lower(translate(regexp_replace(P_TEXT, '[ً-ٰٟـ]', '', 'g'),
                                                  'أإآٱةى', 'ااااهي')), '\s+', ' ', 'g')) $$;

COMMENT ON FUNCTION CAT_NORMALIZE_AR(TEXT) IS 'RULE-CAT-003 / search: drops tashkeel and tatweel, folds أ إ آ ٱ to ا, ة to ه, ى to ي, lower-cases Latin, trims and collapses spaces (ADR-CAT-062)';

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 2 — PARENT TABLES (no FK dependency on another CAT table)
-- ──────────────────────────────────────────────────────────────────

CREATE TABLE CAT_CATEGORY (
    CATEGORY_PK                 BIGINT        GENERATED ALWAYS AS IDENTITY,
    CODE                        VARCHAR(30)   NOT NULL,
    NAME_AR                     VARCHAR(200)  NOT NULL,
    NAME_EN                     VARCHAR(200),
    SORT_ORDER                  INTEGER       NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    PARENT_CATEGORY_FK          BIGINT,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_VAR_ATTR (
    VARIANT_ATTRIBUTE_PK        BIGINT        GENERATED ALWAYS AS IDENTITY,
    CODE                        VARCHAR(30)   NOT NULL,
    NAME_AR                     VARCHAR(200)  NOT NULL,
    NAME_EN                     VARCHAR(200),
    SORT_ORDER                  INTEGER       NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_SCALE_LABEL_LAYOUT (
    SCALE_LABEL_LAYOUT_PK       BIGINT        GENERATED ALWAYS AS IDENTITY,
    KEY                         VARCHAR(2)    NOT NULL,
    VALUE_AR                    VARCHAR(200),
    VALUE_EN                    VARCHAR(200),
    SCALE_CODE_LENGTH           INTEGER       NOT NULL DEFAULT 5,
    EMBEDDED_VALUE_TYPE_CODE    VARCHAR(50)   NOT NULL,
    VALUE_DECIMALS              INTEGER       NOT NULL DEFAULT 3,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_UNIT_EINVOICE_CODE (
    UNIT_EINVOICE_CODE_PK       BIGINT        GENERATED ALWAYS AS IDENTITY,
    KEY                         VARCHAR(50)   NOT NULL,
    VALUE_AR                    VARCHAR(200),
    VALUE_EN                    VARCHAR(200),
    EINVOICE_UNIT_CODE          VARCHAR(50)   NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 3 — CHILD TABLES (chain: CATEGORY → ITEM → VARIANT → assignment / bundle / unit / barcode / media)
-- ──────────────────────────────────────────────────────────────────

CREATE TABLE CAT_ITEM (
    ITEM_PK                     BIGINT        GENERATED ALWAYS AS IDENTITY,
    CODE                        VARCHAR(20)   NOT NULL,
    NAME_AR                     VARCHAR(200)  NOT NULL,
    NAME_EN                     VARCHAR(200),
    ITEM_KIND_CODE              VARCHAR(50)   NOT NULL,
    BASE_UOM_CODE               VARCHAR(50)   NOT NULL,
    BRAND_CODE                  VARCHAR(50),
    TAX_CLASS_CODE              VARCHAR(50)   NOT NULL,
    EINVOICE_CODE_TYPE_CODE     VARCHAR(50),
    EINVOICE_CODE               VARCHAR(100),
    DESCRIPTION_AR              VARCHAR(2000),
    DESCRIPTION_EN              VARCHAR(2000),
    PUBLISH_STATE_CODE          VARCHAR(50)   NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    CATEGORY_FK                 BIGINT,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_VAR_ATTR_VALUE (
    VARIANT_ATTRIBUTE_VALUE_PK  BIGINT        GENERATED ALWAYS AS IDENTITY,
    CODE                        VARCHAR(30)   NOT NULL,
    NAME_AR                     VARCHAR(200)  NOT NULL,
    NAME_EN                     VARCHAR(200),
    SORT_ORDER                  INTEGER       NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    VARIANT_ATTRIBUTE_FK        BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_VARIANT (
    VARIANT_PK                  BIGINT        GENERATED ALWAYS AS IDENTITY,
    CODE                        VARCHAR(20)   NOT NULL,
    IS_DEFAULT_FL               BOOLEAN       NOT NULL DEFAULT FALSE,
    EINVOICE_CODE_TYPE_CODE     VARCHAR(50),
    EINVOICE_CODE               VARCHAR(100),
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    ITEM_FK                     BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_VAR_ATTR_ASSIGN (
    VARIANT_ATTRIBUTE_ASSIGNMENT_PK BIGINT    GENERATED ALWAYS AS IDENTITY,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    VARIANT_FK                  BIGINT        NOT NULL,
    VARIANT_ATTRIBUTE_FK        BIGINT        NOT NULL,
    VARIANT_ATTRIBUTE_VALUE_FK  BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_BUNDLE_COMP (
    BUNDLE_COMPONENT_PK         BIGINT        GENERATED ALWAYS AS IDENTITY,
    QUANTITY                    NUMERIC(18,3) NOT NULL DEFAULT 1,
    SORT_ORDER                  INTEGER       NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    BUNDLE_ITEM_FK              BIGINT        NOT NULL,
    COMPONENT_VARIANT_FK        BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_ITEM_UNIT (
    ITEM_UNIT_PK                BIGINT        GENERATED ALWAYS AS IDENTITY,
    UOM_CODE                    VARCHAR(50)   NOT NULL,
    CONVERSION_FACTOR           NUMERIC(18,6) NOT NULL,
    IS_PURCHASE_FL              BOOLEAN       NOT NULL DEFAULT TRUE,
    IS_SALES_FL                 BOOLEAN       NOT NULL DEFAULT TRUE,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    ITEM_FK                     BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_BARCODE (
    BARCODE_PK                  BIGINT        GENERATED ALWAYS AS IDENTITY,
    BARCODE_VALUE               VARCHAR(48)   NOT NULL,
    BARCODE_TYPE_CODE           VARCHAR(50)   NOT NULL,
    UOM_CODE                    VARCHAR(50)   NOT NULL,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    VARIANT_FK                  BIGINT        NOT NULL,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

CREATE TABLE CAT_ITEM_MEDIA (
    ITEM_MEDIA_PK               BIGINT        GENERATED ALWAYS AS IDENTITY,
    FILE_DOCUMENT_REF           BIGINT        NOT NULL,   -- given erp-core FILE_DOCUMENT.ID: soft read, no constraint, no XM
    NAME_AR                     VARCHAR(200)  NOT NULL,
    NAME_EN                     VARCHAR(200),
    SORT_ORDER                  INTEGER       NOT NULL,
    IS_PRIMARY_FL               BOOLEAN       NOT NULL DEFAULT FALSE,
    IS_ACTIVE_FL                BOOLEAN       NOT NULL DEFAULT TRUE,
    ITEM_FK                     BIGINT        NOT NULL,
    VARIANT_FK                  BIGINT,
    TENANT_ID                   BIGINT        NOT NULL,
    VERSION                     BIGINT        NOT NULL DEFAULT 0,
    CREATED_BY                  VARCHAR(100)  NOT NULL,
    CREATED_AT                  TIMESTAMPTZ   NOT NULL,
    UPDATED_BY                  VARCHAR(100),
    UPDATED_AT                  TIMESTAMPTZ
);

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 4 — COMMENTS (each column comment cites its DBF id)
-- ──────────────────────────────────────────────────────────────────

COMMENT ON TABLE CAT_CATEGORY IS 'ENT-CAT-006 Category / فئة — node of the tenant item classification tree (max 5 levels)';
COMMENT ON COLUMN CAT_CATEGORY.CATEGORY_PK IS 'DBF-CAT-071 — Category identifier (identity)';
COMMENT ON COLUMN CAT_CATEGORY.CODE IS 'DBF-CAT-072 — Category code, 1-30 Latin letters, digits or hyphens, unique per tenant (RULE-CAT-024)';
COMMENT ON COLUMN CAT_CATEGORY.NAME_AR IS 'DBF-CAT-073 — Arabic name, at least one Arabic letter (RULE-CAT-001)';
COMMENT ON COLUMN CAT_CATEGORY.NAME_EN IS 'DBF-CAT-074 — English name (RULE-CAT-002)';
COMMENT ON COLUMN CAT_CATEGORY.SORT_ORDER IS 'DBF-CAT-075 — Order under the parent';
COMMENT ON COLUMN CAT_CATEGORY.IS_ACTIVE_FL IS 'DBF-CAT-076 — Active flag; deactivation, never deletion (RULE-CAT-048)';
COMMENT ON COLUMN CAT_CATEGORY.PARENT_CATEGORY_FK IS 'DBF-CAT-077 — Parent Category; empty = top level (RULE-CAT-023)';
COMMENT ON COLUMN CAT_CATEGORY.TENANT_ID IS 'DBF-CAT-078 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_CATEGORY.VERSION IS 'DBF-CAT-079 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_CATEGORY.CREATED_BY IS 'DBF-CAT-080 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_CATEGORY.CREATED_AT IS 'DBF-CAT-081 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_CATEGORY.UPDATED_BY IS 'DBF-CAT-082 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_CATEGORY.UPDATED_AT IS 'DBF-CAT-083 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_VAR_ATTR IS 'ENT-CAT-003 Variant attribute / خاصية المتغيّر — dimension Variants differ by';
COMMENT ON COLUMN CAT_VAR_ATTR.VARIANT_ATTRIBUTE_PK IS 'DBF-CAT-035 — Variant attribute identifier (identity)';
COMMENT ON COLUMN CAT_VAR_ATTR.CODE IS 'DBF-CAT-036 — Attribute code, unique per tenant (RULE-CAT-016)';
COMMENT ON COLUMN CAT_VAR_ATTR.NAME_AR IS 'DBF-CAT-037 — Arabic name, unique among active attributes (RULE-CAT-001, RULE-CAT-016)';
COMMENT ON COLUMN CAT_VAR_ATTR.NAME_EN IS 'DBF-CAT-038 — English name (RULE-CAT-002)';
COMMENT ON COLUMN CAT_VAR_ATTR.SORT_ORDER IS 'DBF-CAT-039 — List order';
COMMENT ON COLUMN CAT_VAR_ATTR.IS_ACTIVE_FL IS 'DBF-CAT-040 — Active flag (RULE-CAT-017)';
COMMENT ON COLUMN CAT_VAR_ATTR.TENANT_ID IS 'DBF-CAT-041 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_VAR_ATTR.VERSION IS 'DBF-CAT-042 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_VAR_ATTR.CREATED_BY IS 'DBF-CAT-043 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR.CREATED_AT IS 'DBF-CAT-044 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR.UPDATED_BY IS 'DBF-CAT-045 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR.UPDATED_AT IS 'DBF-CAT-046 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_SCALE_LABEL_LAYOUT IS 'ENT-CAT-011 Scale label layout / إعداد ملصق الميزان — tenant layout of 13-digit scale labels';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.SCALE_LABEL_LAYOUT_PK IS 'DBF-CAT-136 — Scale label layout identifier (identity)';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.KEY IS 'DBF-CAT-137 — Label prefix 02 or 20-29, unique among active layouts (RULE-CAT-034)';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.VALUE_AR IS 'DBF-CAT-138 — Arabic layout description';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.VALUE_EN IS 'DBF-CAT-139 — English layout description';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.SCALE_CODE_LENGTH IS 'DBF-CAT-140 — Scale code length 4-6, fixed after creation (ADR-CAT-064); embedded value takes 10 minus this length';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.EMBEDDED_VALUE_TYPE_CODE IS 'DBF-CAT-141 — Embedded value type, lookup CAT_SCALE_VALUE_TYPE code';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.VALUE_DECIMALS IS 'DBF-CAT-142 — Decimals of the embedded value 0-3';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.IS_ACTIVE_FL IS 'DBF-CAT-143 — Active flag';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.TENANT_ID IS 'DBF-CAT-144 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.VERSION IS 'DBF-CAT-145 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.CREATED_BY IS 'DBF-CAT-146 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.CREATED_AT IS 'DBF-CAT-147 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.UPDATED_BY IS 'DBF-CAT-148 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_SCALE_LABEL_LAYOUT.UPDATED_AT IS 'DBF-CAT-149 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_UNIT_EINVOICE_CODE IS 'ENT-CAT-012 Unit e-invoice code / رمز الوحدة في الفاتورة الإلكترونية — e-invoice unit of one unit of measure';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.UNIT_EINVOICE_CODE_PK IS 'DBF-CAT-150 — Unit e-invoice code identifier (identity)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.KEY IS 'DBF-CAT-151 — Unit of measure, lookup CAT_UOM code; one active row per unit (RULE-CAT-043)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.VALUE_AR IS 'DBF-CAT-152 — Arabic name, copied from the CAT_UOM label at save (not entered)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.VALUE_EN IS 'DBF-CAT-153 — English name, copied from the CAT_UOM label at save (not entered)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.EINVOICE_UNIT_CODE IS 'DBF-CAT-154 — E-invoice unit, lookup CAT_EINVOICE_UNIT code';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.IS_ACTIVE_FL IS 'DBF-CAT-155 — Active flag';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.TENANT_ID IS 'DBF-CAT-156 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.VERSION IS 'DBF-CAT-157 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.CREATED_BY IS 'DBF-CAT-158 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.CREATED_AT IS 'DBF-CAT-159 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.UPDATED_BY IS 'DBF-CAT-160 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_UNIT_EINVOICE_CODE.UPDATED_AT IS 'DBF-CAT-161 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_ITEM IS 'ENT-CAT-001 Item / صنف — the one catalogue record every channel reads';
COMMENT ON COLUMN CAT_ITEM.ITEM_PK IS 'DBF-CAT-001 — Item identifier (identity)';
COMMENT ON COLUMN CAT_ITEM.CODE IS 'DBF-CAT-002 — Item code from core number series CAT_ITEM, read-only (RULE-CAT-004)';
COMMENT ON COLUMN CAT_ITEM.NAME_AR IS 'DBF-CAT-003 — Arabic name, at least one Arabic letter, unique among active Items (RULE-CAT-001, RULE-CAT-003)';
COMMENT ON COLUMN CAT_ITEM.NAME_EN IS 'DBF-CAT-004 — English name; NAME_AR is returned when empty (RULE-CAT-002)';
COMMENT ON COLUMN CAT_ITEM.ITEM_KIND_CODE IS 'DBF-CAT-005 — Item kind, lookup CAT_ITEM_KIND code, fixed after first save (RULE-CAT-022)';
COMMENT ON COLUMN CAT_ITEM.BASE_UOM_CODE IS 'DBF-CAT-006 — Base unit of measure, lookup CAT_UOM code, fixed after first save (RULE-CAT-007)';
COMMENT ON COLUMN CAT_ITEM.BRAND_CODE IS 'DBF-CAT-007 — Brand, lookup CAT_BRAND code (RULE-CAT-006)';
COMMENT ON COLUMN CAT_ITEM.TAX_CLASS_CODE IS 'DBF-CAT-008 — Tax class, lookup CAT_TAX_CLASS code (RULE-CAT-006)';
COMMENT ON COLUMN CAT_ITEM.EINVOICE_CODE_TYPE_CODE IS 'DBF-CAT-009 — E-invoice code type, lookup CAT_EINVOICE_ITEM_CODE_TYPE code (RULE-CAT-042)';
COMMENT ON COLUMN CAT_ITEM.EINVOICE_CODE IS 'DBF-CAT-010 — E-invoice code (RULE-CAT-042)';
COMMENT ON COLUMN CAT_ITEM.DESCRIPTION_AR IS 'DBF-CAT-011 — Arabic description';
COMMENT ON COLUMN CAT_ITEM.DESCRIPTION_EN IS 'DBF-CAT-012 — English description';
COMMENT ON COLUMN CAT_ITEM.PUBLISH_STATE_CODE IS 'DBF-CAT-013 — Publish state, lookup CAT_PUBLISH_STATE code (RULE-CAT-041)';
COMMENT ON COLUMN CAT_ITEM.IS_ACTIVE_FL IS 'DBF-CAT-014 — Active flag; deactivation, never deletion (RULE-CAT-048)';
COMMENT ON COLUMN CAT_ITEM.CATEGORY_FK IS 'DBF-CAT-015 — Category of the Item (RULE-CAT-026)';
COMMENT ON COLUMN CAT_ITEM.TENANT_ID IS 'DBF-CAT-016 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_ITEM.VERSION IS 'DBF-CAT-017 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_ITEM.CREATED_BY IS 'DBF-CAT-018 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM.CREATED_AT IS 'DBF-CAT-019 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_ITEM.UPDATED_BY IS 'DBF-CAT-020 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM.UPDATED_AT IS 'DBF-CAT-021 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_VAR_ATTR_VALUE IS 'ENT-CAT-004 Variant attribute value / قيمة خاصية المتغيّر — one value of a Variant attribute';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.VARIANT_ATTRIBUTE_VALUE_PK IS 'DBF-CAT-047 — Variant attribute value identifier (identity)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.CODE IS 'DBF-CAT-048 — Value code, unique per attribute (RULE-CAT-016)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.NAME_AR IS 'DBF-CAT-049 — Arabic name, unique among active values of the attribute (RULE-CAT-001, RULE-CAT-016)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.NAME_EN IS 'DBF-CAT-050 — English name (RULE-CAT-002)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.SORT_ORDER IS 'DBF-CAT-051 — Order within the attribute';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.IS_ACTIVE_FL IS 'DBF-CAT-052 — Active flag (RULE-CAT-017)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.VARIANT_ATTRIBUTE_FK IS 'DBF-CAT-053 — Attribute the value belongs to, fixed after creation';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.TENANT_ID IS 'DBF-CAT-054 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.VERSION IS 'DBF-CAT-055 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.CREATED_BY IS 'DBF-CAT-056 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.CREATED_AT IS 'DBF-CAT-057 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.UPDATED_BY IS 'DBF-CAT-058 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_VALUE.UPDATED_AT IS 'DBF-CAT-059 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_VARIANT IS 'ENT-CAT-002 Variant / متغيّر الصنف — sellable, stockable version of an Item; names derived at read (D-15)';
COMMENT ON COLUMN CAT_VARIANT.VARIANT_PK IS 'DBF-CAT-022 — Variant identifier (identity)';
COMMENT ON COLUMN CAT_VARIANT.CODE IS 'DBF-CAT-023 — Variant code from core number series CAT_VARIANT, read-only (RULE-CAT-004)';
COMMENT ON COLUMN CAT_VARIANT.IS_DEFAULT_FL IS 'DBF-CAT-024 — True for one Variant per Item: the Variant created with the Item, until the default moves to an active Variant (REQ-CAT-025, ADR-CAT-072)';
COMMENT ON COLUMN CAT_VARIANT.EINVOICE_CODE_TYPE_CODE IS 'DBF-CAT-025 — E-invoice code type override, lookup CAT_EINVOICE_ITEM_CODE_TYPE code (RULE-CAT-042)';
COMMENT ON COLUMN CAT_VARIANT.EINVOICE_CODE IS 'DBF-CAT-026 — E-invoice code override (RULE-CAT-042)';
COMMENT ON COLUMN CAT_VARIANT.IS_ACTIVE_FL IS 'DBF-CAT-027 — Active flag (RULE-CAT-015, RULE-CAT-021, RULE-CAT-048)';
COMMENT ON COLUMN CAT_VARIANT.ITEM_FK IS 'DBF-CAT-028 — Item the Variant belongs to, fixed after creation';
COMMENT ON COLUMN CAT_VARIANT.TENANT_ID IS 'DBF-CAT-029 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_VARIANT.VERSION IS 'DBF-CAT-030 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_VARIANT.CREATED_BY IS 'DBF-CAT-031 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_VARIANT.CREATED_AT IS 'DBF-CAT-032 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_VARIANT.UPDATED_BY IS 'DBF-CAT-033 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_VARIANT.UPDATED_AT IS 'DBF-CAT-034 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_VAR_ATTR_ASSIGN IS 'ENT-CAT-005 Variant attribute assignment / إسناد قيمة خاصية للمتغيّر — the value a Variant carries for one attribute';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.VARIANT_ATTRIBUTE_ASSIGNMENT_PK IS 'DBF-CAT-060 — Assignment identifier (identity)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.IS_ACTIVE_FL IS 'DBF-CAT-061 — Active flag, follows the Variant (RULE-CAT-010)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.VARIANT_FK IS 'DBF-CAT-062 — Variant carrying the value';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.VARIANT_ATTRIBUTE_FK IS 'DBF-CAT-063 — Attribute; one active assignment per Variant and attribute (RULE-CAT-013)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.VARIANT_ATTRIBUTE_VALUE_FK IS 'DBF-CAT-064 — Value, of the same attribute (RULE-CAT-013)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.TENANT_ID IS 'DBF-CAT-065 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.VERSION IS 'DBF-CAT-066 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.CREATED_BY IS 'DBF-CAT-067 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.CREATED_AT IS 'DBF-CAT-068 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.UPDATED_BY IS 'DBF-CAT-069 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_VAR_ATTR_ASSIGN.UPDATED_AT IS 'DBF-CAT-070 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_BUNDLE_COMP IS 'ENT-CAT-007 Bundle component / مكوّن الحزمة — one component Variant of a Bundle and its quantity';
COMMENT ON COLUMN CAT_BUNDLE_COMP.BUNDLE_COMPONENT_PK IS 'DBF-CAT-084 — Bundle component identifier (identity)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.QUANTITY IS 'DBF-CAT-085 — Component quantity above zero, 3 decimals (RULE-CAT-018)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.SORT_ORDER IS 'DBF-CAT-086 — Line order';
COMMENT ON COLUMN CAT_BUNDLE_COMP.IS_ACTIVE_FL IS 'DBF-CAT-087 — Active flag; removing a line deactivates it (RULE-CAT-048)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.BUNDLE_ITEM_FK IS 'DBF-CAT-088 — Bundle Item (kind BUNDLE)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.COMPONENT_VARIANT_FK IS 'DBF-CAT-089 — Component Variant of a STANDARD Item, once per Bundle (RULE-CAT-019)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.TENANT_ID IS 'DBF-CAT-090 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.VERSION IS 'DBF-CAT-091 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_BUNDLE_COMP.CREATED_BY IS 'DBF-CAT-092 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_BUNDLE_COMP.CREATED_AT IS 'DBF-CAT-093 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_BUNDLE_COMP.UPDATED_BY IS 'DBF-CAT-094 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_BUNDLE_COMP.UPDATED_AT IS 'DBF-CAT-095 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_ITEM_UNIT IS 'ENT-CAT-008 Item unit / وحدة الصنف — extra unit of an Item with its factor to the base unit';
COMMENT ON COLUMN CAT_ITEM_UNIT.ITEM_UNIT_PK IS 'DBF-CAT-096 — Item unit identifier (identity)';
COMMENT ON COLUMN CAT_ITEM_UNIT.UOM_CODE IS 'DBF-CAT-097 — Unit of measure, lookup CAT_UOM code, once among active units (RULE-CAT-027)';
COMMENT ON COLUMN CAT_ITEM_UNIT.CONVERSION_FACTOR IS 'DBF-CAT-098 — Base units in one of this unit, above zero, fixed after saving (RULE-CAT-027, RULE-CAT-028)';
COMMENT ON COLUMN CAT_ITEM_UNIT.IS_PURCHASE_FL IS 'DBF-CAT-099 — Used for purchasing';
COMMENT ON COLUMN CAT_ITEM_UNIT.IS_SALES_FL IS 'DBF-CAT-100 — Used for selling';
COMMENT ON COLUMN CAT_ITEM_UNIT.IS_ACTIVE_FL IS 'DBF-CAT-101 — Active flag (RULE-CAT-048)';
COMMENT ON COLUMN CAT_ITEM_UNIT.ITEM_FK IS 'DBF-CAT-102 — Item the unit belongs to';
COMMENT ON COLUMN CAT_ITEM_UNIT.TENANT_ID IS 'DBF-CAT-103 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_ITEM_UNIT.VERSION IS 'DBF-CAT-104 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_ITEM_UNIT.CREATED_BY IS 'DBF-CAT-105 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_UNIT.CREATED_AT IS 'DBF-CAT-106 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_UNIT.UPDATED_BY IS 'DBF-CAT-107 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_UNIT.UPDATED_AT IS 'DBF-CAT-108 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_BARCODE IS 'ENT-CAT-009 Barcode / باركود — scannable code identifying a Variant in one unit';
COMMENT ON COLUMN CAT_BARCODE.BARCODE_PK IS 'DBF-CAT-109 — Barcode identifier (identity)';
COMMENT ON COLUMN CAT_BARCODE.BARCODE_VALUE IS 'DBF-CAT-110 — Barcode digits, unique among active barcodes, read-only (RULE-CAT-029 to RULE-CAT-033)';
COMMENT ON COLUMN CAT_BARCODE.BARCODE_TYPE_CODE IS 'DBF-CAT-111 — Barcode type, lookup CAT_BARCODE_TYPE code';
COMMENT ON COLUMN CAT_BARCODE.UOM_CODE IS 'DBF-CAT-112 — Unit the barcode identifies, lookup CAT_UOM code (RULE-CAT-031)';
COMMENT ON COLUMN CAT_BARCODE.IS_ACTIVE_FL IS 'DBF-CAT-113 — Active flag; inactive barcodes never match a lookup (RULE-CAT-048)';
COMMENT ON COLUMN CAT_BARCODE.VARIANT_FK IS 'DBF-CAT-114 — Variant the barcode identifies';
COMMENT ON COLUMN CAT_BARCODE.TENANT_ID IS 'DBF-CAT-115 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_BARCODE.VERSION IS 'DBF-CAT-116 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_BARCODE.CREATED_BY IS 'DBF-CAT-117 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_BARCODE.CREATED_AT IS 'DBF-CAT-118 — Creation time, filled by the platform; orders the earliest-created barcode for labels';
COMMENT ON COLUMN CAT_BARCODE.UPDATED_BY IS 'DBF-CAT-119 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_BARCODE.UPDATED_AT IS 'DBF-CAT-120 — Last update time, filled by the platform';

COMMENT ON TABLE CAT_ITEM_MEDIA IS 'ENT-CAT-010 Item media / وسائط الصنف — one image of an Item; the file is kept by the given file module';
COMMENT ON COLUMN CAT_ITEM_MEDIA.ITEM_MEDIA_PK IS 'DBF-CAT-121 — Item media identifier (identity)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.FILE_DOCUMENT_REF IS 'DBF-CAT-122 — Given erp-core FILE_DOCUMENT identifier in public image storage; soft read, no constraint, no XM';
COMMENT ON COLUMN CAT_ITEM_MEDIA.NAME_AR IS 'DBF-CAT-123 — Arabic image description, default the Item NAME_AR (RULE-CAT-001)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.NAME_EN IS 'DBF-CAT-124 — English image description (RULE-CAT-002)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.SORT_ORDER IS 'DBF-CAT-125 — Image order for the Item';
COMMENT ON COLUMN CAT_ITEM_MEDIA.IS_PRIMARY_FL IS 'DBF-CAT-126 — Main image; at most one active per Item, exactly one kept by the service (RULE-CAT-039)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.IS_ACTIVE_FL IS 'DBF-CAT-127 — Active flag; removing an image deactivates it';
COMMENT ON COLUMN CAT_ITEM_MEDIA.ITEM_FK IS 'DBF-CAT-128 — Item the image belongs to';
COMMENT ON COLUMN CAT_ITEM_MEDIA.VARIANT_FK IS 'DBF-CAT-129 — Optional Variant of the same Item (RULE-CAT-040)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.TENANT_ID IS 'DBF-CAT-130 — Owning tenant (given erp-core tenancy)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.VERSION IS 'DBF-CAT-131 — Optimistic lock version (given AuditableEntity)';
COMMENT ON COLUMN CAT_ITEM_MEDIA.CREATED_BY IS 'DBF-CAT-132 — Creating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_MEDIA.CREATED_AT IS 'DBF-CAT-133 — Creation time, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_MEDIA.UPDATED_BY IS 'DBF-CAT-134 — Last updating principal, filled by the platform';
COMMENT ON COLUMN CAT_ITEM_MEDIA.UPDATED_AT IS 'DBF-CAT-135 — Last update time, filled by the platform';

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 5 — CONSTRAINTS
-- ──────────────────────────────────────────────────────────────────

-- 5a — PRIMARY KEYS
ALTER TABLE CAT_CATEGORY           ADD CONSTRAINT PK_CAT_CATEGORY           PRIMARY KEY (CATEGORY_PK);
ALTER TABLE CAT_VAR_ATTR           ADD CONSTRAINT PK_CAT_VAR_ATTR           PRIMARY KEY (VARIANT_ATTRIBUTE_PK);
ALTER TABLE CAT_SCALE_LABEL_LAYOUT ADD CONSTRAINT PK_CAT_SCALE_LABEL_LAYOUT PRIMARY KEY (SCALE_LABEL_LAYOUT_PK);
ALTER TABLE CAT_UNIT_EINVOICE_CODE ADD CONSTRAINT PK_CAT_UNIT_EINVOICE_CODE PRIMARY KEY (UNIT_EINVOICE_CODE_PK);
ALTER TABLE CAT_ITEM               ADD CONSTRAINT PK_CAT_ITEM               PRIMARY KEY (ITEM_PK);
ALTER TABLE CAT_VAR_ATTR_VALUE     ADD CONSTRAINT PK_CAT_VAR_ATTR_VALUE     PRIMARY KEY (VARIANT_ATTRIBUTE_VALUE_PK);
ALTER TABLE CAT_VARIANT            ADD CONSTRAINT PK_CAT_VARIANT            PRIMARY KEY (VARIANT_PK);
ALTER TABLE CAT_VAR_ATTR_ASSIGN    ADD CONSTRAINT PK_CAT_VAR_ATTR_ASSIGN    PRIMARY KEY (VARIANT_ATTRIBUTE_ASSIGNMENT_PK);
ALTER TABLE CAT_BUNDLE_COMP        ADD CONSTRAINT PK_CAT_BUNDLE_COMP        PRIMARY KEY (BUNDLE_COMPONENT_PK);
ALTER TABLE CAT_ITEM_UNIT          ADD CONSTRAINT PK_CAT_ITEM_UNIT          PRIMARY KEY (ITEM_UNIT_PK);
ALTER TABLE CAT_BARCODE            ADD CONSTRAINT PK_CAT_BARCODE            PRIMARY KEY (BARCODE_PK);
ALTER TABLE CAT_ITEM_MEDIA         ADD CONSTRAINT PK_CAT_ITEM_MEDIA         PRIMARY KEY (ITEM_MEDIA_PK);

-- 5b — UNIQUE (composite with TENANT_ID per the given tenancy convention)
-- business keys (constraints)
ALTER TABLE CAT_CATEGORY       ADD CONSTRAINT UQ_CAT_CATEGORY_CODE       UNIQUE (TENANT_ID, CODE);                          -- RULE-CAT-024
ALTER TABLE CAT_VAR_ATTR       ADD CONSTRAINT UQ_CAT_VAR_ATTR_CODE       UNIQUE (TENANT_ID, CODE);                          -- RULE-CAT-016
ALTER TABLE CAT_ITEM           ADD CONSTRAINT UQ_CAT_ITEM_CODE           UNIQUE (TENANT_ID, CODE);                          -- RULE-CAT-004
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT UQ_CAT_VAR_ATTR_VALUE_CODE UNIQUE (TENANT_ID, VARIANT_ATTRIBUTE_FK, CODE);    -- RULE-CAT-016
ALTER TABLE CAT_VARIANT        ADD CONSTRAINT UQ_CAT_VARIANT_CODE        UNIQUE (TENANT_ID, CODE);                          -- RULE-CAT-004
-- targets of the tenant-composite FKs of 5d: every intra-module FK carries TENANT_ID, so a row can only
-- point to a parent of its own tenant (D-P2-12, ADR-CAT-067)
ALTER TABLE CAT_CATEGORY       ADD CONSTRAINT UQ_CAT_CATEGORY_PK            UNIQUE (TENANT_ID, CATEGORY_PK);
ALTER TABLE CAT_VAR_ATTR       ADD CONSTRAINT UQ_CAT_VAR_ATTR_PK            UNIQUE (TENANT_ID, VARIANT_ATTRIBUTE_PK);
ALTER TABLE CAT_ITEM           ADD CONSTRAINT UQ_CAT_ITEM_PK                UNIQUE (TENANT_ID, ITEM_PK);
ALTER TABLE CAT_VARIANT        ADD CONSTRAINT UQ_CAT_VARIANT_PK             UNIQUE (TENANT_ID, VARIANT_PK);
-- targets of the same-parent composite FKs of 5d (RULE-CAT-013, RULE-CAT-040)
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT UQ_CAT_VAR_ATTR_VALUE_PK_ATTR UNIQUE (TENANT_ID, VARIANT_ATTRIBUTE_VALUE_PK, VARIANT_ATTRIBUTE_FK);
ALTER TABLE CAT_VARIANT        ADD CONSTRAINT UQ_CAT_VARIANT_PK_ITEM        UNIQUE (TENANT_ID, VARIANT_PK, ITEM_FK);
-- uniqueness among active records (partial unique indexes)
CREATE UNIQUE INDEX UQ_CAT_CATEGORY_NAME_AR       ON CAT_CATEGORY (TENANT_ID, PARENT_CATEGORY_FK, NAME_AR) NULLS NOT DISTINCT WHERE IS_ACTIVE_FL;  -- RULE-CAT-024
CREATE UNIQUE INDEX UQ_CAT_VAR_ATTR_NAME_AR       ON CAT_VAR_ATTR (TENANT_ID, NAME_AR) WHERE IS_ACTIVE_FL;                                      -- RULE-CAT-016
CREATE UNIQUE INDEX UQ_CAT_SCALE_LABEL_LAYOUT_KEY ON CAT_SCALE_LABEL_LAYOUT (TENANT_ID, KEY) WHERE IS_ACTIVE_FL;                                 -- RULE-CAT-034
CREATE UNIQUE INDEX UQ_CAT_UNIT_EINVOICE_CODE_KEY ON CAT_UNIT_EINVOICE_CODE (TENANT_ID, KEY) WHERE IS_ACTIVE_FL;                                 -- RULE-CAT-043
CREATE UNIQUE INDEX UQ_CAT_ITEM_NAME_AR           ON CAT_ITEM (TENANT_ID, CAT_NORMALIZE_AR(NAME_AR)) WHERE IS_ACTIVE_FL;                         -- RULE-CAT-003, ADR-CAT-062
CREATE UNIQUE INDEX UQ_CAT_VAR_ATTR_VALUE_NAME_AR ON CAT_VAR_ATTR_VALUE (TENANT_ID, VARIANT_ATTRIBUTE_FK, NAME_AR) WHERE IS_ACTIVE_FL;          -- RULE-CAT-016
CREATE UNIQUE INDEX UQ_CAT_VARIANT_DEFAULT        ON CAT_VARIANT (TENANT_ID, ITEM_FK) WHERE IS_DEFAULT_FL;                                      -- REQ-CAT-025, ADR-CAT-072 (at most one default per Item; the service clears the former default before it sets the new one)
CREATE UNIQUE INDEX UQ_CAT_VAR_ATTR_ASSIGN_ATTR   ON CAT_VAR_ATTR_ASSIGN (TENANT_ID, VARIANT_FK, VARIANT_ATTRIBUTE_FK) WHERE IS_ACTIVE_FL;     -- RULE-CAT-013
CREATE UNIQUE INDEX UQ_CAT_BUNDLE_COMP_VARIANT    ON CAT_BUNDLE_COMP (TENANT_ID, BUNDLE_ITEM_FK, COMPONENT_VARIANT_FK) WHERE IS_ACTIVE_FL;     -- RULE-CAT-019
CREATE UNIQUE INDEX UQ_CAT_ITEM_UNIT_UOM          ON CAT_ITEM_UNIT (TENANT_ID, ITEM_FK, UOM_CODE) WHERE IS_ACTIVE_FL;                           -- RULE-CAT-027
CREATE UNIQUE INDEX UQ_CAT_BARCODE_VALUE          ON CAT_BARCODE (TENANT_ID, BARCODE_VALUE) WHERE IS_ACTIVE_FL;                                 -- RULE-CAT-029
CREATE UNIQUE INDEX UQ_CAT_ITEM_MEDIA_PRIMARY     ON CAT_ITEM_MEDIA (TENANT_ID, ITEM_FK) WHERE IS_PRIMARY_FL AND IS_ACTIVE_FL;                  -- RULE-CAT-039

-- 5c — CHECK
ALTER TABLE CAT_CATEGORY ADD CONSTRAINT CHK_CAT_CATEGORY_CODE CHECK (CODE ~ '^[A-Za-z0-9-]{1,30}$');                                     -- RULE-CAT-024
ALTER TABLE CAT_CATEGORY ADD CONSTRAINT CHK_CAT_CATEGORY_NAME_AR CHECK (NAME_AR ~ '[\u0621-\u064A]');                                     -- RULE-CAT-001
ALTER TABLE CAT_CATEGORY ADD CONSTRAINT CHK_CAT_CATEGORY_PARENT_CATEGORY_FK CHECK (PARENT_CATEGORY_FK IS NULL OR PARENT_CATEGORY_FK <> CATEGORY_PK);  -- RULE-CAT-023
ALTER TABLE CAT_VAR_ATTR ADD CONSTRAINT CHK_CAT_VAR_ATTR_CODE CHECK (CODE ~ '^[A-Za-z0-9-]{1,30}$');                                     -- RULE-CAT-016
ALTER TABLE CAT_VAR_ATTR ADD CONSTRAINT CHK_CAT_VAR_ATTR_NAME_AR CHECK (NAME_AR ~ '[\u0621-\u064A]');                                     -- RULE-CAT-001
ALTER TABLE CAT_SCALE_LABEL_LAYOUT ADD CONSTRAINT CHK_CAT_SCALE_LABEL_LAYOUT_KEY CHECK (KEY ~ '^(02|2[0-9])$');                            -- RULE-CAT-034
ALTER TABLE CAT_SCALE_LABEL_LAYOUT ADD CONSTRAINT CHK_CAT_SCALE_LABEL_LAYOUT_SCALE_CODE_LENGTH CHECK (SCALE_CODE_LENGTH BETWEEN 4 AND 6);  -- RULE-CAT-034
ALTER TABLE CAT_SCALE_LABEL_LAYOUT ADD CONSTRAINT CHK_CAT_SCALE_LABEL_LAYOUT_VALUE_DECIMALS CHECK (VALUE_DECIMALS BETWEEN 0 AND 3);         -- RULE-CAT-034
ALTER TABLE CAT_ITEM ADD CONSTRAINT CHK_CAT_ITEM_NAME_AR CHECK (NAME_AR ~ '[\u0621-\u064A]');                                             -- RULE-CAT-001
ALTER TABLE CAT_ITEM ADD CONSTRAINT CHK_CAT_ITEM_EINVOICE_CODE CHECK ((EINVOICE_CODE_TYPE_CODE IS NULL) = (EINVOICE_CODE IS NULL));       -- RULE-CAT-042
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT CHK_CAT_VAR_ATTR_VALUE_CODE CHECK (CODE ~ '^[A-Za-z0-9-]{1,30}$');                         -- RULE-CAT-016
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT CHK_CAT_VAR_ATTR_VALUE_NAME_AR CHECK (NAME_AR ~ '[\u0621-\u064A]');                         -- RULE-CAT-001
ALTER TABLE CAT_VARIANT ADD CONSTRAINT CHK_CAT_VARIANT_EINVOICE_CODE CHECK ((EINVOICE_CODE_TYPE_CODE IS NULL) = (EINVOICE_CODE IS NULL));  -- RULE-CAT-042
ALTER TABLE CAT_BUNDLE_COMP ADD CONSTRAINT CHK_CAT_BUNDLE_COMP_QUANTITY CHECK (QUANTITY > 0);                                             -- RULE-CAT-018
ALTER TABLE CAT_ITEM_UNIT ADD CONSTRAINT CHK_CAT_ITEM_UNIT_CONVERSION_FACTOR CHECK (CONVERSION_FACTOR > 0);                               -- RULE-CAT-027
ALTER TABLE CAT_BARCODE ADD CONSTRAINT CHK_CAT_BARCODE_BARCODE_VALUE CHECK (BARCODE_VALUE ~ '^[0-9]+$');                                  -- RULE-CAT-030, RULE-CAT-033
ALTER TABLE CAT_ITEM_MEDIA ADD CONSTRAINT CHK_CAT_ITEM_MEDIA_NAME_AR CHECK (NAME_AR ~ '[\u0621-\u064A]');                                 -- RULE-CAT-001

-- 5d — INTRA-MODULE FOREIGN KEYS (parent PK / UNIQUE already in place). Every one is composite with
-- TENANT_ID (D-P2-12, ADR-CAT-067); a NULL optional FK column skips the check (MATCH SIMPLE).
ALTER TABLE CAT_CATEGORY ADD CONSTRAINT FK_CAT_CATEGORY_CAT_CATEGORY
    FOREIGN KEY (TENANT_ID, PARENT_CATEGORY_FK) REFERENCES CAT_CATEGORY (TENANT_ID, CATEGORY_PK);
ALTER TABLE CAT_ITEM ADD CONSTRAINT FK_CAT_ITEM_CAT_CATEGORY
    FOREIGN KEY (TENANT_ID, CATEGORY_FK) REFERENCES CAT_CATEGORY (TENANT_ID, CATEGORY_PK);
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT FK_CAT_VAR_ATTR_VALUE_CAT_VAR_ATTR
    FOREIGN KEY (TENANT_ID, VARIANT_ATTRIBUTE_FK) REFERENCES CAT_VAR_ATTR (TENANT_ID, VARIANT_ATTRIBUTE_PK);
ALTER TABLE CAT_VARIANT ADD CONSTRAINT FK_CAT_VARIANT_CAT_ITEM
    FOREIGN KEY (TENANT_ID, ITEM_FK) REFERENCES CAT_ITEM (TENANT_ID, ITEM_PK);
ALTER TABLE CAT_VAR_ATTR_ASSIGN ADD CONSTRAINT FK_CAT_VAR_ATTR_ASSIGN_CAT_VARIANT
    FOREIGN KEY (TENANT_ID, VARIANT_FK) REFERENCES CAT_VARIANT (TENANT_ID, VARIANT_PK);
ALTER TABLE CAT_VAR_ATTR_ASSIGN ADD CONSTRAINT FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR
    FOREIGN KEY (TENANT_ID, VARIANT_ATTRIBUTE_FK) REFERENCES CAT_VAR_ATTR (TENANT_ID, VARIANT_ATTRIBUTE_PK);
ALTER TABLE CAT_VAR_ATTR_ASSIGN ADD CONSTRAINT FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE
    FOREIGN KEY (TENANT_ID, VARIANT_ATTRIBUTE_VALUE_FK, VARIANT_ATTRIBUTE_FK)
    REFERENCES CAT_VAR_ATTR_VALUE (TENANT_ID, VARIANT_ATTRIBUTE_VALUE_PK, VARIANT_ATTRIBUTE_FK);
ALTER TABLE CAT_BUNDLE_COMP ADD CONSTRAINT FK_CAT_BUNDLE_COMP_CAT_ITEM
    FOREIGN KEY (TENANT_ID, BUNDLE_ITEM_FK) REFERENCES CAT_ITEM (TENANT_ID, ITEM_PK);
ALTER TABLE CAT_BUNDLE_COMP ADD CONSTRAINT FK_CAT_BUNDLE_COMP_CAT_VARIANT
    FOREIGN KEY (TENANT_ID, COMPONENT_VARIANT_FK) REFERENCES CAT_VARIANT (TENANT_ID, VARIANT_PK);
ALTER TABLE CAT_ITEM_UNIT ADD CONSTRAINT FK_CAT_ITEM_UNIT_CAT_ITEM
    FOREIGN KEY (TENANT_ID, ITEM_FK) REFERENCES CAT_ITEM (TENANT_ID, ITEM_PK);
ALTER TABLE CAT_BARCODE ADD CONSTRAINT FK_CAT_BARCODE_CAT_VARIANT
    FOREIGN KEY (TENANT_ID, VARIANT_FK) REFERENCES CAT_VARIANT (TENANT_ID, VARIANT_PK);
ALTER TABLE CAT_ITEM_MEDIA ADD CONSTRAINT FK_CAT_ITEM_MEDIA_CAT_ITEM
    FOREIGN KEY (TENANT_ID, ITEM_FK) REFERENCES CAT_ITEM (TENANT_ID, ITEM_PK);
ALTER TABLE CAT_ITEM_MEDIA ADD CONSTRAINT FK_CAT_ITEM_MEDIA_CAT_VARIANT
    FOREIGN KEY (TENANT_ID, VARIANT_FK, ITEM_FK) REFERENCES CAT_VARIANT (TENANT_ID, VARIANT_PK, ITEM_FK);

-- 5e — TENANT FOREIGN KEYS (given erp-core CORE_TENANT, created by the given migration chain; erp-core checklist C1,
-- conventions § Migrations rule 5 — a permitted given reference, not an XM: ADR-CAT-055)
ALTER TABLE CAT_CATEGORY ADD CONSTRAINT FK_CAT_CATEGORY_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_VAR_ATTR ADD CONSTRAINT FK_CAT_VAR_ATTR_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_SCALE_LABEL_LAYOUT ADD CONSTRAINT FK_CAT_SCALE_LABEL_LAYOUT_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_UNIT_EINVOICE_CODE ADD CONSTRAINT FK_CAT_UNIT_EINVOICE_CODE_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_ITEM ADD CONSTRAINT FK_CAT_ITEM_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_VAR_ATTR_VALUE ADD CONSTRAINT FK_CAT_VAR_ATTR_VALUE_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_VARIANT ADD CONSTRAINT FK_CAT_VARIANT_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_VAR_ATTR_ASSIGN ADD CONSTRAINT FK_CAT_VAR_ATTR_ASSIGN_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_BUNDLE_COMP ADD CONSTRAINT FK_CAT_BUNDLE_COMP_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_ITEM_UNIT ADD CONSTRAINT FK_CAT_ITEM_UNIT_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_BARCODE ADD CONSTRAINT FK_CAT_BARCODE_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);
ALTER TABLE CAT_ITEM_MEDIA ADD CONSTRAINT FK_CAT_ITEM_MEDIA_TENANT
    FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID);


-- ──────────────────────────────────────────────────────────────────
-- BLOCK 6 — TRIGGERS
-- none: no SRS RULE requires a trigger; audit columns are filled by the platform; no PK trigger.
-- ──────────────────────────────────────────────────────────────────

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 7 — INDEXES (non-PK)
-- ──────────────────────────────────────────────────────────────────

-- tenant-leading index (given tenancy convention) where no non-partial UNIQUE already leads with TENANT_ID
CREATE INDEX IDX_CAT_ITEM_TENANT_ID                ON CAT_ITEM (TENANT_ID);
CREATE INDEX IDX_CAT_CATEGORY_TENANT_ID            ON CAT_CATEGORY (TENANT_ID);
CREATE INDEX IDX_CAT_VAR_ATTR_ASSIGN_TENANT_ID    ON CAT_VAR_ATTR_ASSIGN (TENANT_ID);
CREATE INDEX IDX_CAT_BUNDLE_COMP_TENANT_ID        ON CAT_BUNDLE_COMP (TENANT_ID);
CREATE INDEX IDX_CAT_ITEM_UNIT_TENANT_ID          ON CAT_ITEM_UNIT (TENANT_ID);
CREATE INDEX IDX_CAT_BARCODE_TENANT_ID            ON CAT_BARCODE (TENANT_ID);
CREATE INDEX IDX_CAT_ITEM_MEDIA_TENANT_ID         ON CAT_ITEM_MEDIA (TENANT_ID);
CREATE INDEX IDX_CAT_SCALE_LABEL_LAYOUT_TENANT_ID ON CAT_SCALE_LABEL_LAYOUT (TENANT_ID);
CREATE INDEX IDX_CAT_UNIT_EINVOICE_CODE_TENANT_ID ON CAT_UNIT_EINVOICE_CODE (TENANT_ID);

-- every FK column
CREATE INDEX IDX_CAT_CATEGORY_PARENT_CATEGORY_FK          ON CAT_CATEGORY (PARENT_CATEGORY_FK);
CREATE INDEX IDX_CAT_ITEM_CATEGORY_FK                     ON CAT_ITEM (CATEGORY_FK);
CREATE INDEX IDX_CAT_VAR_ATTR_VALUE_VARIANT_ATTRIBUTE_FK  ON CAT_VAR_ATTR_VALUE (VARIANT_ATTRIBUTE_FK);
CREATE INDEX IDX_CAT_VARIANT_ITEM_FK                      ON CAT_VARIANT (ITEM_FK);
CREATE INDEX IDX_CAT_VAR_ATTR_ASSIGN_VARIANT_FK           ON CAT_VAR_ATTR_ASSIGN (VARIANT_FK);
CREATE INDEX IDX_CAT_VAR_ATTR_ASSIGN_VARIANT_ATTRIBUTE_FK ON CAT_VAR_ATTR_ASSIGN (VARIANT_ATTRIBUTE_FK);
CREATE INDEX IDX_CAT_VAR_ATTR_ASSIGN_VARIANT_ATTRIBUTE_VALUE_FK ON CAT_VAR_ATTR_ASSIGN (VARIANT_ATTRIBUTE_VALUE_FK);
CREATE INDEX IDX_CAT_BUNDLE_COMP_BUNDLE_ITEM_FK           ON CAT_BUNDLE_COMP (BUNDLE_ITEM_FK);
CREATE INDEX IDX_CAT_BUNDLE_COMP_COMPONENT_VARIANT_FK     ON CAT_BUNDLE_COMP (COMPONENT_VARIANT_FK);
CREATE INDEX IDX_CAT_ITEM_UNIT_ITEM_FK                    ON CAT_ITEM_UNIT (ITEM_FK);
CREATE INDEX IDX_CAT_BARCODE_VARIANT_FK                   ON CAT_BARCODE (VARIANT_FK);
CREATE INDEX IDX_CAT_ITEM_MEDIA_ITEM_FK                   ON CAT_ITEM_MEDIA (ITEM_FK);
CREATE INDEX IDX_CAT_ITEM_MEDIA_VARIANT_FK                ON CAT_ITEM_MEDIA (VARIANT_FK);

-- search / list filters (SRS PART B B2); codes and active barcodes are covered by the UNIQUE indexes of 5b
CREATE INDEX IDX_CAT_ITEM_NAME_AR            ON CAT_ITEM (NAME_AR);
CREATE INDEX IDX_CAT_ITEM_NAME_EN            ON CAT_ITEM (NAME_EN);
CREATE INDEX IDX_CAT_ITEM_BRAND_CODE         ON CAT_ITEM (BRAND_CODE);
CREATE INDEX IDX_CAT_ITEM_ITEM_KIND_CODE     ON CAT_ITEM (ITEM_KIND_CODE);
CREATE INDEX IDX_CAT_ITEM_PUBLISH_STATE_CODE ON CAT_ITEM (PUBLISH_STATE_CODE);
CREATE INDEX IDX_CAT_ITEM_IS_ACTIVE_FL       ON CAT_ITEM (IS_ACTIVE_FL);
CREATE INDEX IDX_CAT_CATEGORY_NAME_AR        ON CAT_CATEGORY (NAME_AR);
CREATE INDEX IDX_CAT_CATEGORY_NAME_EN        ON CAT_CATEGORY (NAME_EN);
CREATE INDEX IDX_CAT_CATEGORY_IS_ACTIVE_FL   ON CAT_CATEGORY (IS_ACTIVE_FL);
CREATE INDEX IDX_CAT_VAR_ATTR_NAME_AR        ON CAT_VAR_ATTR (NAME_AR);
CREATE INDEX IDX_CAT_VAR_ATTR_IS_ACTIVE_FL   ON CAT_VAR_ATTR (IS_ACTIVE_FL);

-- substring search (LIKE '%…%' with ESCAPE) on names, codes and barcodes — trigram GIN (ADR-CAT-062)
CREATE INDEX IDX_CAT_ITEM_NAME_AR_TRGM          ON CAT_ITEM USING gin (CAT_NORMALIZE_AR(NAME_AR) gin_trgm_ops);
CREATE INDEX IDX_CAT_ITEM_NAME_EN_TRGM          ON CAT_ITEM USING gin (CAT_NORMALIZE_AR(NAME_EN) gin_trgm_ops);
CREATE INDEX IDX_CAT_ITEM_CODE_TRGM             ON CAT_ITEM USING gin (CODE gin_trgm_ops);
CREATE INDEX IDX_CAT_VARIANT_CODE_TRGM          ON CAT_VARIANT USING gin (CODE gin_trgm_ops);
CREATE INDEX IDX_CAT_BARCODE_BARCODE_VALUE_TRGM ON CAT_BARCODE USING gin (BARCODE_VALUE gin_trgm_ops);

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 8 — LOOKUP AND REFERENCE SEED DATA (PLATFORM tenant, TENANT_ID = 1).
-- New tenants (ADR-CAT-058, REQ-CAT-134): the given copies 8a/8b (erp-core mdl) and 8c (sequence,
-- counter at 1) from PLATFORM at provisioning; CatTenantProvisioningContributor (given TenantProvisioningContributor)
-- copies 8d with an explicit TENANT_ID.
-- Column names verified against erp-core 1.6.0 (V3__mdl_schema.sql + V10__tenant_schema.sql for MDL_*,
-- V14__sequence_and_settings.sql for CORE_NUMBER_SERIES, V10 for CORE_TENANT ID 1 = 'PLATFORM') and written
-- the way the core's own seed V8__mdl_seed.sql writes them (D-P2-13). VERSION defaults to 0 in each table.
-- No COMMIT: Flyway runs a PostgreSQL migration in its own transaction and commits it.
-- ──────────────────────────────────────────────────────────────────

-- 8a — lookup types. Table owned by erp-core modules.mdl (MDL_LOOKUP_TYPE); the keys are owned by CAT (SRS A6).
-- OWNER_MODULE_CODE 'CAT' is the permission module CatPermissions declares (RULE-MDL-001 is checked
-- application-side against SEC_MODULE_REG, which the given fills from the contributor at startup).
INSERT INTO MDL_LOOKUP_TYPE (LOOKUP_TYPE_PK, TENANT_ID, KEY, OWNER_MODULE_CODE, NAME_AR, NAME_EN, IS_ACTIVE_FL, CREATED_BY, CREATED_AT)
SELECT nextval('SEQ_MDL_LOOKUP_TYPE'), 1, v.TYPE_KEY, 'CAT', v.NAME_AR, v.NAME_EN, TRUE, 'SYSTEM', CURRENT_TIMESTAMP
FROM (VALUES
    (1, 'CAT_ITEM_KIND', 'نوع الصنف', 'Item kind'),
    (2, 'CAT_PUBLISH_STATE', 'حالة النشر', 'Publish state'),
    (3, 'CAT_BRAND', 'العلامة التجارية', 'Brand'),
    (4, 'CAT_UOM', 'وحدة القياس', 'Unit of measure'),
    (5, 'CAT_TAX_CLASS', 'التصنيف الضريبي', 'Tax class'),
    (6, 'CAT_EINVOICE_ITEM_CODE_TYPE', 'نوع رمز الفاتورة الإلكترونية', 'E-invoice code type'),
    (7, 'CAT_EINVOICE_UNIT', 'وحدة الفاتورة الإلكترونية', 'E-invoice unit'),
    (8, 'CAT_BARCODE_TYPE', 'نوع الباركود', 'Barcode type'),
    (9, 'CAT_SCALE_VALUE_TYPE', 'نوع القيمة المضمّنة', 'Embedded value type'),
    (10, 'CAT_CHANGE_KIND', 'نوع تغيير الكتالوج', 'Catalogue change kind')
) AS v (ORD, TYPE_KEY, NAME_AR, NAME_EN)
ORDER BY v.ORD;

-- 8b — lookup values. Table owned by erp-core modules.mdl (MDL_LOOKUP_VALUE); the values are owned by CAT (SRS A6).
-- CAT_BRAND has no seeded value (the tenant builds its own list). LOOKUP_TYPE_ID is resolved by KEY.
INSERT INTO MDL_LOOKUP_VALUE (LOOKUP_VALUE_PK, TENANT_ID, LOOKUP_TYPE_ID, CODE, NAME_AR, NAME_EN, SORT_ORDER, IS_ACTIVE_FL, CREATED_BY, CREATED_AT)
SELECT nextval('SEQ_MDL_LOOKUP_VALUE'), 1, t.LOOKUP_TYPE_PK, v.CODE, v.NAME_AR, v.NAME_EN, v.SORT_ORDER, TRUE, 'SYSTEM', CURRENT_TIMESTAMP
FROM (VALUES
    ('CAT_ITEM_KIND', 'STANDARD', 'صنف عادي', 'Standard item', 1),
    ('CAT_ITEM_KIND', 'BUNDLE', 'حزمة', 'Bundle', 2),
    ('CAT_PUBLISH_STATE', 'UNPUBLISHED', 'غير منشور', 'Not published', 1),
    ('CAT_PUBLISH_STATE', 'PUBLISHED', 'منشور', 'Published', 2),
    ('CAT_UOM', 'PIECE', 'قطعة', 'Piece', 1),
    ('CAT_UOM', 'CARTON', 'كرتونة', 'Carton', 2),
    ('CAT_UOM', 'BOX', 'علبة', 'Box', 3),
    ('CAT_UOM', 'DOZEN', 'دستة', 'Dozen', 4),
    ('CAT_UOM', 'KILOGRAM', 'كيلوجرام', 'Kilogram', 5),
    ('CAT_UOM', 'GRAM', 'جرام', 'Gram', 6),
    ('CAT_UOM', 'METRE', 'متر', 'Metre', 7),
    ('CAT_UOM', 'LITRE', 'لتر', 'Litre', 8),
    ('CAT_TAX_CLASS', 'STANDARD', 'خاضع للنسبة العامة', 'Standard rate', 1),
    ('CAT_TAX_CLASS', 'ZERO_RATED', 'خاضع لنسبة صفر', 'Zero-rated', 2),
    ('CAT_TAX_CLASS', 'EXEMPT', 'معفى', 'Exempt', 3),
    ('CAT_EINVOICE_ITEM_CODE_TYPE', 'GS1', 'GS1', 'GS1', 1),
    ('CAT_EINVOICE_ITEM_CODE_TYPE', 'EGS', 'EGS', 'EGS', 2),
    ('CAT_EINVOICE_UNIT', 'EA', 'وحدة', 'Each', 1),
    ('CAT_EINVOICE_UNIT', 'KGM', 'كيلوجرام', 'Kilogram', 2),
    ('CAT_EINVOICE_UNIT', 'GRM', 'جرام', 'Gram', 3),
    ('CAT_EINVOICE_UNIT', 'MTR', 'متر', 'Metre', 4),
    ('CAT_EINVOICE_UNIT', 'LTR', 'لتر', 'Litre', 5),
    ('CAT_EINVOICE_UNIT', 'CT', 'كرتونة', 'Carton', 6),
    ('CAT_EINVOICE_UNIT', 'BX', 'علبة', 'Box', 7),
    ('CAT_EINVOICE_UNIT', 'DZN', 'دستة', 'Dozen', 8),
    ('CAT_BARCODE_TYPE', 'GTIN', 'GTIN', 'GTIN', 1),
    ('CAT_BARCODE_TYPE', 'INTERNAL', 'داخلي', 'Internal', 2),
    ('CAT_BARCODE_TYPE', 'SCALE', 'ميزان', 'Scale', 3),
    ('CAT_SCALE_VALUE_TYPE', 'WEIGHT', 'وزن', 'Weight', 1),
    ('CAT_SCALE_VALUE_TYPE', 'PRICE', 'سعر', 'Price', 2),
    ('CAT_CHANGE_KIND', 'CREATED', 'إنشاء', 'Created', 1),
    ('CAT_CHANGE_KIND', 'UPDATED', 'تعديل', 'Updated', 2),
    ('CAT_CHANGE_KIND', 'DEACTIVATED', 'إيقاف', 'Deactivated', 3),
    ('CAT_CHANGE_KIND', 'REACTIVATED', 'إعادة تفعيل', 'Reactivated', 4)
) AS v (TYPE_KEY, CODE, NAME_AR, NAME_EN, SORT_ORDER)
JOIN MDL_LOOKUP_TYPE t ON t.TENANT_ID = 1 AND t.KEY = v.TYPE_KEY;

-- 8c — number series (SRS D-11). Table owned by erp-core modules.sequence (CORE_NUMBER_SERIES); the series codes are owned by CAT.
-- PERIOD_KEY ('' for NEVER), NEXT_VALUE (1), IS_ACTIVE (TRUE), CREATED_AT and VERSION take their column defaults.
INSERT INTO CORE_NUMBER_SERIES (ID, TENANT_ID, CODE, PREFIX, PATTERN, RESET_POLICY, CREATED_BY)
VALUES
    (nextval('SEQ_CORE_NUMBER_SERIES'), 1, 'CAT_ITEM', 'ITM', '{PREFIX}-{SEQ:6}', 'NEVER', 'SYSTEM'),
    (nextval('SEQ_CORE_NUMBER_SERIES'), 1, 'CAT_VARIANT', 'VAR', '{PREFIX}-{SEQ:6}', 'NEVER', 'SYSTEM'),
    (nextval('SEQ_CORE_NUMBER_SERIES'), 1, 'CAT_INTERNAL_BARCODE', '04', '{SEQ:10}', 'NEVER', 'SYSTEM');

-- 8d — Unit e-invoice codes (SRS D-12: CAT_UOM → CAT_EINVOICE_UNIT). Table created by this script.
INSERT INTO CAT_UNIT_EINVOICE_CODE (KEY, VALUE_AR, VALUE_EN, EINVOICE_UNIT_CODE, IS_ACTIVE_FL, TENANT_ID, CREATED_BY, CREATED_AT)
VALUES
    ('PIECE', 'قطعة', 'Piece', 'EA', TRUE, 1, 'system', now()),
    ('CARTON', 'كرتونة', 'Carton', 'CT', TRUE, 1, 'system', now()),
    ('BOX', 'علبة', 'Box', 'BX', TRUE, 1, 'system', now()),
    ('DOZEN', 'دستة', 'Dozen', 'DZN', TRUE, 1, 'system', now()),
    ('KILOGRAM', 'كيلوجرام', 'Kilogram', 'KGM', TRUE, 1, 'system', now()),
    ('GRAM', 'جرام', 'Gram', 'GRM', TRUE, 1, 'system', now()),
    ('METRE', 'متر', 'Metre', 'MTR', TRUE, 1, 'system', now()),
    ('LITRE', 'لتر', 'Litre', 'LTR', TRUE, 1, 'system', now());

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 9 — VIEWS
-- none: no SRS requirement asks for a view; derived Variant names are composed at read by the service.
-- ──────────────────────────────────────────────────────────────────

-- ──────────────────────────────────────────────────────────────────
-- BLOCK 10 — FUNCTIONS / PROCEDURES
-- CAT_NORMALIZE_AR is created in BLOCK 1 (it must exist before BLOCK 5b and BLOCK 7). No procedure.
-- ──────────────────────────────────────────────────────────────────
```

### Self-verification (§7.2)

| Check | Result |
|---|---|
| SYNTAX S-1 … S-8 | Every statement ends with `;`. No trailing comma. Every CAT object referenced is created above. The given tables of BLOCK 8 (MDL_LOOKUP_TYPE, MDL_LOOKUP_VALUE, CORE_NUMBER_SERIES) and the FK target CORE_TENANT of BLOCK 5e are created by the given's own migration chain, which runs before application scripts. Types come only from §4.1, plus INTEGER declared in the header. All constraints are in ALTER TABLE form. No PK trigger, no PK sequence default. INSERTs carry column lists, named as erp-core 1.6.0 names them (D-P2-13). No transaction statement: Flyway owns the migration's transaction. No schema prefix. No placeholders |
| PK strategy | 12 of 12 PK columns use `GENERATED ALWAYS AS IDENTITY`. No PK sequence |
| Order | Parent tables come before child tables. Every PK is added before any UNIQUE or FK, and the UNIQUE targets of the tenant-composite FKs are added before those FKs. The lookup types are inserted before the lookup values |
| Deployment preconditions | The given core migration chain (V1 … V34 of erp-core 1.6.0) has run. The Flyway role holds CREATE on the database for `pg_trgm`, or a DBA has installed `pg_trgm` beforehand (BLOCK 1) |
| Completeness | 12 of 12 ENT have a table, and every stored SRS field has a DBF. The two derived Variant names are not stored (D-P2-08). All 10 lookup keys are seeded (34 values). The shared lookup tables are not re-created |
| Cross-module | No XM, so no cross-module column. No constraint references another business module's table. The only constraint to a given table is `FK_<TABLE>_TENANT → CORE_TENANT(ID)` on the 12 tables (checklist C1, ADR-CAT-055); FILE_DOCUMENT_REF and the lookup and series codes are soft reads with no constraint. Every intra-module FK is composite with TENANT_ID (D-P2-12, ADR-CAT-067) |
| Trace | Every DBF traces to its ENT.field and to at least 1 REQ. No XM exists, so no orphan XM and no dangling id |

## 5 — Decisions applied

| DEFAULT | What | Source | Override |
|---|---|---|---|
| D-P2-01 | Identifier transformation: camelCase → UPPER_SNAKE_CASE, unquoted. PostgreSQL stores the name in lower case, which matches the given's physical naming. Tables are named `CAT_` + entity abbreviation (VAR_ATTR = Variant attribute, COMP = component) | brief §3; given migrations convention | Rename in a later delta (breaking for consumers of physical names) |
| D-P2-02 | Every table carries the given entity column set: TENANT_ID, VERSION, CREATED_BY, CREATED_AT, UPDATED_BY, UPDATED_AT. This includes the lookup tables (ENT-CAT-003, 004) and config tables (ENT-CAT-011, 012), whose SRS kind lists no audit fields. The source is the given's tenancy rule (every table has `TENANT_ID BIGINT NOT NULL`; every entity extends `AuditableEntity`). SRS A2 says "Tenancy is handled by the given core", and SRS A3 says audit fields are filled by the system. Without these columns the given's base entity cannot map the table | SRS A2, A3; given erp-core 1.6.0 conventions (tenancy, migrations) | Drop the columns from the lookup and config tables only if the given allows an entity that does not extend `AuditableEntity` |
| D-P2-03 | Every table has a `TENANT_ID` NOT NULL column, an index led by `TENANT_ID` (a non-partial UNIQUE of 5b or an `IDX_<TABLE>_TENANT_ID` of BLOCK 7), and every UNIQUE composite with `TENANT_ID`. Every table also carries `FK_<TABLE>_TENANT FOREIGN KEY (TENANT_ID) REFERENCES CORE_TENANT (ID)` (BLOCK 5e). The tenant table is the given erp-core `CORE_TENANT`, so the FK is a permitted given reference and not an XM. This supersedes ADR-CAT-054. The factory's explicit exemption of this FK from C6.15 is OPEN platform finding 1 in business-policies-cat.md | brief §5.1; erp-core checklist C1; conventions § Migrations rule 5; ADR-CAT-055 | — |
| D-P2-04 | The PK is `{entity}Pk` with the identity clause. This replaces the given's `ID` + `SEQ_<TABLE>` default, because the profile decision governs and one database carries one PK strategy | profile `stack.db.pk_generation`, `naming.pk_pattern`; brief §4 | Profile change only |
| D-P2-05 | Lookup-backed columns are `VARCHAR(50)` and hold the code. They carry no CHECK of allowed codes and no DB default. The service applies the SRS defaults (STANDARD, PIECE, STANDARD, UNPUBLISHED — D-09) and validates against the active values in `mdl` (RULE-CAT-006) | profile lookups rule; given lookups convention | — |
| D-P2-06 | Logical `number` → `INTEGER`, declared once in the header. Decimal precision: `quantity` NUMERIC(18,3) and `conversionFactor` NUMERIC(18,6). The SRS fixes the scale only; 15 and 12 integer digits are enough for any retail quantity | SRS A3 (3 and 6 decimals) | Widen the precision in a later delta (non-breaking) |
| D-P2-07 | `createdBy` and `updatedBy` are `VARCHAR(100)` principal strings. The created pair is NOT NULL and the updated pair is NULL until the first update | brief §3 audit rule; given migrations convention | — |
| D-P2-08 | Variant `nameAr` and `nameEn` are derived at read (SRS D-15) and are not stored. A stored copy would go stale when a value is renamed (AC-CAT-048) | SRS ENT-CAT-002, D-15 | Store an owner override name if D-15 changes |
| D-P2-09 | Unit e-invoice code `valueAr` and `valueEn` are stored (config-kind defaults) and nullable. The service copies them from the CAT_UOM labels at save; they are never entered | SRS ENT-CAT-012; profile entity_defaults.config | Derive at read and drop the columns in a later delta |
| D-P2-10 | Rules that apply among active records are partial unique indexes named `UQ_…`. The Item Arabic name is compared after `CAT_NORMALIZE_AR` (tashkeel and tatweel dropped, alef / taa marbuta / alef maqsura folded, Latin lower-cased, spaces trimmed and collapsed — RULE-CAT-003, ADR-CAT-062). Rows from the same import file are checked by the service | SRS RULE-CAT-003, 016, 019, 024, 027, 029, 034, 039, 043 | — |
| D-P2-11 | Barcode uniqueness is one partial unique index on the value among active barcodes. A SCALE code has 4–6 digits (RULE-CAT-033, RULE-CAT-034); a GTIN has 8–14 and an INTERNAL code has 13. So a SCALE code can never equal a GTIN or INTERNAL value, and RULE-CAT-029's per-type comparison holds without naming a lookup code in DDL | SRS RULE-CAT-029, 030, 033 | — |
| D-P2-12 | Same-tenant and same-parent integrity are structural. Every intra-module FK of 5d is composite with `TENANT_ID` and targets a `(TENANT_ID, <PK>)` UNIQUE of its parent (UQ_CAT_CATEGORY_PK, UQ_CAT_VAR_ATTR_PK, UQ_CAT_ITEM_PK, UQ_CAT_VARIANT_PK), so a row can never point to a parent of another tenant, even through a native query or a script that bypasses the given tenant filter (ADR-CAT-067). Two FKs also hold a same-parent rule: an assignment's value belongs to its attribute (RULE-CAT-013), and a media record's Variant belongs to its Item (RULE-CAT-040; no check when the Variant is empty — MATCH SIMPLE) | SRS RULE-CAT-013, RULE-CAT-040; erp-core conventions § Tenancy; ADR-CAT-067 | — |
| D-P2-13 | Seeds go into the given's tables by the columns of erp-core 1.6.0, checked against its migrations (gate-analysis round 2, G14): `MDL_LOOKUP_TYPE (LOOKUP_TYPE_PK from SEQ_MDL_LOOKUP_TYPE, TENANT_ID, KEY, OWNER_MODULE_CODE VARCHAR(10), NAME_AR, NAME_EN — both NOT NULL —, IS_ACTIVE_FL, CREATED_BY, CREATED_AT, VERSION default 0)` (V3 + V10); `MDL_LOOKUP_VALUE (LOOKUP_VALUE_PK from SEQ_MDL_LOOKUP_VALUE, TENANT_ID, LOOKUP_TYPE_ID → LOOKUP_TYPE_PK, CODE, NAME_AR, NAME_EN, SORT_ORDER, IS_ACTIVE_FL, CREATED_BY, CREATED_AT)` (V3 + V10); `CORE_NUMBER_SERIES (ID from SEQ_CORE_NUMBER_SERIES, TENANT_ID, CODE, PREFIX, PATTERN, RESET_POLICY; PERIOD_KEY, NEXT_VALUE, IS_ACTIVE, CREATED_AT, VERSION by default)` (V14); `CORE_TENANT` row ID 1 = `PLATFORM` (V10). Result: the earlier names `ID`, `LABEL_AR`, `LABEL_EN` of the mdl tables were wrong and are replaced, `NAME_AR` / `NAME_EN` are added to the type rows, and the series columns and the PLATFORM id were right. The rows follow the core's own seed `V8__mdl_seed.sql` (types by KEY, values joined by KEY) | brief §4.2 (the key owner owns the seed); given lookups convention; erp-core 1.6.0 V3, V8, V10, V14 | Adjust the column names only if a later erp-core release renames them; keys and values are fixed by SRS A6 |
| D-P2-14 | The number series CAT_ITEM (ITM-000001), CAT_VARIANT (VAR-000001) and CAT_INTERNAL_BARCODE (10 digits) are seeded in `CORE_NUMBER_SERIES` with RESET_POLICY NEVER, so the first save does not fail with `SEQUENCE_NOT_CONFIGURED`. The given copies the series to every new tenant with the counter at 1, and the Unit e-invoice codes are copied by `CatTenantProvisioningContributor` (REQ-CAT-134, ADR-CAT-058) | SRS D-11; given numbering convention; ADR-CAT-058 | Patterns are configurable per tenant in the core sequence module |
| D-P2-15 | The 8 Unit e-invoice codes of D-12 are seeded for the PLATFORM tenant (AC-CAT-097 lists 8 active ones) | SRS D-12, ENT-CAT-012 | Re-map against the authority's code table |
| D-P2-16 | These rules stay with the service and are not DDL, because they need other rows, flags, files or sequences: RULE-CAT-004, 005, 006, 007, 008, 009, 010, 011, 012, 013 (unique combination), 014, 015, 017, 018 (at least one line), 019 (active, STANDARD), 020, 021, 022, 023 (active parent, no cycle, depth ≤ 5), 025, 026, 027 (differs from base unit), 028, 030 (check digit), 031, 032, 033 (length), 035, 036, 037, 038, 039 (exactly one), 040 (when the Variant is empty), 041, 042 (GS1 / EGS format), 044, 045, 046, 047, 048, 049 | SRS A5 | — |
| D-P2-17 | Profile check CM-8: CAT holds no stock quantity, no monetary amount and no price (SRS A2 out of scope: PRC, INV). No warehouse, currency or price-list dimension applies to a CAT row | SRS A2; profile CM-8 | — |
| D-P2-18 | Delta: the current state holds no earlier CAT db-script, so this v2 migration creates every object. The DBF sequence starts at 001 (next free 162). No XM exists (next free XM sequence 001) | brief delta rule; change-manifest CS-CAT-001 | — |

No question was asked. ADR-CAT-054 (repair round 1) is superseded by ADR-CAT-055 (gate-analysis revise); ADR-CAT-058 (tenant provisioning), ADR-CAT-062 (Arabic normalisation, trigram indexes) and ADR-CAT-067 (tenant-composite intra-module FKs) also shape this script. ADR-CAT-072 (the default Variant moves) changes only the comments of `IS_DEFAULT_FL` and `UQ_CAT_VARIANT_DEFAULT`: the index stays non-partial, because the service clears the former default before it sets the new one. The next free ADR sequence for CAT, per the current registries, is 077. Profile check CM-3 holds: every flag column (`IS_ACTIVE_FL`, `IS_DEFAULT_FL`, `IS_PURCHASE_FL`, `IS_SALES_FL`, `IS_PRIMARY_FL`) ends with the flag suffix `Fl` under the transformation of D-P2-01.

## 6 — Registry content

See `registry-db-cat.md` (same content). Event: "P2 completed: CAT v2 — 12 tables, 161 DBF, 0 XM".


<<<END INPUT>>>

<<<INPUT: registry-db (baseline — continue it, never restart it)>>>
﻿## REGISTRY — P2 — CAT v2

### Tables
| Table | ENT id | Kind | DBF range |
|---|---|---|---|
| CAT_ITEM | ENT-CAT-001 | master | DBF-CAT-001 … DBF-CAT-021 |
| CAT_VARIANT | ENT-CAT-002 | master | DBF-CAT-022 … DBF-CAT-034 |
| CAT_VAR_ATTR | ENT-CAT-003 | lookup | DBF-CAT-035 … DBF-CAT-046 |
| CAT_VAR_ATTR_VALUE | ENT-CAT-004 | lookup | DBF-CAT-047 … DBF-CAT-059 |
| CAT_VAR_ATTR_ASSIGN | ENT-CAT-005 | master (dependent) | DBF-CAT-060 … DBF-CAT-070 |
| CAT_CATEGORY | ENT-CAT-006 | master | DBF-CAT-071 … DBF-CAT-083 |
| CAT_BUNDLE_COMP | ENT-CAT-007 | master (dependent) | DBF-CAT-084 … DBF-CAT-095 |
| CAT_ITEM_UNIT | ENT-CAT-008 | master (dependent) | DBF-CAT-096 … DBF-CAT-108 |
| CAT_BARCODE | ENT-CAT-009 | master (dependent) | DBF-CAT-109 … DBF-CAT-120 |
| CAT_ITEM_MEDIA | ENT-CAT-010 | master (dependent) | DBF-CAT-121 … DBF-CAT-135 |
| CAT_SCALE_LABEL_LAYOUT | ENT-CAT-011 | config | DBF-CAT-136 … DBF-CAT-149 |
| CAT_UNIT_EINVOICE_CODE | ENT-CAT-012 | config | DBF-CAT-150 … DBF-CAT-161 |

Tenant: every table carries TENANT_ID with `FK_<TABLE>_TENANT → CORE_TENANT(ID)` (12 constraints, db-script BLOCK 5e; erp-core checklist C1) and an index led by TENANT_ID. The FK targets a given table, so it is a permitted given reference, not an XM (ADR-CAT-055, superseding ADR-CAT-054). The factory's explicit exemption of this FK from `gov.py` C6.15 is OPEN platform finding 1 in business-policies-cat.md. Every intra-module FK is composite with TENANT_ID and targets a `(TENANT_ID, <PK>)` UNIQUE of its parent, so same-tenant integrity is structural (D-P2-12, ADR-CAT-067).

Given tables read, never created: MDL_LOOKUP_TYPE and MDL_LOOKUP_VALUE (`erp-core:modules.mdl`, seeded only), CORE_NUMBER_SERIES (`erp-core:modules.sequence`, seeded only), FILE_DOCUMENT (`erp-core:modules.file`, soft read through `CAT_ITEM_MEDIA.FILE_DOCUMENT_REF`) and CORE_TENANT (`erp-core:modules.tenant`, through `TENANT_ID` and its FK). New tenants receive the CAT lookups and number series from the given at provisioning, and the Unit e-invoice codes from `CatTenantProvisioningContributor` (ADR-CAT-058).

### XM index
```yaml name=xm-register
records: []
```
None. SRS A8 `consumes: []`. CAT is first in the fixed module order and has no outbound cross-module edge.

### Lookups
| Key | Seeded values | Owner of the key | Table owner |
|---|---|---|---|
| CAT_ITEM_KIND | 2 | CAT | erp-core mdl (MDL_LOOKUP_TYPE / MDL_LOOKUP_VALUE) |
| CAT_PUBLISH_STATE | 2 | CAT | erp-core mdl |
| CAT_BRAND | 0 (open, tenant-maintained) | CAT | erp-core mdl |
| CAT_UOM | 8 | CAT | erp-core mdl |
| CAT_TAX_CLASS | 3 | CAT | erp-core mdl |
| CAT_EINVOICE_ITEM_CODE_TYPE | 2 | CAT | erp-core mdl |
| CAT_EINVOICE_UNIT | 8 | CAT | erp-core mdl |
| CAT_BARCODE_TYPE | 3 | CAT | erp-core mdl |
| CAT_SCALE_VALUE_TYPE | 2 | CAT | erp-core mdl |
| CAT_CHANGE_KIND | 4 | CAT | erp-core mdl |

Total: 10 keys, 34 seeded values.

Other seeds:
- Number series CAT_ITEM, CAT_VARIANT and CAT_INTERNAL_BARCODE go into CORE_NUMBER_SERIES (owner: erp-core sequence).
- 8 Unit e-invoice codes go into CAT_UNIT_EINVOICE_CODE (owner: CAT).
- The columns of the given seed tables were checked against the erp-core 1.6.0 migrations (V3, V8, V10, V14 — D-P2-13). BLOCK 8 ends without a COMMIT; Flyway owns the transaction. Deployment precondition: the Flyway role may CREATE on the database for `pg_trgm`, or a DBA installs it beforehand.

### Sequences
- Last DBF: DBF-CAT-161 (next free sequence 162)
- Last XM: none (next free sequence 001)
- No database sequence is created (every PK uses the identity clause)
- One function (`CAT_NORMALIZE_AR`) and the `pg_trgm` extension are created for RULE-CAT-003 and search (ADR-CAT-062)
- Migration file: `V1000__cat_catalog.sql`

### Decisions
DEFAULTs D-P2-01 … D-P2-18 (db-script §5). ADR-CAT-055 (tenant FK restored; supersedes ADR-CAT-054), ADR-CAT-058 (tenant provisioning of seeds), ADR-CAT-062 (CAT_NORMALIZE_AR, pg_trgm indexes), ADR-CAT-067 (tenant-composite intra-module FKs), ADR-CAT-072 (the default Variant moves; UQ_CAT_VARIANT_DEFAULT kept non-partial, comments only). None BLOCKED. The next free ADR sequence, per the current registries (re-stamped at the eighth gate-analysis revise), is 077. Inputs: srs REQ 147 · AC 164 · ENT 12 · RULE 53.

### Event
"P2 completed: CAT v2 — 12 tables, 161 DBF, 0 XM"

### Cascade
None by hand. `gov.py graph` derives the edges that target CAT and raises their resolution events (shared/XM-PROTOCOL.md §5). No consumer edge exists yet.


<<<END INPUT>>>

<<<INPUT: change-manifest>>>
# CHANGE MANIFEST — CAT v2 (baseline v1)

```yaml name=change-manifest
change_set: CS-CAT-001
type: ADDITIVE
summary: 'given erp-core upgraded 1.5.0 → 1.6.0: re-run from `P0` (the first stage
  that matches candidates against the given) through `P1`, `P1.5`, `P2`, `P0` (the
  stages the C-GIVEN-1, C-GIVEN-2, C-GIVEN-3 clauses read); `domain-profile` is platform-level
  — re-run it by hand when the given''s module set changed'
artifacts:
  project-registry:
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  platform-summary:          # owns no IDs; written in full (no prior platform-summary in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  module-registry:           # file module-registry-cat.md; owns no IDs (indexes the POL-CAT ids of business-policies); written in full (no prior module registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  business-policies:         # file business-policies-cat.md
    ADDED: [POL-CAT-001, POL-CAT-002, POL-CAT-003, POL-CAT-004, POL-CAT-005, POL-CAT-006, POL-CAT-007, POL-CAT-008, POL-CAT-009]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  prd:                       # file P0_5/prd-cat.md; written in full (no prior PRD in the current state); US-CAT stream starts at 001, next free 023
    ADDED: [US-CAT-001, US-CAT-002, US-CAT-003, US-CAT-004, US-CAT-005, US-CAT-006, US-CAT-007, US-CAT-008, US-CAT-009, US-CAT-010, US-CAT-011, US-CAT-012, US-CAT-013, US-CAT-014, US-CAT-015, US-CAT-016, US-CAT-017, US-CAT-018, US-CAT-019, US-CAT-020, US-CAT-021, US-CAT-022]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  srs:                       # file P1/srs-cat.md; written in full (no prior SRS in the current state); streams start at 001 — next free REQ 128, AC 130, ENT 013, RULE 050, SCR-REQ 007
    ADDED: [REQ-CAT-001, REQ-CAT-002, REQ-CAT-003, REQ-CAT-004, REQ-CAT-005, REQ-CAT-006, REQ-CAT-007, REQ-CAT-008, REQ-CAT-009, REQ-CAT-010, REQ-CAT-011, REQ-CAT-012, REQ-CAT-013, REQ-CAT-014, REQ-CAT-015, REQ-CAT-016, REQ-CAT-017, REQ-CAT-018, REQ-CAT-019, REQ-CAT-020, REQ-CAT-021, REQ-CAT-022, REQ-CAT-023, REQ-CAT-024, REQ-CAT-025, REQ-CAT-026, REQ-CAT-027, REQ-CAT-028, REQ-CAT-029, REQ-CAT-030, REQ-CAT-031, REQ-CAT-032, REQ-CAT-033, REQ-CAT-034, REQ-CAT-035, REQ-CAT-036, REQ-CAT-037, REQ-CAT-038, REQ-CAT-039, REQ-CAT-040, REQ-CAT-041, REQ-CAT-042, REQ-CAT-043, REQ-CAT-044, REQ-CAT-045, REQ-CAT-046, REQ-CAT-047, REQ-CAT-048, REQ-CAT-049, REQ-CAT-050, REQ-CAT-051, REQ-CAT-052, REQ-CAT-053, REQ-CAT-054, REQ-CAT-055, REQ-CAT-056, REQ-CAT-057, REQ-CAT-058, REQ-CAT-059, REQ-CAT-060, REQ-CAT-061, REQ-CAT-062, REQ-CAT-063, REQ-CAT-064, REQ-CAT-065, REQ-CAT-066, REQ-CAT-067, REQ-CAT-068, REQ-CAT-069, REQ-CAT-070, REQ-CAT-071, REQ-CAT-072, REQ-CAT-073, REQ-CAT-074, REQ-CAT-075, REQ-CAT-076, REQ-CAT-077, REQ-CAT-078, REQ-CAT-079, REQ-CAT-080, REQ-CAT-081, REQ-CAT-082, REQ-CAT-083, REQ-CAT-084, REQ-CAT-085, REQ-CAT-086, REQ-CAT-087, REQ-CAT-088, REQ-CAT-089, REQ-CAT-090, REQ-CAT-091, REQ-CAT-092, REQ-CAT-093, REQ-CAT-094, REQ-CAT-095, REQ-CAT-096, REQ-CAT-097, REQ-CAT-098, REQ-CAT-099, REQ-CAT-100, REQ-CAT-101, REQ-CAT-102, REQ-CAT-103, REQ-CAT-104, REQ-CAT-105, REQ-CAT-106, REQ-CAT-107, REQ-CAT-108, REQ-CAT-109, REQ-CAT-110, REQ-CAT-111, REQ-CAT-112, REQ-CAT-113, REQ-CAT-114, REQ-CAT-115, REQ-CAT-116, REQ-CAT-117, REQ-CAT-118, REQ-CAT-119, REQ-CAT-120, REQ-CAT-121, REQ-CAT-122, REQ-CAT-123, REQ-CAT-124, REQ-CAT-125, REQ-CAT-126, REQ-CAT-127,
      AC-CAT-001, AC-CAT-002, AC-CAT-003, AC-CAT-004, AC-CAT-005, AC-CAT-006, AC-CAT-007, AC-CAT-008, AC-CAT-009, AC-CAT-010, AC-CAT-011, AC-CAT-012, AC-CAT-013, AC-CAT-014, AC-CAT-015, AC-CAT-016, AC-CAT-017, AC-CAT-018, AC-CAT-019, AC-CAT-020, AC-CAT-021, AC-CAT-022, AC-CAT-023, AC-CAT-024, AC-CAT-025, AC-CAT-026, AC-CAT-027, AC-CAT-028, AC-CAT-029, AC-CAT-030, AC-CAT-031, AC-CAT-032, AC-CAT-033, AC-CAT-034, AC-CAT-035, AC-CAT-036, AC-CAT-037, AC-CAT-038, AC-CAT-039, AC-CAT-040, AC-CAT-041, AC-CAT-042, AC-CAT-043, AC-CAT-044, AC-CAT-045, AC-CAT-046, AC-CAT-047, AC-CAT-048, AC-CAT-049, AC-CAT-050, AC-CAT-051, AC-CAT-052, AC-CAT-053, AC-CAT-054, AC-CAT-055, AC-CAT-056, AC-CAT-057, AC-CAT-058, AC-CAT-059, AC-CAT-060, AC-CAT-061, AC-CAT-062, AC-CAT-063, AC-CAT-064, AC-CAT-065, AC-CAT-066, AC-CAT-067, AC-CAT-068, AC-CAT-069, AC-CAT-070, AC-CAT-071, AC-CAT-072, AC-CAT-073, AC-CAT-074, AC-CAT-075, AC-CAT-076, AC-CAT-077, AC-CAT-078, AC-CAT-079, AC-CAT-080, AC-CAT-081, AC-CAT-082, AC-CAT-083, AC-CAT-084, AC-CAT-085, AC-CAT-086, AC-CAT-087, AC-CAT-088, AC-CAT-089, AC-CAT-090, AC-CAT-091, AC-CAT-092, AC-CAT-093, AC-CAT-094, AC-CAT-095, AC-CAT-096, AC-CAT-097, AC-CAT-098, AC-CAT-099, AC-CAT-100, AC-CAT-101, AC-CAT-102, AC-CAT-103, AC-CAT-104, AC-CAT-105, AC-CAT-106, AC-CAT-107, AC-CAT-108, AC-CAT-109, AC-CAT-110, AC-CAT-111, AC-CAT-112, AC-CAT-113, AC-CAT-114, AC-CAT-115, AC-CAT-116, AC-CAT-117, AC-CAT-118, AC-CAT-119, AC-CAT-120, AC-CAT-121, AC-CAT-122, AC-CAT-123, AC-CAT-124, AC-CAT-125, AC-CAT-126, AC-CAT-127, AC-CAT-128, AC-CAT-129,
      ENT-CAT-001, ENT-CAT-002, ENT-CAT-003, ENT-CAT-004, ENT-CAT-005, ENT-CAT-006, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010, ENT-CAT-011, ENT-CAT-012,
      RULE-CAT-001, RULE-CAT-002, RULE-CAT-003, RULE-CAT-004, RULE-CAT-005, RULE-CAT-006, RULE-CAT-007, RULE-CAT-008, RULE-CAT-009, RULE-CAT-010, RULE-CAT-011, RULE-CAT-012, RULE-CAT-013, RULE-CAT-014, RULE-CAT-015, RULE-CAT-016, RULE-CAT-017, RULE-CAT-018, RULE-CAT-019, RULE-CAT-020, RULE-CAT-021, RULE-CAT-022, RULE-CAT-023, RULE-CAT-024, RULE-CAT-025, RULE-CAT-026, RULE-CAT-027, RULE-CAT-028, RULE-CAT-029, RULE-CAT-030, RULE-CAT-031, RULE-CAT-032, RULE-CAT-033, RULE-CAT-034, RULE-CAT-035, RULE-CAT-036, RULE-CAT-037, RULE-CAT-038, RULE-CAT-039, RULE-CAT-040, RULE-CAT-041, RULE-CAT-042, RULE-CAT-043, RULE-CAT-044, RULE-CAT-045, RULE-CAT-046, RULE-CAT-047, RULE-CAT-048, RULE-CAT-049,
      SCR-REQ-CAT-001, SCR-REQ-CAT-002, SCR-REQ-CAT-003, SCR-REQ-CAT-004, SCR-REQ-CAT-005, SCR-REQ-CAT-006]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  registry-srs:              # file P1/registry-srs-cat.md; owns no IDs (indexes the srs ids); written in full (no prior SRS registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  contract:                  # file P1_5/contract-cat.md; written in full (no prior contract in the current state); CON stream starts at 001, next free 019
    ADDED: [CON-CAT-001, CON-CAT-002, CON-CAT-003, CON-CAT-004, CON-CAT-005, CON-CAT-006, CON-CAT-007, CON-CAT-008, CON-CAT-009, CON-CAT-010, CON-CAT-011, CON-CAT-012, CON-CAT-013, CON-CAT-014, CON-CAT-015, CON-CAT-016, CON-CAT-017, CON-CAT-018]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  db-script:                 # file P2/db-script-cat.md; written in full as a create-all migration (no prior db-script in the current state); DBF stream starts at 001, next free 162; no XM (consumes: []), XM next free 001
    ADDED: [DBF-CAT-001, DBF-CAT-002, DBF-CAT-003, DBF-CAT-004, DBF-CAT-005, DBF-CAT-006, DBF-CAT-007, DBF-CAT-008, DBF-CAT-009, DBF-CAT-010,
      DBF-CAT-011, DBF-CAT-012, DBF-CAT-013, DBF-CAT-014, DBF-CAT-015, DBF-CAT-016, DBF-CAT-017, DBF-CAT-018, DBF-CAT-019, DBF-CAT-020,
      DBF-CAT-021, DBF-CAT-022, DBF-CAT-023, DBF-CAT-024, DBF-CAT-025, DBF-CAT-026, DBF-CAT-027, DBF-CAT-028, DBF-CAT-029, DBF-CAT-030,
      DBF-CAT-031, DBF-CAT-032, DBF-CAT-033, DBF-CAT-034, DBF-CAT-035, DBF-CAT-036, DBF-CAT-037, DBF-CAT-038, DBF-CAT-039, DBF-CAT-040,
      DBF-CAT-041, DBF-CAT-042, DBF-CAT-043, DBF-CAT-044, DBF-CAT-045, DBF-CAT-046, DBF-CAT-047, DBF-CAT-048, DBF-CAT-049, DBF-CAT-050,
      DBF-CAT-051, DBF-CAT-052, DBF-CAT-053, DBF-CAT-054, DBF-CAT-055, DBF-CAT-056, DBF-CAT-057, DBF-CAT-058, DBF-CAT-059, DBF-CAT-060,
      DBF-CAT-061, DBF-CAT-062, DBF-CAT-063, DBF-CAT-064, DBF-CAT-065, DBF-CAT-066, DBF-CAT-067, DBF-CAT-068, DBF-CAT-069, DBF-CAT-070,
      DBF-CAT-071, DBF-CAT-072, DBF-CAT-073, DBF-CAT-074, DBF-CAT-075, DBF-CAT-076, DBF-CAT-077, DBF-CAT-078, DBF-CAT-079, DBF-CAT-080,
      DBF-CAT-081, DBF-CAT-082, DBF-CAT-083, DBF-CAT-084, DBF-CAT-085, DBF-CAT-086, DBF-CAT-087, DBF-CAT-088, DBF-CAT-089, DBF-CAT-090,
      DBF-CAT-091, DBF-CAT-092, DBF-CAT-093, DBF-CAT-094, DBF-CAT-095, DBF-CAT-096, DBF-CAT-097, DBF-CAT-098, DBF-CAT-099, DBF-CAT-100,
      DBF-CAT-101, DBF-CAT-102, DBF-CAT-103, DBF-CAT-104, DBF-CAT-105, DBF-CAT-106, DBF-CAT-107, DBF-CAT-108, DBF-CAT-109, DBF-CAT-110,
      DBF-CAT-111, DBF-CAT-112, DBF-CAT-113, DBF-CAT-114, DBF-CAT-115, DBF-CAT-116, DBF-CAT-117, DBF-CAT-118, DBF-CAT-119, DBF-CAT-120,
      DBF-CAT-121, DBF-CAT-122, DBF-CAT-123, DBF-CAT-124, DBF-CAT-125, DBF-CAT-126, DBF-CAT-127, DBF-CAT-128, DBF-CAT-129, DBF-CAT-130,
      DBF-CAT-131, DBF-CAT-132, DBF-CAT-133, DBF-CAT-134, DBF-CAT-135, DBF-CAT-136, DBF-CAT-137, DBF-CAT-138, DBF-CAT-139, DBF-CAT-140,
      DBF-CAT-141, DBF-CAT-142, DBF-CAT-143, DBF-CAT-144, DBF-CAT-145, DBF-CAT-146, DBF-CAT-147, DBF-CAT-148, DBF-CAT-149, DBF-CAT-150,
      DBF-CAT-151, DBF-CAT-152, DBF-CAT-153, DBF-CAT-154, DBF-CAT-155, DBF-CAT-156, DBF-CAT-157, DBF-CAT-158, DBF-CAT-159, DBF-CAT-160,
      DBF-CAT-161]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  registry-db:               # file P2/registry-db-cat.md; owns no IDs (indexes the db-script DBF ranges and the empty XM register); written in full (no prior DB registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  backend-execution-plan:    # file P3_1/backend-execution-plan-cat.md; written in full (no prior backend plan in the current state); API and QR streams start at 001 — next free API 052, QR 071; no XM placed or minted (register empty)
    ADDED: [API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010,
      API-CAT-011, API-CAT-012, API-CAT-013, API-CAT-014, API-CAT-015, API-CAT-016, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020,
      API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030,
      API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040,
      API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-047, API-CAT-048, API-CAT-049, API-CAT-050,
      API-CAT-051,
      QR-CAT-001, QR-CAT-002, QR-CAT-003, QR-CAT-004, QR-CAT-005, QR-CAT-006, QR-CAT-007, QR-CAT-008, QR-CAT-009, QR-CAT-010,
      QR-CAT-011, QR-CAT-012, QR-CAT-013, QR-CAT-014, QR-CAT-015, QR-CAT-016, QR-CAT-017, QR-CAT-018, QR-CAT-019, QR-CAT-020,
      QR-CAT-021, QR-CAT-022, QR-CAT-023, QR-CAT-024, QR-CAT-025, QR-CAT-026, QR-CAT-027, QR-CAT-028, QR-CAT-029, QR-CAT-030,
      QR-CAT-031, QR-CAT-032, QR-CAT-033, QR-CAT-034, QR-CAT-035, QR-CAT-036, QR-CAT-037, QR-CAT-038, QR-CAT-039, QR-CAT-040,
      QR-CAT-041, QR-CAT-042, QR-CAT-043, QR-CAT-044, QR-CAT-045, QR-CAT-046, QR-CAT-047, QR-CAT-048, QR-CAT-049, QR-CAT-050,
      QR-CAT-051, QR-CAT-052, QR-CAT-053, QR-CAT-054, QR-CAT-055, QR-CAT-056, QR-CAT-057, QR-CAT-058, QR-CAT-059, QR-CAT-060,
      QR-CAT-061, QR-CAT-062, QR-CAT-063, QR-CAT-064, QR-CAT-065, QR-CAT-066, QR-CAT-067, QR-CAT-068, QR-CAT-069, QR-CAT-070]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  registry-exec-be:          # file P3_1/registry-exec-be-cat.md; owns no IDs (indexes the API / QR ranges of the backend plan); written in full (no prior registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  api-spec:                  # file P3_1/api-spec-cat.yaml; owns no IDs (generated from the backend plan — fold: replace, re-emitted whole); 51 operations = 51 API blocks
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  flow-diagram:              # file P3_2/flow-diagram-cat.md; owns no IDs (flows carry no atom); written in full (no prior flow diagram in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  ui-ux-spec:                # file P3_2/ui-ux-spec-cat.md; written in full (no prior ui-ux-spec in the current state); SCR and UXD streams start at 001 — next free SCR 007, UXD 004
    ADDED: [SCR-CAT-001, SCR-CAT-002, SCR-CAT-003, SCR-CAT-004, SCR-CAT-005, SCR-CAT-006,
      UXD-CAT-001, UXD-CAT-002, UXD-CAT-003]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  frontend-execution-plan:   # file P3_2/frontend-execution-plan-cat.md; owns no IDs (cites SCR/UXD of the spec and API of api-spec-cat.yaml); written in full (no prior frontend plan in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  registry-exec-fe:          # file P3_2/registry-exec-fe-cat.md; owns no IDs (indexes the SCR / UXD ranges); written in full (no prior registry in the current state)
    ADDED: []
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  backend-test-plan:         # file P4/backend-test-plan-cat.md; written in full (no prior test plan in the current state); TC stream starts at 001 (shared with the frontend test plan) — 124 TCs, no INT-XM (no XM)
    ADDED: [TC-CAT-001, TC-CAT-002, TC-CAT-003, TC-CAT-004, TC-CAT-005, TC-CAT-006, TC-CAT-007, TC-CAT-008, TC-CAT-009, TC-CAT-010,
      TC-CAT-011, TC-CAT-012, TC-CAT-013, TC-CAT-014, TC-CAT-015, TC-CAT-016, TC-CAT-017, TC-CAT-018, TC-CAT-019, TC-CAT-020,
      TC-CAT-021, TC-CAT-022, TC-CAT-023, TC-CAT-024, TC-CAT-025, TC-CAT-026, TC-CAT-027, TC-CAT-028, TC-CAT-029, TC-CAT-030,
      TC-CAT-031, TC-CAT-032, TC-CAT-033, TC-CAT-034, TC-CAT-035, TC-CAT-036, TC-CAT-037, TC-CAT-038, TC-CAT-039, TC-CAT-040,
      TC-CAT-041, TC-CAT-042, TC-CAT-043, TC-CAT-044, TC-CAT-045, TC-CAT-046, TC-CAT-047, TC-CAT-048, TC-CAT-049, TC-CAT-050,
      TC-CAT-051, TC-CAT-052, TC-CAT-053, TC-CAT-054, TC-CAT-055, TC-CAT-056, TC-CAT-057, TC-CAT-058, TC-CAT-059, TC-CAT-060,
      TC-CAT-061, TC-CAT-062, TC-CAT-063, TC-CAT-064, TC-CAT-065, TC-CAT-066, TC-CAT-067, TC-CAT-068, TC-CAT-069, TC-CAT-070,
      TC-CAT-071, TC-CAT-072, TC-CAT-073, TC-CAT-074, TC-CAT-075, TC-CAT-076, TC-CAT-077, TC-CAT-078, TC-CAT-079, TC-CAT-080,
      TC-CAT-081, TC-CAT-082, TC-CAT-083, TC-CAT-084, TC-CAT-085, TC-CAT-086, TC-CAT-087, TC-CAT-088, TC-CAT-089, TC-CAT-090,
      TC-CAT-091, TC-CAT-092, TC-CAT-093, TC-CAT-094, TC-CAT-095, TC-CAT-096, TC-CAT-097, TC-CAT-098, TC-CAT-099, TC-CAT-100,
      TC-CAT-101, TC-CAT-102, TC-CAT-103, TC-CAT-104, TC-CAT-105, TC-CAT-106, TC-CAT-107, TC-CAT-108, TC-CAT-109, TC-CAT-110,
      TC-CAT-111, TC-CAT-112, TC-CAT-113, TC-CAT-114, TC-CAT-115, TC-CAT-116, TC-CAT-117, TC-CAT-118, TC-CAT-119, TC-CAT-120,
      TC-CAT-121, TC-CAT-122, TC-CAT-123, TC-CAT-124]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
  frontend-test-plan:        # file P4/frontend-test-plan-cat.md; written in full (no prior test plan in the current state); continues the shared TC stream — 58 TCs (52 module, 6 INT-UXD); next free TC 183
    ADDED: [TC-CAT-125, TC-CAT-126, TC-CAT-127, TC-CAT-128, TC-CAT-129, TC-CAT-130,
      TC-CAT-131, TC-CAT-132, TC-CAT-133, TC-CAT-134, TC-CAT-135, TC-CAT-136, TC-CAT-137, TC-CAT-138, TC-CAT-139, TC-CAT-140,
      TC-CAT-141, TC-CAT-142, TC-CAT-143, TC-CAT-144, TC-CAT-145, TC-CAT-146, TC-CAT-147, TC-CAT-148, TC-CAT-149, TC-CAT-150,
      TC-CAT-151, TC-CAT-152, TC-CAT-153, TC-CAT-154, TC-CAT-155, TC-CAT-156, TC-CAT-157, TC-CAT-158, TC-CAT-159, TC-CAT-160,
      TC-CAT-161, TC-CAT-162, TC-CAT-163, TC-CAT-164, TC-CAT-165, TC-CAT-166, TC-CAT-167, TC-CAT-168, TC-CAT-169, TC-CAT-170,
      TC-CAT-171, TC-CAT-172, TC-CAT-173, TC-CAT-174, TC-CAT-175, TC-CAT-176, TC-CAT-177, TC-CAT-178, TC-CAT-179, TC-CAT-180,
      TC-CAT-181, TC-CAT-182]
    MODIFIED: []
    REMOVED: []
    UNCHANGED: []
```

P0 delta note: the given's module set changed (1.6.0 adds `gl`), so the domain-profile hand re-run named in the summary applies (OI-02).

P0.5 delta note: 22 user stories were added, covering 9 of 9 POL-CAT policies. No story is deferred. The erp-core 1.6.0 change (`gl`) does not touch the catalogue boundary, so no story depends on the given upgrade.

P1 delta note: the SRS is written in full, because the current state holds no earlier SRS. It adds REQ 127, AC 129, ENT 12, RULE 49 and SCR-REQ 6, covering 22 of 22 stories. It records ADR-CAT-038 … ADR-CAT-043 (all ACCEPTED, none BLOCKED). The `module-dependencies` block is `consumes: []`. The given uses go through erp-core mdl, cu, sequence, file, events, report and sec; no use goes through the new `gl` module.

P1.5 delta note: the contract is written in full, because the current state holds no earlier contract. It adds CON 18:
- 6 entity items, one for each SHARED ENT (ENT-CAT-001, 002, 006, 007, 008, 009), each with a BIGINT identifier.
- 11 operation items (signatures only).
- 1 catalogue-change event item.

Nothing is MODIFIED or REMOVED, so no consumer edge is affected. The contract repeats the SRS `given-usage` rows verbatim. No question was raised and no ADR was needed; the next free ADR is ADR-CAT-044.

P2 delta note: the db-script is written as a create-all migration, because the current state holds no earlier db-script. It adds 12 tables (one per ENT) and DBF 161 (001 … 161):
- every PK uses the identity clause, as a BIGINT, which matches the contract's identifier promise;
- 13 intra-module FKs;
- every UNIQUE is composite with TENANT_ID.

The XM register is empty (`consumes: []`), so `gov.py graph` gets no edge from CAT. The given tables (mdl lookup tables, CORE_NUMBER_SERIES, FILE_DOCUMENT, CORE_TENANT) are never re-created. The 10 CAT lookup keys (34 values), the 3 number series and the 8 Unit e-invoice codes are seeded for the PLATFORM tenant. Decisions are recorded as DEFAULTs D-P2-01 … D-P2-18. No question was raised and no ADR was needed; the next free ADR is unchanged.

P3.1 delta note: the backend execution plan is written in full, because the current state holds no earlier plan. It adds API 51 (001 … 051) and QR 70 (001 … 070) in the profile's seven phases; INT-C, the last phase, carries no block because the XM register is empty and no edge was minted. Every CON-CAT item (001 … 018) is honoured. The Error Catalog has 57 codes (49 RULE rows, 8 PLATFORM-STD). The API document `api-spec-cat.yaml` (OpenAPI 3.1.0) is derived from the plan with one operation per API block. ADR-CAT-044 … ADR-CAT-048 are recorded (all ACCEPTED, non-breaking, none BLOCKED); the next free ADR is ADR-CAT-049. No question was raised.

P3.2 delta note: the frontend design and the frontend execution plan are written in full, because the current state holds no earlier frontend artifact. The ui-ux-spec adds SCR 6 (001 … 006, one per SCR-REQ) and UXD 3 (001 … 003). The three UXDs are lookup values, capability flags and image files; all three are served by the given erp-core, and none of them is an XM. The flow diagram holds 18 flows covering 22 of 22 stories. The plan binds to `api-spec-cat.yaml` (the mock): 126 REQ → API bindings, 49 runtime codes → RULE, and 49 of 51 operations used. In the profile's six frontend phases it has 24 per-screen SUBs and one `screen-hooks` block per screen. ADR-CAT-049 … ADR-CAT-051 are recorded (all ACCEPTED, non-breaking, none BLOCKED); the next free ADR is ADR-CAT-052. No question was raised.

P4 delta note: both test plans are written in full, because the current state holds no earlier test plan. The TC stream starts at 001 and is shared by the two plans: TC-CAT-001 … 124 are backend and TC-CAT-125 … 182 are frontend.
- Coverage over the union of the two plans is AC 129 / 129, REQ 127 / 127, API 51 / 51, RULE 49 / 49, SCR 6 / 6 and UXD 3 / 3.
- The backend `INT-XM` phase is absent, because there is no XM. The frontend `INT-UXD` phase holds 6 TCs, for the three UXDs owned by the given erp-core.
- ADR-CAT-052 is recorded (ACCEPTED, non-breaking). It covers the track split, the package binding, the host-data creation surface, catalogue isolation, the fields `api-spec-cat.yaml` does not expose (next-action code, listed Variants, Variant `updatedAt`) and fault injection.
- Round 2 (after `gov.py analyze` refused C10.7 and C10.8): ADR-CAT-053 is recorded (ACCEPTED, non-breaking). Eight frontend TCs (TC-CAT-130, 143, 144, 145, 150, 157, 165, 171) now assert the routing and unchanged display of codes whose words the frontend plan does not bind; their wording stays asserted on the backend track. The unseeded AC values CAT_UOM `BAG` and CAT_EINVOICE_UNIT `H87`, for which no named add-value call exists, are replaced by seeded values in TC-CAT-063, 120, 121, 122 and 175 (TC-CAT-138 drops its `BAG` mention). Every TC id keeps its number and stays ADDED in this version. Coverage is unchanged.
- The next free ADR is ADR-CAT-054. No question was raised.

<<<END INPUT>>>

---
# KNOWLEDGE (profile primary sources — cite as [KB:<file> §n])

<<<KB: profiles/commerce/knowledge/raw-idea.md>>>
# Platform Brief — Retail & Wholesale Commerce SaaS on erp-core

**Status:** approved vision, input to `domain-profile` · **Owner:** Hesham Ezzat · **Date:** 2026-10-21
**Given:** `erp-core` (multi-tenant core, deployment mode `single|multi`, business modules tenant-agnostic) — consumed via `gov.py given add erp-core --version <current>`; nothing the core provides is rebuilt.
**Technical base (approved):** Spring Boot (Java 25) backend on `erp-core` · React frontend, one build for all deployments reading `GET /api/public/app-config` (tenancy mode, feature flags, activity preset) · PostgreSQL 16 (Flyway, app migrations `V1000+`). Jasper for printing via the core `PrintApi`.

---

## 1. One-paragraph vision

A SaaS platform for small and medium **retail and wholesale traders** in Egypt and the Gulf, covering back-office (purchasing, inventory, sales, receivables/payables, accounting, tax & e-invoicing), point of sale, and an **integrated e-commerce storefront** — one catalogue, one stock, one customer base. It serves many trading activities (electronics & appliances, apparel, household goods, cosmetics, grocery, spare parts, building materials, …) from **one codebase** through six activity capability flags and per-activity presets. It is **feature-complete but simple to use**: every capability exists, nothing is forced on the user; power lives in the backend, the UI shows only what is enabled.

## 2. Market and positioning (facts gathered 2026-09)

- Wholesale & retail trade is the largest activity by number of establishments: **54.4%** of Egypt's 3.9M establishments (6th Economic Census 2022/23) and **30.7%** of Saudi SMEs (Monsha'at SME Monitor).
- Demand is regulation-driven: **mandatory e-invoicing** (Saudi ZATCA phase-2 waves; Egypt B2C e-receipt expansion from Sept 2025) forces every small trader to adopt compliant software.
- Competitors in the segment are all SaaS (Qoyod, Wafeq, Daftra, …); the differentiator is **one product for back-office + POS + storefront**, activity-aware, and simple.

## 3. Fixed decisions (P0 — not open questions)

| # | Decision |
|---|---|
| D1 | Activity scope: **retail & wholesale, multi-activity**, driven by six capability flags (§4). Pharmacies, restaurants, gold, vehicles, manufacturing, contracting, services are **out of scope** (different business models). |
| D2 | Business logic reads **capability flags only**, never the activity name. The activity is a **preset** that sets flags. Enforced by ArchUnit/lint. |
| D3 | Deployment: `erp.tenancy.mode=multi` (SaaS) is the default go-to-market; `single` available for dedicated/on-prem customers. Modules are tenant-agnostic (core rule). |
| D4 | Public storefront tenant resolution: **subdomain** (`<shop>.<platform>`) by default, **custom domain** optional; resolver feeds the core `TenantContext`; in `single` mode it returns the default tenant. Designed once in the Storefront API. |
| D5 | **Guest checkout allowed**; the guest becomes a CUSTOMER-realm account after the first order (core customer realm). |
| D6 | Approvals are a **lightweight business module** (`Approvals`): per document type → none / one step / role-based steps, configured on one screen. No BPM engine (core decision upheld). |
| D7 | Accounting is fully present (GL, cost centres, budgets) but invisible by default; auto-posting from documents; "accounting mode" reveals its screens. |
| D8 | Roles: three presets (Owner, Seller/Cashier, Warehouse) + custom roles via core permissions. |
| D9 | Simplicity is a measurable requirement (§6), not a feature cut. |
| D10 | Delivery is module-by-module in the fixed order of §5; a module is Done only with its reports, tests and conformance scenarios green. |

## 4. Capability flags and activity presets

### 4.1 The six flags (read through one `FeatureFlags` API)

| Flag | What it changes | Core seam used |
|---|---|---|
| `variants` | items have size/colour/… variants; catalogue, cart, stock at variant level | custom attributes |
| `serialTracking` (+ `warranty`) | serial captured on receipt/sale; warranty card; service returns | new business module `SerialWarranty` |
| `lotExpiry` | lot + expiry on receipt; FEFO issue; near-expiry reports | core `lot_id` + policy |
| `dualQuantity` | count + weight/length; pricing per either unit | core dual-quantity columns |
| `bundles` | composite items / kits / promo packs | — |
| `b2bCredit` | wholesale customers: credit limits, terms, price lists | Pricing + AR |

Each flag has tests in both states; the UI shows fields/menus/reports only when the flag is on.

### 4.2 Presets (✓ required · ○ optional)

| Activity | variants | serial | lotExpiry | dualQty | bundles | b2b |
|---|---|---|---|---|---|---|
| Electronics & appliances, mobiles, computers | ○ | ✓ | | | ✓ | ✓ |
| Apparel & footwear | ✓ | | | | | ✓ |
| Household, cosmetics, stationery, toys, accessories | ○ | | | | ✓ | ✓ |
| Grocery & food | | | ✓ | ○ | | ✓ |
| Cleaning supplies, feed | | | ✓ | | | ✓ |
| Auto spare parts | | ○ | | | | ✓ |
| Power tools & hardware | | ✓ | | | ✓ | ✓ |
| Furniture & lighting | ✓ | | | | ✓ | |
| Sanitary & plumbing | ✓ | | | | | ✓ |
| Fabrics & furnishings | ✓ | | | ✓ (metre) | | ✓ |
| Building materials & steel | | | | ✓ | | ✓ |
| Produce / meat by weight | | | ✓ | ✓ | | |

Launch priority: electronics & appliances → apparel → household/cosmetics (zero or one extension each); grocery next (lot/expiry + delivery).

## 5. Module map (business modules the factory generates) — fixed DAG

| # | Module | Depends on | Core given used |
|---|---|---|---|
| 1 | **Catalog** — item, variants, media, categories, publish state | — | files (public), lookups, settings |
| 2 | **Pricing** — price lists, promotions, coupons, loyalty points | Catalog | settings |
| 3 | **Inventory** — stock, reservations, transfers, counts, lot/expiry, dual qty | Catalog | sequences, events, audit |
| 4 | **Party** — customers (incl. CUSTOMER-realm users), suppliers, addresses, credit | — | auth (realms), settings |
| 5 | **Purchasing** — PO, goods receipt, supplier invoice | 1,3,4 | sequences, print |
| 6 | **Sales & POS** — quotation, order, invoice, returns/exchange; POS mode (offline-capable) | 1,2,3,4 | sequences, print, events |
| 7 | **Settlements** — collections, payments, AR/AP ageing, payment gateway adapter | 4,5,6 | cash&bank seams, audit |
| 8 | **Tax & E-invoicing** — VAT, country adapters (ETA/ZATCA/Oman), tax returns | 5,6 | tax adapter |
| 9 | **Accounting** — auto-posting rules, GL, cost centres, budgets, period close | 5,6,7,8 | report/print |
| 10 | **SerialWarranty** — serials, warranty cards, service returns (flag `serialTracking`) | 3,6 | — |
| 11 | **Approvals** — per-document approval steps (D6) | any document module | events, notifications |
| 12 | **Shipping** — delivery status, carrier adapters | 6 | notifications |
| 13 | **Storefront** — public catalogue/search, guest cart, checkout, customer area; tenant-by-host resolver (D4) | 1,2,3,4,6,7,12 | auth realms, files public |
| 14 | **Insights** — dashboards, 15–20 standard reports, saved filters | all | report engine + Jasper |

Rules: dependencies flow downward only; cross-module calls only through declared `crossmodule` APIs; core only through manifest extension points (`C-GIVEN-1/2`).

## 6. Simplicity as a requirement ("everything available, nothing imposed")

1. **Progressive disclosure**: every screen starts minimal; "Advanced" reveals the rest; ≤ 4 mandatory fields per screen.
2. **Presets then customise**: choosing the activity fills all settings; later changes never require re-setup. Flags may be turned **on** at any time; turning off is blocked when data depends on it.
3. **Only enabled is shown**: menus, fields, reports, and storefront features bound to flags — no dead menus.
4. **Action in place**: return from the invoice, collect from the statement, receive from the PO.
5. **No dead ends**: every error offers a next action; undo for unposted operations.
6. **One global search** (items, customers, documents).
7. **Mobile-first** for selling, collecting, counting; responsive web, no separate app in v1.
8. **Defaults ("everything available, nothing imposed")**: one warehouse/branch/currency/price list to start; every such dimension exists in the data model from day one (e.g. every stock move carries `warehouse_id`); the UI hides the selector while the count is 1 and reveals it — plus the related actions (e.g. inter-warehouse transfer) and report filters — automatically when a second one is added. No data migration, no "mode" switch.
9. **Setup wizard (5 steps)**: company → activity preset → tax & e-invoice → payment methods → import items from Excel.
10. **Metric**: an owner with no technical background completes setup and issues the first invoice in **≤ 30 minutes** without training; a new employee sells after **≤ 10 minutes**.

## 7. Coverage and architecture guarantees (how we know it is sound)

| Risk | Guarantee | Enforced in |
|---|---|---|
| Activity "covered" on paper only | One **conformance scenario per preset** (setup → receive → sell → return → collect → report) runs in CI | `activity-conformance` test suite |
| Behaviour varies by activity ad hoc | Logic reads the six flags via `FeatureFlags` only; every flag tested in both states | ArchUnit + lint (`activity ==` or preset read outside settings = build failure) |
| Missing requirement for an activity | **Flag × activity matrix** is a P0 artifact; each cell traces to an EARS requirement ID (P1) and a `TC-*`; an untraced cell fails the consistency check | Factory traceability P0→P1→test-gen |
| Random inter-module design | Fixed DAG (§5); `crossmodule` contracts only; core via manifest extension points | ArchUnit boundaries + `C-GIVEN-1/2` + pass-review checklist (`erp-core-usage`) |
| Decisions drift during build | §3 decisions enter P0 as fixed + ADRs; no open questions after the PRD | One-gate analysis + adversarial reading |
| Uncontrolled growth | Fixed delivery order; a module is Done with reports, tests, conformance green | execution-state gates |

**Definition of "covers activity X":** preset exists + conformance scenario green + every matrix cell traced. Adding an activity later = one matrix column + one scenario.

## 8. Delivery order

1. Catalog, Party, Inventory, Purchasing, Sales & POS, Settlements, Tax & E-invoicing (sellable back-office + POS).
2. Storefront + Shipping (e-commerce live).
3. Accounting (visible mode), SerialWarranty, Approvals, Insights.
4. Advanced: multi-currency, cost centres/budgets, loyalty, carrier adapters, AI assistant (deferred core module).

## 9. Out of scope (v1)

Native mobile apps; marketplace (multi-vendor in one storefront); manufacturing/contracting/services modules; pharmacy/restaurant/gold/vehicle models; report designer; BPM workflow engine.

## 10. Next steps

1. Create project `commerce` from the governance-shared template; `gov.py given add erp-core --version <current>`.
2. Place this brief as the platform-brief input; run `domain-profile` → expect §4/§5 reproduced with core items marked `EXISTING (given)`.
3. P0 with §3 as fixed decisions and §4.2 as the matrix artifact → P1 (EARS + traceability) → … → packages → build in §8 order with the `erp-core-usage` skill.
<<<END KB>>>


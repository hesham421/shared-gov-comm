# REPAIR BRIEF — stage `P1` (SRS) · module INV · v1 · round 1

## What to do
You wrote this stage's files, and `gov.py analyze` refused them with the 7 finding(s) below.
Read the files at the absolute paths listed (an exception to the base brief's 'read only what this brief
contains'), fix EVERY finding by editing those files in place with your file tools, and change nothing else:
never re-number an id, never restart a sequence, never raise a `[QUESTION]`.
A finding that names an artifact this stage does not write is fixed from THIS stage's side (a test that asserts
a text the plan does not bind asserts the code and the control instead; a value nothing seeds is replaced by a
seeded one or created by the test's own precondition). Where a fix needs a choice, take the best-practice one and
record it as an ADR (`analysis/decisions/INV/ADR-{MOD}-{seq:03d}.md`, the next free id, status ACCEPTED).
End with a short list of what you changed. The files you may edit:
- `C:\repos\commerce\governance-shared\analysis\modules\INV\P1\srs-inv.md`
- `C:\repos\commerce\governance-shared\analysis\modules\INV\P1\registry-srs-inv.md`

## Findings to fix
- [CRITICAL] C5.2 (ears) :1002 — `REQ-INV-044` statement matches no EARS pattern: “While the tenant disallows negative stock, if a reservation request exceeds the ”
- [CRITICAL] C5.2 (ears) :1016 — `REQ-INV-045` statement matches no EARS pattern: “While the tenant allows negative stock, if a reservation request exceeds the ava”
- [CRITICAL] C5.2 (ears) :1335 — `REQ-INV-067` statement matches no EARS pattern: “Where the tenant disallows negative stock, if an online issue, transfer or stock”
- [CRITICAL] C5.2 (ears) :1575 — `REQ-INV-083` statement matches no EARS pattern: “Where the tenant disallows negative stock, if posting a transfer would take the ”
- [MAJOR] C5.8 (registry-agree) registry-srs — `AC-INV-155` registered in `registry-srs` but not defined in `srs`
- [MAJOR] C5.8 (registry-agree) registry-srs — `ENT-INV-015` registered in `registry-srs` but not defined in `srs`
- [MAJOR] C5.8 (registry-agree) registry-srs — `RULE-INV-049` registered in `registry-srs` but not defined in `srs`

---
# THE STAGE'S BRIEF (its rules still bind; its inputs are below)

# BRIEF — stage `P1` (SRS) · module INV · v1 · profile `commerce`

Lane `analysis` · implementer ['claude:opus'] · effort high · round 1

## Rules that bind this run
- Questions: **forbidden**. A `[QUESTION]` block is refused. Ambiguity → ADR in `analysis/decisions/INV/` (`ADR-{MOD}-{seq:03d}.md`): non-breaking → continue; breaking → status BLOCKED and stop.
- Owns IDs: REQ, AC, ENT, RULE, SCR-REQ — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-INV-014`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files):
- `governance-shared\analysis\modules\INV\P1\srs-inv.md` — absolute path `C:\repos\commerce\governance-shared\analysis\modules\INV\P1\srs-inv.md`
- `governance-shared\analysis\modules\INV\P1\registry-srs-inv.md` (registry) — absolute path `C:\repos\commerce\governance-shared\analysis\modules\INV\P1\registry-srs-inv.md`
- Write each file above, complete, with your Write tool at its ABSOLUTE path (a long file does not fit in one response); write a long file in parts if you must (Write the first part, then append with Edit), and end with a short summary of what you wrote. Write nowhere else. Only if you cannot write, respond with one `<<<FILE: path/from/the/repo/root.md>>>` … `<<<END FILE>>>` block per file.

## Contracts checked by `gov.py analyze` after this stage
- **C4** PRD → SRS (human PRD approval in between): C4.1 exists {'artifact': 'prd'} [CRITICAL]; C4.2 gate-approved {'gate': 'prd-approval'} [CRITICAL]; C4.3 ids-owned {'stage': 'P0.5'} [CRITICAL]; C4.4 traces {'from': 'US', 'to': ['POL'], 'min': 1} [MAJOR]; C4.5 no-questions {'stage': 'P0.5'} [CRITICAL]; C4.6 languages {'stage': 'P0.5'} [MAJOR]; C4.7 glossary {'artifact': ['prd'], 'glossary': 'vocabulary.glossary', 'synonyms': 'vocabulary.glossary_synonyms'} [MINOR]; C4.8 ids-continue {'stage': 'P0.5'} [CRITICAL]
- **C5** SRS → database: C5.1 exists {'artifact': 'srs'} [CRITICAL]; C5.2 ears {'kind': 'REQ', 'patterns': 'factory.ids.ears.patterns'} [CRITICAL]; C5.3 traces {'from': 'REQ', 'to': ['US'], 'min': 1} [MAJOR]; C5.4 orphans {'kind': 'REQ', 'referenced_by': ['AC'], 'min': 1} [CRITICAL]; C5.5 traces {'from': 'AC', 'to': ['REQ'], 'min': 1} [MAJOR]; C5.6 traces {'from': 'RULE', 'to': ['REQ'], 'min': 1} [MAJOR]; C5.7 ids-owned {'stage': 'P1'} [CRITICAL]; C5.8 registry-agree {'artifact': 'srs', 'registry': 'registry-srs', 'kinds': ['REQ', 'AC', 'ENT', 'RULE']} [MAJOR]; C5.9 no-questions {'stage': 'P1'} [CRITICAL]; C5.10 languages {'stage': 'P1'} [MAJOR]; C5.11 ids-continue {'stage': 'P1'} [CRITICAL]; C5.12 data-source {'kind': 'RULE', 'label': 'Data source', 'resolves_to': ['ENT'], 'deferral': 'DEFERRED'} [CRITICAL]; C5.13 ambiguity {'artifact': 'srs', 'kinds': ['REQ', 'AC', 'RULE'], 'lines': ['Statement', 'Given', 'When', 'Then'], 'lexicon': 'analyze.maturity.ambiguity_lexicon', 'extend': 'review.ambiguity_lexicon'} [MINOR]; C5.14 ac-measurable {'artifact': 'srs', 'kind': 'AC', 'label': 'Then', 'spec': 'analyze.maturity.measurable'} [MINOR]; C5.15 crud-covered {'artifact': 'srs', 'entity': 'ENT', 'kind': 'REQ', 'statement': 'Statement', 'spec': 'analyze.maturity.crud'} [MINOR]; C5.16 feature-unwanted {'artifact': 'srs', 'group': 'US', 'kind': 'REQ', 'statement': 'Statement', 'pattern': 'unwanted'} [MINOR]; C5.17 glossary {'artifact': ['srs'], 'glossary': 'vocabulary.glossary', 'synonyms': 'vocabulary.glossary_synonyms'} [MINOR]
- **C6** SRS + database → backend execution plan: C6.1 exists {'artifact': 'db-script'} [CRITICAL]; C6.2 traces {'from': 'DBF', 'to': ['REQ', 'ENT'], 'min': 1} [MAJOR]; C6.3 traces {'from': 'XM', 'to': ['REQ'], 'min': 1} [MAJOR]; C6.4 ids-owned {'stage': 'P2'} [CRITICAL]; C6.5 registry-agree {'artifact': 'db-script', 'registry': 'registry-db', 'kinds': ['DBF', 'XM']} [MAJOR]; C6.6 orphans {'kind': 'ENT', 'referenced_by': ['DBF'], 'min': 1} [MAJOR]; C6.7 no-questions {'stage': 'P2'} [CRITICAL]; C6.8 ids-continue {'stage': 'P2'} [CRITICAL]; C6.9 data-source {'kind': 'RULE', 'label': 'Data source', 'resolves_to': ['ENT'], 'deferral': 'DEFERRED', 'bound_in': 'db-script'} [CRITICAL]; C6.10 xm-record-valid {'artifact': ['db-script', 'registry-db']} [MAJOR]; C6.11 xm-tier-rule {} [CRITICAL]; C6.12 xm-transition-valid {'artifact': ['db-script', 'registry-db']} [MAJOR]; C6.13 graph-acyclic {} [CRITICAL]; C6.14 graph-target-known {} [CRITICAL]; C6.15 db-column-only {'artifact': 'db-script', 'register': ['db-script', 'registry-db']} [MAJOR]
- **C-GIVEN** given dependency → every stage: C-GIVEN-1 given-no-duplicate {'spec': 'capabilities.given', 'entity': 'ENT', 'srs': 'srs', 'db': 'db-script', 'when': 'factory.capabilities.given'} [MAJOR]; C-GIVEN-2 given-via-extension {'spec': 'capabilities.given', 'srs': 'srs', 'contract': 'contract', 'plan': 'backend-execution-plan', 'when': 'factory.capabilities.given'} [MAJOR]

## Blocks this stage emits — each a ```yaml name={name} fence, held to its schema by C0.1 (`gov.py analyze`)
- `module-dependencies` in `srs` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"module-dependencies","description":"P1 SRS §A8: the entities this module consumes from other modules (factory.yaml → xm.graph.module_block); `consumes: []` when it consumes nothing","type":"object","required":["consumes"],"additionalProperties":false,"properties":{"consumes":{"type":"array","items":{"type":"object","required":["module","entity","type"],"additionalProperties":false,"properties":{"module":{"type":"string","pattern":"^[A-Z][A-Z0-9]*$"},"entity":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"},"type":{"type":"string"}}}}}}
  ```
- `lookups` in `srs` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"lookups","description":"P1 SRS §A6: every lookup key the module owns — the values it seeds, whether the set is open (host data may add values), every value the analysis names for it, and the entity fields bound to it","type":"object","required":["lookups"],"additionalProperties":false,"properties":{"lookups":{"type":"array","items":{"type":"object","required":["key","seeded","open"],"additionalProperties":false,"properties":{"key":{"type":"string","pattern":"^[A-Z][A-Z0-9]*(?:_[A-Z0-9]+)*$"},"seeded":{"type":"array","items":{"type":"string"}},"open":{"type":"boolean"},"values":{"type":"array","items":{"type":"string"}},"fields":{"type":"array","items":{"type":"string"}},"owner":{"type":"string","pattern":"^[A-Z][A-Z0-9]*$"},"entity":{"type":"string"},"control":{"type":"string"},"source":{"type":"string"}}}}}}
  ```
- `given-usage` in `srs`, `contract` (optional) — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"given-usage","description":"P1 SRS (repeated verbatim by the P1.5 contract): how this module uses the project's given dependencies (factory.yaml → capabilities.given) — one row per use: the given, its module, the extension point / interface / API document the use goes through (`via`), and why; `rows: []` when the module uses none","type":"object","required":["rows"],"additionalProperties":false,"properties":{"rows":{"type":"array","items":{"type":"object","required":["given","module","via","purpose"],"additionalProperties":false,"properties":{"given":{"type":"string","minLength":1},"module":{"type":"string","minLength":1},"via":{"type":"string","minLength":1},"purpose":{"type":"string","minLength":1}}}}}}
  ```

## The previous run of this stage was refused by `gov.py analyze` — fix exactly these, keep everything else
- [CRITICAL] C5.2 (ears) line 1002: `REQ-INV-044` statement matches no EARS pattern: “While the tenant disallows negative stock, if a reservation request exceeds the ”
- [CRITICAL] C5.2 (ears) line 1016: `REQ-INV-045` statement matches no EARS pattern: “While the tenant allows negative stock, if a reservation request exceeds the ava”
- [CRITICAL] C5.2 (ears) line 1335: `REQ-INV-067` statement matches no EARS pattern: “Where the tenant disallows negative stock, if an online issue, transfer or stock”
- [CRITICAL] C5.2 (ears) line 1575: `REQ-INV-083` statement matches no EARS pattern: “Where the tenant disallows negative stock, if posting a transfer would take the ”
- [MAJOR] C5.8 (registry-agree) `registry-srs`: `AC-INV-155` registered in `registry-srs` but not defined in `srs`
- [MAJOR] C5.8 (registry-agree) `registry-srs`: `ENT-INV-015` registered in `registry-srs` but not defined in `srs`
- [MAJOR] C5.8 (registry-agree) `registry-srs`: `RULE-INV-049` registered in `registry-srs` but not defined in `srs`

---
# ENGINE
# SRS — ENGINE

```
Engine        : SRS
Stage id      : P1
Pass          : 1
Questions     : forbidden — ambiguity is self-resolved (§9)
Lane          : analysis
Inputs        : prd, domain-profile, project-registry   (PRD must be APPROVED — gate prd-approval)
Produces      : srs-{mod}.md · registry-srs-{mod}.md (registry)
Owns IDs      : REQ, AC, ENT, RULE, SCR-REQ   → `{prefix}-{MOD}-{seq}` (seq width 3)
Format        : EARS for every functional requirement (§4)
Next          : P1.5
Module        : INV   Version: 1
Profile       : commerce — COMMERCE
```

This engine produces the module's **functional truth**: the SRS. Everything downstream
(database, execution plans, UX, tests) derives from it; when a downstream artifact
disagrees with the SRS, the SRS governs and the other artifact is corrected. The SRS
never contains DDL, execution phases, component names or any ID owned by a later stage. [C:C5.7]

Completion (write → registry → analyze → commit) is owned by the orchestrator — see
`shared/GOVERNANCE-CORE.md`. In a delta version (version > 1): read
`_state/current-{artifact}` of the previous version for every input and for this
stage's own artifacts, and emit only ADDED / MODIFIED / REMOVED elements plus the [C:C12.2]
`change-manifest.md` per `shared/VERSIONING.md`. ID sequences continue from the current state.

Language policy: narrative in `en`; every label, screen name and message carries all
of `ar, en` — a single-language message is INCOMPLETE; identifiers, IDs and field names in
the domain-profile's identifier language.

### IDs this stage assigns

| Atom | Meaning | Traces to | Requires |
|---|---|---|---|
| `REQ` | requirement (EARS) | US | AC |
| `AC` | acceptance criterion (Given/When/Then) | REQ | — |
| `ENT` | entity | — | — |
| `RULE` | business rule | REQ | — |
| `SCR-REQ` | screen requirement | REQ | — |

Sequences are continuous per module and per atom, never reused. [C:C5.11]

---

## 1 — Inputs and reading protocol

```
STEP A — PRD (approved): every P0.5 story with its Traces → the demand this SRS must cover
STEP B — domain-profile.md §7 STEERING: vocabulary (verbatim), bounded contexts, codes,
         identifier rules, knowledge sources; §8 resolved decisions — not re-opened [G]
STEP C — project-registry.md (shared/REGISTRY-SCHEMA.md categories):
         entity ownership + shared declarations → reuse, do not re-create [G]
         dependency graph (platform/dependency-graph.json) → edges to carry into A8
         structural registry                      → names already fixed by other modules
         open-question index                      → must be empty for this module
STEP D — this stage's upstream module artifacts (module registry, business policies):
         entities owned / lookups / dependencies → names as given
         policies                                → the rules and requirements they imply
STEP E — knowledge sources (cite when applying a default)
         - profiles/commerce/knowledge/raw-idea.md
```

### 1.1 Registry pre-check (before any generation)

```
Does this entity already exist (any module)?      → reuse its ID; do not re-create
Does an equivalent lookup exist?                    → reuse its key
Does the module exist in the registry?              → extend, continue sequences
Naming conflict with a registered element?          → §9: ADR (non-breaking) or ADR BLOCKED
A closed decision / resolved open item affects it?  → apply as-is, no deviation
```

### 1.2 Feature type (auto-determined, documented, not asked)

```
Entity kinds (profile.vocabulary.entity_kinds): master / transactional / lookup / config / security
For every entity: kind + reason (one line), recorded in A3. The user is not asked.
```

---

## 2 — Resolution order (zero questions)

Before writing any section, answer every needed fact from, in order:

```
1. business policies (P0)   → apply directly; cite the policy id
2. PRD stories (P0.5)            → the need; scope and priority
3. registries (project + module)         → ownership, names, dependencies
4. knowledge sources (§1 STEP E)         → domain defaults — apply and document as DEFAULT
5. domain-profile STEERING + rules       → vocabulary, constraints
6. domain best practice                  → choose, document as ADR (§9)

DEFAULT documentation (inline, where applied):
  DEFAULT : [what was decided]
  Source  : [knowledge file § / domain-profile §]
  Override: [what to change if the client wants otherwise]
```

Nothing is asked. A fact that 1–6 cannot settle is an ambiguity → §9.

---

## 3 — Entities (`ENT`)

### 3.1 Ownership classification — first step, before any ID

| Classification | Rule | Declaration |
|---|---|---|
| PRIVATE | fully owned by this module | `ENT-INV-[SEQ]` — [name] — PRIVATE |
| SHARED (owner) | other modules consume read-only | `ENT-INV-[SEQ]` — [name] — SHARED (owner) |
| SHARED (consumer) | mastered by another module — NO new ID | consumes `ENT-[OWNER]-[SEQ]` — HARD-FK / SOFT-READ → XM candidate for P2 |
| GIVEN | provided by a library the project builds ON (`erp-core`) — NO new ID, no XM edge | used through the given's interface / extension point — one row of the A9 `given-usage` block names the module and the `via`; an `ENT` that re-creates a given's entity or a table that re-creates a given's table is refused [C:C-GIVEN-1] |

Entities already registered by another module are consumed, not re-created. [G]
What a given provides is listed in its manifest (`deps/erp-core/core-manifest.yaml`): the entities
`erp-core:modules.audit` → AuditEvent; `erp-core:modules.cu` → AppConfiguration; `erp-core:modules.file` → FileCategory, FileDocument; `erp-core:modules.mdl` → LookupType, LookupValue; `erp-core:modules.notif` → NotificationChannelConfig, NotificationInboxItem, NotificationLog, NotificationLogAttachment, NotificationTemplate; `erp-core:modules.sec` → ActionRegistry, AuditLogEntry, ModuleRegistry, Role, RoleActionGrant, RoleModuleGrant, RoleScreenGrant, ScreenRegistry, SignupRequest, User, UserRoleAssignment; `erp-core:modules.sequence` → NumberSeries; `erp-core:modules.tenant` → Tenant are the given's, reached through
`erp-core:modules.audit` → com.erp.audit.crossmodule.AuditApi · api `docs/api-docs/audit/index.md`; `erp-core:modules.cu` → com.erp.cu.crossmodule.SettingsApi · api `docs/api-docs/cu/index.md`; `erp-core:modules.events` → com.erp.events.DomainEventPublisher, com.erp.events.OutboxPublisher, com.erp.events.OutboxReplayApi, com.erp.events.OutboxDeliveryGate, com.erp.events.OutboxSubscriber; `erp-core:modules.file` → com.erp.file.crossmodule.FileDocumentLookupApi, com.erp.file.crossmodule.FileImageStoreApi, com.erp.file.crossmodule.FilePrivateStoreApi, com.erp.file.service.DownloadTokenStore, com.erp.file.storage.StorageProvider · api `docs/api-docs/file/index.md`; `erp-core:modules.gl` → com.erp.gl.crossmodule.GlAccountLookupApi, com.erp.gl.crossmodule.GlEntryQueryApi, com.erp.gl.crossmodule.GlPostingApi · api `docs/api-docs/gl/index.md`; `erp-core:modules.mdl` → com.erp.mdl.crossmodule.MdlLookupApi · api `docs/api-docs/mdl/index.md`; `erp-core:modules.module` → com.erp.module.crossmodule.HomeCardApi, com.erp.module.crossmodule.ModuleCatalogApi, com.erp.module.crossmodule.ModuleEntitlementApi, com.erp.module.HomeCardProvider · api `docs/api-docs/module/index.md`; `erp-core:modules.notif` → com.erp.notif.crossmodule.NotificationChannelAdminApi, com.erp.notif.crossmodule.NotificationDispatchApi, com.erp.notif.crossmodule.NotificationLogQueryApi, com.erp.notif.channel.ChannelProvider, com.erp.notif.crossmodule.RecipientDirectory · api `docs/api-docs/notif/index.md`; `erp-core:modules.report` → com.erp.report.crossmodule.PaymentFileExportApi, com.erp.report.crossmodule.PrintApi, com.erp.report.ReportProvider, com.erp.report.crossmodule.PrintAuthorizationHook, com.erp.report.crossmodule.PrintParameterContributor, com.erp.report.crossmodule.PrintTemplateSource · api `docs/api-docs/report/index.md`; `erp-core:modules.sec` → com.erp.sec.crossmodule.SecAdminRecoveryApi, com.erp.sec.crossmodule.SecCustomerDefaultRoleApi, com.erp.sec.crossmodule.SecCustomerInvitationApi, com.erp.sec.crossmodule.SecModuleRegistryApi, com.erp.sec.crossmodule.SecUserDirectoryApi, com.erp.sec.permission.PermissionContributor · api `docs/api-docs/sec/index.md`; `erp-core:modules.sequence` → com.erp.sequence.crossmodule.NumberSeriesApi · api `docs/api-docs/sequence/index.md`; `erp-core:modules.tenant` → com.erp.tenant.TenantPolicy, com.erp.tenant.TenantRunner, com.erp.tenant.crossmodule.TenantLookupApi, com.erp.tenant.TenantExportContributor, com.erp.tenant.TenantProvisioningContributor · api `docs/api-docs/tenant/index.md`. [C:C-GIVEN-2]

### 3.2 Defaults per entity kind (profile.conventions.entity_defaults)

Every entity of a kind below carries these fields automatically (in A3 they are
listed once under "standard fields — per profile", not re-typed per entity):

| Kind | Default fields |
|---|---|
| master | nameAr, nameEn, code, isActiveFl, createdBy, createdAt, updatedBy, updatedAt |
| transactional | docNo, docDate, statusCode, createdBy, createdAt, updatedBy, updatedAt |
| lookup | code, nameAr, nameEn, sortOrder, isActiveFl |
| config | key, valueAr, valueEn, isActiveFl |

Naming (profile.stack.db.naming): primary key `{entity}Pk`; flag fields end with `Fl`; audit fields `createdBy, createdAt, updatedBy, updatedAt` are system-filled and not accepted from a client. Field names are [G]
taken from the module registry, the policies and the knowledge sources — not invented [G]
from generic templates. Physical types belong to P2; the SRS states the
logical type only (text / number / decimal / flag / date-time / lookup / reference). [G]

### 3.3 Structural rules

```
ARCH-1  Reuse before create — registry first.
ARCH-2  Master data has ONE owner; others consume via XM (not duplicated). [G]
ARCH-3  Every referenced entity is defined (own ID) or consumed (owner's ID); no
        undefined reference anywhere.
ARCH-4  Names match the registry exactly. [G]
ARCH-5  A SHARED (owner) entity that can be deactivated/deleted → a RULE stating the
        effect on SOFT-READ consumers (prevent / notify / cascade), traced to the REQ
        that introduces the operation. Undecidable → ADR (§9), never an open question. [T:questions-forbidden]
IDENTIFIERS Profile rule: every entity identifier is a BIGINT identity key the database assigns (stack.db.pk_generation = identity); a contract states identifier type BIGINT; a consumer's HARD-FK column is BIGINT; no other identifier type exists in v1
        Every entity's identifier field states this type in A3; no other identifier type is invented. [G]
LOOKUPS Profile rule: all LOV values runtime-loaded from the core lookup module (mdl); no hardcoded enums in APIs or field specs; activity presets and capability flags are core settings, read through FeatureFlags
        Lookup-backed fields reference a lookup key declared in A6; the SRS decides
        control type (fixed short list → lookup; growing/large set → reference entity
        with its own ENT). Same field → same lookup key in every screen.
NUMBERING Profile rule: document numbers come from the core sequence module; never generated in a business module
        Business/document numbers: decided PER ENTITY, not module-wide. An entity gets [G]
        a business number only if (a) its identifier is used outside the system, (b) a [G]
        policy/story asks for a human-readable reference, or (c) it is the numbered
        transactional document itself. If yes → system-generated on first save,
        read-only after, unique per entity type; the RULE cites the numbering rule above.
WORKFLOW Profile: workflow engine `forbidden`. Status lifecycles (a status field +
        allowed transitions) are documented (A7). A module-specific approval flow [G]
        is written only when a story explicitly asks for it and is custom to the module — [G]
        not a generic engine. [G]
```

---

## 4 — Requirements (`REQ`) — EARS is mandatory [C:C5.2]

Every functional requirement is **exactly one** EARS pattern [C:C5.2]
(`factory.ids.ears.patterns`); free prose is a contract violation (`gov.py analyze`:
CRITICAL).

```
  ubiquitous  The system shall <response>
  state       While <condition / trigger / feature>, the system shall <response>
  event       When <condition / trigger / feature>, the system shall <response>
  optional    Where <condition / trigger / feature>, the system shall <response>
  unwanted    If <condition / trigger / feature>, then the system shall <response>
  complex     a legitimate composition of the above (e.g. While … , when … , the system shall …)
```

```
REQ-INV-[SEQ] — [short name]
  Pattern    : [ubiquitous | state | event | optional | unwanted | complex]
  Statement  : [one EARS sentence — one behaviour, one subject "the system"]
  Traces     : P0.5-INV-[SEQ] [, …]      (≥ 1, mandatory) [C:C5.3]
  Entities   : ENT-INV-[SEQ] [, …]
  Rationale  : [one line — why]
  Source     : [PRD story / policy / knowledge file / ADR]
  Priority   : [from the story]
```

Rules: singular (one requirement per REQ — "and" between behaviours means two REQs);
verifiable (a test can pass/fail it); no design (no table, endpoint, component);
every P0.5 story is covered by ≥ 1 REQ; every REQ traces to ≥ 1 story
(a REQ with no story = invented scope → remove or raise an ADR).

### 4.1 Acceptance criteria (`AC`) — ≥ 1 per REQ

```
#### AC-INV-<seq> — [REQ-INV-<seq>]        ← the square brackets are LITERAL: they are the trace to the REQ
  Given  : <precondition / state>
  When   : <action / event>
  Then   : <observable outcome — with the exact message when one is shown, in each of ar/en>
```
(`<…>` is a placeholder; `[…]` is not — `gov.py analyze` C5.5 reads the REQ id inside the brackets on the heading, and an AC without them traces to nothing. Runs 8 and 9 of the golden wrote `— REQ-…` bare and lost every trace.)

Rules: each AC tests one path of one REQ (happy path first, then each unwanted/edge
path); an AC that cannot be phrased Given/When/Then means the REQ is not verifiable —
rewrite the REQ. ACs are the mechanical source of test cases for the test stage
(`P4` — `TC` traces to `AC`).

---

## 5 — Business rules (`RULE`)

```
RULE-INV-[SEQ] — [short name]
  Scope      : ENT-INV-[SEQ]
  Trigger    : [when evaluated — on create / update / submit / transition …]
  Statement  : The system shall [prevent / require / validate …] when [condition]
  Message    : ar: [text] · en: [text]   (business language, not a literal translation)
  Traces     : REQ-INV-[SEQ] [, …]                     (≥ 1, mandatory) [C:C5.6]
  Data source: ENT-INV-[SEQ].[field] [, …]             (mandatory — see below) [C:C5.12]
  Source     : [policy id / story / DEFAULT / ADR]
  Test-Hint  : [optional, one line of business intent for the test stage; omit if obvious]
```

Rules: a RULE formalises a constraint that a REQ needs; it never introduces behaviour [C:C5.6]
absent from every REQ. Database errors do not reach users — every constraint that can [G]
fail has a RULE with a message. Rules are defined once (A5) and referenced by ID from
every screen block.

**`Data source` — where the data the check READS comes from (mandatory).** `Scope` says [C:C5.12]
which entities the rule *guards*; `Data source` says which declared fields the rule
*reads to decide*. Every field named here is an `ENT-INV-[SEQ].[field]` that A3
actually declares (the next stage binds each one to a column, so a field A3 never [C:C6.9]
declares can never be read at runtime). [C:C6.9]

A rule whose statement leans on data this module does not declare — "a
{module|admin|externally}-declared X", "a configured Y", "a registered pair" — has two honest
outcomes, and no third:

```
  Data source: ENT-INV-[SEQ].[field]        ← the declaration surface exists: name it,
                                                    and A3 carries the field / A6 the lookup
  Data source: DEFERRED — no declaration surface in this version ([what would be needed])
```

A `DEFERRED` rule is still written, still traced and still counted, but it is marked
unenforceable *here* — the next stages do not mint an error code, an enforcement query or
an enforcing endpoint for it, and the test stage does not derive a test case whose precondition
no API of this version can establish. Emitting such a rule as if it were enforceable
produces a guard that compiles, an error code that is registered, and a check that can
never fire. `gov.py analyze` resolves this mechanically (`data-source`): a rule with [C:C5.12]
neither a resolvable `Data source` nor the deferral marker is a finding, not a pass.

---

## 6 — Lookups and status lifecycle

```
A6 LOOKUPS — the `lookups` block (data, not prose — `gov.py analyze` reads it: tc-data, tc-consistency, bootstrap-complete; C0.1 holds it to its schema)
  one row per lookup key this module OWNS: key · seeded (the values the product seeds) · open
  (true when host data may add values — "added as data") · values (EVERY value the analysis
  names for the key, seeded or not) · fields (the entity fields bound to it) · entity · control · source
  Consumed lookups are listed by key + owner only (not redefined). [G]
  The labels per language and the rationale stay in the prose beside the block.

A7 STATUS LIFECYCLE — for every entity with a status field and > 2 transitions
  Diagram of states and allowed transitions ONLY — no roles, no approval steps.
  Each transition that carries a constraint → RULE id.
  Module-specific approval flow (only if a story explicitly asks and the profile allows): [G]
  documented as its own block with the story it traces to.
```

---

## 7 — Screens for `P3.2` (`SCR-REQ`)

This stage lists what screens the module needs — functional scope, not design.
`P3.2` turns each entry into `SCR` / `UXD` decisions and owns pattern,
container, layout and component choices. This stage never decides those. [C:C5.7]

```
SCR-REQ-INV-[SEQ] — [screen name in each of ar/en]
  Purpose      : [what the user achieves]
  Entities     : ENT-INV-[SEQ] [, …]
  Operations   : [search / list / create / read / update / deactivate / custom …]
  Users        : [roles]
  Navigation   : [module] → [menu] → [screen]; from: [screens]; to: [screens]
  Content shape: [flat record | header + repeating lines with totals | true hierarchy
                  (parent/child) | other (journal, calendar …)] — a hint, not a design
  Traces       : REQ-INV-[SEQ] [, …]
  Composite    : Search + Entry (or Master + Detail, Wizard) = ONE screen requirement
                 (profile.conventions.composite_screen) — not one per sub-screen [G]
```

Screen rules: search filters correspond to result columns; the same field uses the same
lookup key in search and entry; every screen declares its navigation position; every
operation on a screen is backed by a REQ.

### 7.1 Access (profile.conventions.security_model)

```
Page registry : SEC_PAGES   — one row per screen requirement
Permission    : PERM_<PAGE_CODE>_<ACTION>  with actions VIEW / CREATE / UPDATE / DELETE
Gateway       : VIEW — without it no other action applies
```
The SRS declares, per screen requirement, its page code and which roles hold which
action. It does NOT enumerate permission names as seed data — the security module
derives them from the page code (a second source of truth is a DUPLICATE finding).
No hardcoded authorisation logic is specified anywhere; checks go through the platform
authorisation layer. Implementation (annotations, guards) belongs to later stages.

---

## 8 — API expectations (stack-neutral)

The SRS states what operations the backend must expose; `P3.1` assigns the
`API` ids and designs them. Expectations follow the profile's conventions
(profile.stack.backend.api) and are referenced from screens by REQ, not by an API id. [G]

```
Base path      : /api/v1/{module}/{resource}
Verbs          : POST = create · GET = read · PUT = update · DELETE = deactivate (soft) · PATCH = partial
Response       : ApiResponse<T>
Paging         : Page<T>
Errors         : LocalizedException → {code, messageAr, messageEn}

| Operation | Verb | Path (per base path) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| create [entity] | [verb] | [base path with module/resource] | [fields] | [entity] | RULE-… | REQ-… |
| search [entity] | [verb] | … | [filters, paging] | [page of entity] | — | REQ-… |
| update [entity] | [verb] | …/{id} | [fields] | [entity] | RULE-… (immutability of number/code if any; uniqueness of names) | REQ-… |
| deactivate [entity] | [verb] | …/{id} | id | confirmation | RULE-… | REQ-… |
| read [entity] | [verb] | …/{id} | id | [entity] | — | REQ-… |
```
Lookup values are runtime-loaded (not enumerated in an API definition). Mobile or [G]
channel-specific behaviour is stated as a REQ (`Where` pattern), not as widget design.

---

## 9 — Ambiguity rule (questions are forbidden here) [T:questions-forbidden]

```
When steps 1–6 of §2 leave a genuine choice:
  NON-BREAKING (does not contradict an approved story, a policy, a registry fact or
  a closed decision) → write an ADR and CONTINUE with the chosen best practice.
      action: adr · then: continue
  BREAKING (contradicts an approved story / policy / registry fact / closed decision,
  or would change a REQ another artifact already relies on) → write the ADR with
  status BLOCKED and STOP the pass; the orchestrator surfaces it at the next
  human point.
      action: adr · status: BLOCKED · then: stop

ADR file : analysis/decisions/INV/ADR-{MOD}-{seq:03d}.md
Content  : Context · Decision · Consequences · traces (REQ / ENT / story ids) · status
Every ADR is referenced from the SRS "Decisions applied" section (§10 STANDALONE).
Details: shared/GOVERNANCE-CORE.md.
```

---

## 10 — `srs-{mod}.md` — canonical template

Structure: **PART A** (module foundation — defined once) → **PART B** (one block per
screen requirement — references PART A by ID, does not redefine) → **STANDALONE**. [G]
No section is omitted; a section that does not apply says so in one line.

```markdown
# SRS — [Module display] (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Profile : commerce
Inputs : prd, domain-profile, project-registry (PRD approved [date])
Counts : REQ [N] · AC [N] · ENT [N] · RULE [N] · SCR-REQ [N] · ADR [N]
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information
| Item | Value |   (module, feature code, version, date, status, prepared by, decisions applied count)

## A2 — Functional context
In scope · Out of scope · Module function (one paragraph) · Detailed description
(workflow narrative, roles) · Current situation (steps / party / notes) ·
Current difficulties · Proposed system and benefits · General notes (constraints,
deferred items) — delete a sub-section only if it has no content and say so. [G]

## A3 — Entities and fields
Standard fields per kind (profile) — listed once here.
### ENT-INV-001 — [name]
| Kind | Ownership | Business number (yes/no — per §3.3 test) | Operations | Cross-module | Source |
| Field | Logical type | Required | Values / source (lookup key, ENT ref) | Notes | Label-ar | Label-en |
(repeat per entity)

## A4 — Functional requirements (EARS) and acceptance criteria
### REQ-INV-001 — [name]        (record §4)
#### AC-INV-001 … (record §4.1, ≥ 1 per REQ)
(repeat per requirement)

## A5 — Business rules
### RULE-INV-001 — [name]       (record §5)
(repeat per rule — the ONLY place rule text appears)

## A6 — Lookups                      (§6 — the ONLY place lookup values appear)
```yaml name=lookups
lookups:
  - {key: [KEY], seeded: [CODE_A, CODE_B], open: false, values: [CODE_A, CODE_B], fields: [fieldName], entity: ENT-INV-[SEQ], control: lookup, source: "[policy / default]"}
```

## A7 — Status lifecycle             (§6 — diagram only; "not applicable" if ≤ 2 states) [G]

## A8 — Module dependencies
```yaml name=module-dependencies
consumes:            # data, not prose — `gov.py graph` reads this named block; XM ids are assigned by P2
  - {module: [OWNER], entity: ENT-[OWNER]-[SEQ], type: HARD-FK | SOFT-READ}
```
(`consumes: []` when this module consumes nothing — the block is always present; C0.1 refuses an SRS without it) [C:C0.1]
| External service | Purpose | Integration kind |

## A9 — Given usage                  (how this module uses the libraries the project builds ON — C-GIVEN-2 reads this block)
```yaml name=given-usage
rows:                        # one row per use; `rows: []` when this module uses no given
  - {given: [erp-core], module: [a module key of its manifest], via: [one of that module's extension points / interfaces / api document — verbatim], purpose: "[what for]"}
```
Every `via` is a value the given's manifest lists for that module — an interface the module calls, an
extension point it implements, or the API document it integrates with — never a class or path of the module's own [C:C-GIVEN-2]
invention; the contract stage repeats these rows verbatim. [C:C-GIVEN-2]

# PART B — SCREEN REQUIREMENTS   (one block per SCR-REQ; references PART A by ID only) [G]

## SCR-REQ-INV-001 — [name]
### B1 — Definition        (record §7: purpose, entities, operations, users, navigation, content shape, traces)
### B2 — Search / list     (filters = result columns; lookup keys by reference; RULEs applied by id) — "not applicable" if no search
### B3 — Input             (fields by ENT reference; buttons/actions → operation + RULE ids)
### B4 — Access            (page code + roles per action per §7.1)
### B5 — API expectations  (table §8, scoped to this screen)
(repeat per screen requirement)

# STANDALONE

## Traceability matrix
| P0.5 | REQ | AC | RULE | ENT | SCR-REQ |
(every story → ≥ 1 REQ; every REQ → ≥ 1 AC; every RULE → REQ; every SCR-REQ → REQ.
 Orphans and dangling references are gate failures — `gov.py analyze`.)

## Decisions applied
| DEFAULT / ADR | What | Source | Override / status |

## Access summary            (aggregate of B4 — B4 is the source)
══════════════════════════════════════════════════════════════════
```

Single-source rule: PART A defines; PART B references by ID ("applies RULE-INV-003").
Restating rule, lookup or entity text in PART B is a DUPLICATE finding (MAJOR).

---

## 11 — `registry-srs-{mod}.md` — registry content

```
## REGISTRY — P1 — INV v1
Entities      : ENT id · name · kind · PRIVATE / SHARED(owner) · status REGISTERED
Consumed      : the ids of the A8 `module-dependencies` block   (→ gov.py graph)
Lookups owned : key · ENT · values count          Lookups consumed : key · owner
Screens       : SCR-REQ id · name · page code
Requirements  : REQ count · AC count · RULE count · last sequence per atom
                (REQ: [n], AC: [n], ENT: [n], RULE: [n], SCR-REQ: [n])
Decisions     : ADR ids (+ BLOCKED, if any)
Event         : "P1 completed: INV v1 — [counts]"
```
The orchestrator merges these rows into `project-registry.md` (entity ownership,
shared declarations, structural registry, pipeline status, events); dependencies reach the graph through §A8, not through the registry. [G]

---

## 12 — Boundaries

```
OWNS      : REQ, AC, ENT, RULE, SCR-REQ · functional truth · the traceability matrix from stories down
DOES NOT  : POL (P0) · US (P0.5) · DBF (P2) · XM (P2) · CON (P1.5) · API (P3.1) · QR (P3.1) · UXD (P3.2) · SCR (P3.2) · TC (P4) · FEAT (feature)
            · DDL / physical types · execution phases · UX patterns, containers, components
            · endpoint design · permission seed data · test cases
```

---

## 13 — Self-check before emitting (ISO/IEC/IEEE 29148 attributes + structure)

Quality attributes scored at the pass gate (`factory.review.rubric`):
- [ ] Every RULE carries a `Data source` that either names `ENT-INV-[SEQ].[field]` values A3 declares, or is the explicit `DEFERRED — no declaration surface in this version` marker (§5).
- [ ] No ENT re-creates an entity a given provides (§3.1 GIVEN); A9 carries the `given-usage` block, every row's `via` taken verbatim from the given's manifest. [C:C-GIVEN-2]

 [C:C5.13]- [ ] **unambiguous** — one reading per REQ / AC / RULE; no "etc.", "as appropriate", "fast"
- [ ] **verifiable** — every REQ has ≥ 1 Given/When/Then AC; every RULE has a message
- [ ] **complete** — every story covered; A1–A8, every B1–B5, STANDALONE present; no placeholder left
- [ ] **consistent** — vocabulary = STEERING block; names = registry; no REQ contradicts a policy or another REQ
- [ ] **singular** — one behaviour per REQ; one path per AC
- [ ] **feasible** — no requirement depends on an undefined entity, unavailable module or forbidden mechanism
- [ ] **traceable** — REQ→P0.5, AC→REQ, RULE→REQ, SCR-REQ→REQ all present; no orphan, no dangling id

Structural checks:
- [ ] Every REQ statement matches exactly one EARS pattern. [C:C5.2]
- [ ] Every entity has a kind from `master, transactional, lookup, config, security` and carries its kind's default fields.
- [ ] Every consumed entity references the owner's ENT id; none re-created.
- [ ] Rules, lookups, entities defined in PART A only; PART B references by ID. [G]
- [ ] Every DEFAULT has Source + Override; every ADR is listed under Decisions applied; no BLOCKED ADR unless the pass stopped.
- [ ] No question raised anywhere; no open-questions section exists.
- [ ] Sequences continuous per atom.
- [ ] Every label, screen name and message carries all of `ar, en`.
- [ ] Profile check `CM-1` (MAJOR): every master entity carries the master entity_defaults.
- [ ] Profile check `CM-2` (MAJOR): every screen maps to exactly one composite SCR and one SEC_PAGES row.
- [ ] Profile check `CM-6` (CRITICAL): a requirement, rule or screen that varies by trading activity names a capability flag read through FeatureFlags — never an activity name or preset (D2).
- [ ] Profile check `CM-7` (MAJOR): every required cell of the flag × activity matrix (brief §4.2) traces to an EARS requirement id; an untraced cell is a gap.
- [ ] Profile check `CM-9` (MINOR): a screen states at most four mandatory fields outside its Advanced section (brief §6.1).


---
# INPUTS (generated current state)

<<<INPUT: prd>>>
# PRD — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module          : INV     Version : v1
Source artifacts: platform-summary, module-registry, business-policies
Stories         : 28   Policies covered : 22/22   Deferred : 0
Status          : DRAFT — awaiting prd-approval
══════════════════════════════════════════════════════════════════

Roles used are the three role presets of KB §3 D8 (Owner, Seller/Cashier, Warehouse) and the storefront customer. A "Warehouse user" is a user holding the Warehouse role preset. Each story states a need. P1 decides the rules, data and permissions behind it. Priorities are derived from the delivery wave and launch order (decision INV-P11), not stated by the client, and the user may adjust them at the gate.

## USER STORIES

US-INV-001
  Title (en)     : Every channel uses one stock
  Title (ar)     : مخزون واحد لجميع القنوات
  Story          : As an Owner, I need back-office documents, the point of sale and the storefront to draw from the same stock in each warehouse, so that what I sell anywhere reflects what I actually have.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-001
  Source         : KB:raw-idea.md §1 "one catalogue, one stock, one customer base"; §2 (differentiator); §8 wave 1
  Status         : DRAFT

US-INV-002
  Title (en)     : See stock balances by variant and warehouse
  Title (ar)     : أرصدة المخزون حسب المتغيّر والمستودع
  Story          : As a Seller/Cashier or a Warehouse user, I need to see how much of each item variant is on hand and still available in each warehouse, so that I can promise and supply goods with confidence.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-001, POL-INV-002
  Source         : module-registry ENTITIES OWNED (Stock balance, read by SLS, SFR, INS); DECISION INV-P05 (stock keys on the Variant); KB §5 #3
  Status         : DRAFT

US-INV-003
  Title (en)     : Set aside stock for orders and storefront checkout
  Title (ar)     : حجز المخزون للطلبات والدفع في المتجر
  Story          : As a Seller/Cashier taking an order, or a customer checking out on the storefront, I need the goods of that order set aside from the shared stock until it is fulfilled or cancelled, so that the same goods are not promised twice across channels.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-001
  Source         : module-registry ENTITIES OWNED (Reservation) and AUTO "SLS (orders) and SFR (checkout) create reservations … A POS sale issues stock directly"; KB §5 #3 "reservations"
  Status         : DRAFT

US-INV-004
  Title (en)     : A default warehouse is ready at the start
  Title (ar)     : مستودع افتراضي جاهز عند البدء
  Story          : As an Owner starting on the platform, I need a warehouse to exist from the first moment, so that I can receive and sell stock without setting up warehouses.
  Priority       : HIGH
  Success metric : Contributes to the shared setup metric: setup completed and first invoice issued in ≤ 30 minutes without training (KB §6.10)
  Traces         : POL-INV-003
  Source         : KB:raw-idea.md §6.8 "one warehouse/branch/currency/price list to start"; module-registry AUTO (default warehouse seeded at provisioning)
  Status         : DRAFT

US-INV-005
  Title (en)     : Every stock move records its warehouse
  Title (ar)     : المستودع في كل حركة مخزون
  Story          : As an Owner, I need every stock move to remember the warehouse it happened in, even while I have only one, so that adding a second warehouse later needs no data migration.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-002
  Source         : KB:raw-idea.md §6.8 "every stock move carries warehouse_id"; DP §5 G8
  Status         : DRAFT

US-INV-006
  Title (en)     : Screens stay simple while there is one warehouse
  Title (ar)     : شاشات بسيطة مع مستودع واحد
  Story          : As a Seller/Cashier or a Warehouse user in a business with one active warehouse, I need screens and reports not to ask me about warehouses or offer transfers, so that I see only what is meaningful to me.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-004
  Source         : KB:raw-idea.md §6.3 "no dead menus", §6.8 "the UI hides the selector while the count is 1"
  Status         : DRAFT

US-INV-007
  Title (en)     : Add and maintain warehouses
  Title (ar)     : إضافة المستودعات وصيانتها
  Story          : As an Owner whose business grows, I need to add warehouses, keep their details current and take one out of use, and to get warehouse choice, transfers and warehouse report filters as soon as a second warehouse exists, so that multi-warehouse work starts without migration or re-setup.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-005, POL-INV-004
  Source         : KB:raw-idea.md §6.8 "reveals it — plus the related actions (e.g. inter-warehouse transfer) and report filters — automatically"; module-registry ENTITIES OWNED (Warehouse)
  Status         : DRAFT

US-INV-008
  Title (en)     : Transfer stock between warehouses
  Title (ar)     : التحويل بين المستودعات
  Story          : As a Warehouse user in a business with more than one warehouse, I need to move stock from one warehouse to another without changing what the goods are worth, so that each warehouse balance shows where the goods really are and the total stock value does not shift because of the move.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-002, POL-INV-004, POL-INV-005, POL-INV-021
  Source         : KB:raw-idea.md §5 #3 "transfers", §6.8 "inter-warehouse transfer"; DECISION INV-P09 "transfers carry cost unchanged"
  Status         : DRAFT

US-INV-009
  Title (en)     : A default branch is ready at the start
  Title (ar)     : فرع افتراضي جاهز عند البدء
  Story          : As an Owner starting on the platform, I need a branch to exist from the first moment, so that I can issue documents without setting up branches.
  Priority       : HIGH
  Success metric : Contributes to the shared setup metric: setup completed and first invoice issued in ≤ 30 minutes without training (KB §6.10)
  Traces         : POL-INV-015
  Source         : DECISION INV-P07; KB:raw-idea.md §6.8 "one warehouse/branch/currency/price list to start"
  Status         : DRAFT

US-INV-010
  Title (en)     : Screens stay simple while there is one branch
  Title (ar)     : شاشات بسيطة مع فرع واحد
  Story          : As a user of a business with one active branch, I need screens and reports not to ask me about branches, so that I see only what is meaningful to me.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-016
  Source         : DECISION INV-P07; KB:raw-idea.md §6.3, §6.8
  Status         : DRAFT

US-INV-011
  Title (en)     : Add and maintain branches
  Title (ar)     : إضافة الفروع وصيانتها
  Story          : As an Owner opening another outlet, I need to add branches, keep their details current and take one out of use, and to get branch choice and branch report filters as soon as a second branch exists, so that multi-branch work starts without migration or re-setup.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-017, POL-INV-016
  Source         : DECISION INV-P07; KB:raw-idea.md §6.8 "reveals it … automatically"; module-registry ENTITIES OWNED (Branch, read by PUR, SLS, STL, TAX, INS)
  Status         : DRAFT

US-INV-012
  Title (en)     : Load opening stock
  Title (ar)     : تحميل المخزون الافتتاحي
  Story          : As an Owner starting on the platform, I need to load the quantities and cost of the stock I already hold, including many items at once, so that balances and stock value are right from the first day.
  Priority       : HIGH
  Success metric : Contributes to the shared setup metric: setup completed and first invoice issued in ≤ 30 minutes without training (KB §6.10)
  Traces         : POL-INV-003, POL-INV-018, POL-INV-021
  Source         : business-policies POL-INV-018 rationale "before opening stock is loaded"; KB:raw-idea.md §6.9 step 5 "import items from Excel", §6.10; dialogue INV-P12
  Status         : DRAFT

US-INV-013
  Title (en)     : Stock move history
  Title (ar)     : سجل حركات المخزون
  Story          : As a Warehouse user, I need to see every move of an item variant in a warehouse, with the document that caused it, so that I can explain any balance.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-002, POL-INV-001
  Source         : module-registry ENTITIES OWNED (Stock move) and DEPENDENCIES (`audit`: change history of stock documents); KB:raw-idea.md §5 #3
  Status         : DRAFT

US-INV-014
  Title (en)     : Count stock from a phone
  Title (ar)     : جرد المخزون من الهاتف المحمول
  Story          : As a Warehouse user, I need to count the stock on the shelf from my phone's web browser, so that I can count where the goods are, with no native app.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-012
  Source         : KB:raw-idea.md §6.7 "Mobile-first for selling, collecting, counting; responsive web, no separate app in v1", §5 #3 "counts"
  Status         : DRAFT

US-INV-015
  Title (en)     : Adjust stock with a reason
  Title (ar)     : تسوية المخزون مع ذكر السبب
  Story          : As a Warehouse user, I need to correct stock with a stated reason, such as a count difference, damage or the write-off of an expired lot, so that balances match what is physically there and every change is explained.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-002, POL-INV-012, POL-INV-022
  Source         : module-registry ENTITIES OWNED (Stock adjustment), LOOKUPS OWNED (`inv.adjustment-reason`), AUTO "a count difference posts through an adjustment document"; DECISION INV-P10 "leaves stock only through a stock adjustment"
  Status         : DRAFT

US-INV-016
  Title (en)     : Stock valued at weighted-average cost
  Title (ar)     : قيمة المخزون بالتكلفة المتوسطة المرجّحة
  Story          : As an Owner, I need my stock valued at the running weighted-average cost of each item variant across my business, so that I know what my stock is worth and what each sale earns, and the accounts follow without manual entries.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-021
  Source         : DECISION INV-P09; IAS 2 ¶25 (not live-verified); module-registry AUTO "stock moves … each carrying its unit cost", "valuation and adjustment postings reach the ledger through ACC"; KB:raw-idea.md §3 D7 (auto-posting)
  Status         : DRAFT

US-INV-017
  Title (en)     : Choose whether negative stock is allowed
  Title (ar)     : اختيار السماح بالرصيد السالب
  Story          : As an Owner, I need to decide whether my business lets stock go below zero, so that I can start selling before opening stock is loaded and keep strict balances once I want them.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-018
  Source         : DECISION INV-P08; KB:raw-idea.md §6.5, §6.10
  Status         : DRAFT

US-INV-018
  Title (en)     : Be alerted on negative stock
  Title (ar)     : تنبيه عند الرصيد السالب
  Story          : As a Warehouse user, I need to be told when an issue takes a warehouse balance below zero, so that I can put it right instead of discovering it later.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-019
  Source         : DECISION INV-P08 "default allowed with an alert to the Warehouse role"; module-registry DEPENDENCIES (`notif`: negative-stock alerts)
  Status         : DRAFT

US-INV-019
  Title (en)     : Offline POS sales are never lost
  Title (ar)     : قبول مبيعات نقطة البيع دون اتصال
  Story          : As a Seller/Cashier who sold while the point of sale was offline, I need those sales to be accepted when they synchronise, even when the stock they take is no longer there, so that real sales whose goods have already left are never lost.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-020
  Source         : DECISION INV-P08 "synced offline POS sales are always accepted"; KB:raw-idea.md §5 #6 "POS mode (offline-capable)"
  Status         : DRAFT

US-INV-020
  Title (en)     : Selling a bundle moves its component stock
  Title (ar)     : بيع الحزمة يحرّك مخزون مكوّناتها
  Story          : As a Seller/Cashier in a business that sells bundles, I need a bundle sale to take its component items out of stock, so that what a bundle has available always follows its components and no assembly step is needed.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-014
  Source         : DECISION INV-P06; KB:raw-idea.md §4.1 (`bundles`), §4.2 (bundles ✓ for electronics and household, both in the launch priority)
  Status         : DRAFT

US-INV-021
  Title (en)     : Record lot and expiry on receipt
  Title (ar)     : تسجيل التشغيلة والصلاحية عند الاستلام
  Story          : As a Warehouse user in a business that tracks lots, I need to record the lot and its expiry date when goods of a lot-tracked item arrive, so that I know which batch I hold and when it expires.
  Priority       : MEDIUM
  Success metric : —
  Traces         : POL-INV-006
  Source         : KB:raw-idea.md §4.1 (`lotExpiry`: "lot + expiry on receipt"), §4.2 (grocery "next" after the launch activities)
  Status         : DRAFT

US-INV-022
  Title (en)     : Issue the nearest expiry first
  Title (ar)     : صرف الأقرب انتهاءً أولًا
  Story          : As a Warehouse user in a business that tracks lots, I need the stock nearest to expiry to go out first, so that fewer goods expire on the shelf.
  Priority       : MEDIUM
  Success metric : —
  Traces         : POL-INV-007
  Source         : KB:raw-idea.md §4.1 (`lotExpiry`: "FEFO issue")
  Status         : DRAFT

US-INV-023
  Title (en)     : Know which stock is near expiry
  Title (ar)     : معرفة المخزون القريب من انتهاء الصلاحية
  Story          : As a Warehouse user or an Owner in a business that tracks lots, I need to see, and be told about, the stock approaching its expiry within a horizon my business chooses, so that I can act before it is lost.
  Priority       : MEDIUM
  Success metric : —
  Traces         : POL-INV-008
  Source         : KB:raw-idea.md §4.1 (`lotExpiry`: "near-expiry reports"); module-registry DEPENDENCIES (`cu`: near-expiry horizon; `notif`: near-expiry alerts to the Warehouse role)
  Status         : DRAFT

US-INV-024
  Title (en)     : Expired lots are never sold
  Title (ar)     : عدم بيع التشغيلات المنتهية
  Story          : As an Owner, I need a lot past its expiry date to be kept out of every sale while it stays visible in my stock until I write it off, so that I never sell expired goods and still account for them.
  Priority       : MEDIUM
  Success metric : —
  Traces         : POL-INV-022, POL-INV-008
  Source         : DECISION INV-P10 "stays in stock, visible in near-expiry reporting"; KB:raw-idea.md §4.1, §4.2
  Status         : DRAFT

US-INV-025
  Title (en)     : Hold stock in two quantities
  Title (ar)     : المخزون بالكمية المزدوجة
  Story          : As a Warehouse user stocking fabrics, building materials or goods sold by weight, I need the stock of those items held both as a count and as a weight or length, so that I know how many pieces and how much material I have.
  Priority       : LOW
  Success metric : —
  Traces         : POL-INV-009
  Source         : KB:raw-idea.md §4.1 (`dualQuantity`: "count + weight/length"), §4.2 (fabrics, building materials, produce; not in the launch priority)
  Status         : DRAFT

US-INV-026
  Title (en)     : Data is protected when a capability flag is switched off
  Title (ar)     : حماية البيانات عند إيقاف مفتاح القدرة
  Story          : As an Owner, I need to be stopped from switching off lot and expiry or dual quantity while my stock data still depends on it, and to be shown which data does, so that I never lose that history and always know my next step.
  Priority       : MEDIUM
  Success metric : —
  Traces         : POL-INV-010
  Source         : KB:raw-idea.md §6.2 "turning off is blocked when data depends on it", §6.5 "no dead ends"; DP §5 G3
  Status         : DRAFT

US-INV-027
  Title (en)     : Inventory follows capability flags, not the activity
  Title (ar)     : سلوك المخزون وفق مفاتيح القدرة لا النشاط
  Story          : As an Owner, I need inventory to behave according to the capabilities switched on for my business, not the activity I declared, so that I can turn capabilities on as my business changes without re-setup.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-011
  Source         : KB:raw-idea.md §3 D2 "Business logic reads capability flags only, never the activity name", §6.2, §7
  Status         : DRAFT

US-INV-028
  Title (en)     : A ready Warehouse role
  Title (ar)     : الدور المسبق «المستودع»
  Story          : As an Owner, I need a ready Warehouse role I can give to the people who run my stock, so that they reach the inventory functions without me designing roles.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INV-013
  Source         : KB:raw-idea.md §3 D8 "three presets (Owner, Seller/Cashier, Warehouse)"; module-registry DEPENDENCIES (`sec`: Warehouse role preset)
  Status         : DRAFT

## TRACEABILITY — story → policy
| US | Traces (POL) | Source |
|---|---|---|
| US-INV-001 | POL-INV-001 | KB §1, §2, §8 |
| US-INV-002 | POL-INV-001, POL-INV-002 | registry (Stock balance); INV-P05 |
| US-INV-003 | POL-INV-001 | registry (Reservation, AUTO); KB §5 #3 |
| US-INV-004 | POL-INV-003 | KB §6.8; registry AUTO |
| US-INV-005 | POL-INV-002 | KB §6.8; DP §5 G8 |
| US-INV-006 | POL-INV-004 | KB §6.3, §6.8 |
| US-INV-007 | POL-INV-005, POL-INV-004 | KB §6.8; registry (Warehouse) |
| US-INV-008 | POL-INV-002, POL-INV-004, POL-INV-005, POL-INV-021 | KB §5 #3, §6.8; INV-P09 |
| US-INV-009 | POL-INV-015 | INV-P07; KB §6.8 |
| US-INV-010 | POL-INV-016 | INV-P07; KB §6.3, §6.8 |
| US-INV-011 | POL-INV-017, POL-INV-016 | INV-P07; KB §6.8; registry (Branch) |
| US-INV-012 | POL-INV-003, POL-INV-018, POL-INV-021 | POL-INV-018 rationale; KB §6.9, §6.10; INV-P12 |
| US-INV-013 | POL-INV-002, POL-INV-001 | registry (Stock move, `audit`); KB §5 #3 |
| US-INV-014 | POL-INV-012 | KB §6.7, §5 #3 |
| US-INV-015 | POL-INV-002, POL-INV-012, POL-INV-022 | registry (Stock adjustment, `inv.adjustment-reason`); INV-P10 |
| US-INV-016 | POL-INV-021 | INV-P09; IAS 2 ¶25; registry AUTO; KB §3 D7 |
| US-INV-017 | POL-INV-018 | INV-P08; KB §6.5, §6.10 |
| US-INV-018 | POL-INV-019 | INV-P08; registry (`notif`) |
| US-INV-019 | POL-INV-020 | INV-P08; KB §5 #6 |
| US-INV-020 | POL-INV-014 | INV-P06; KB §4.1, §4.2 |
| US-INV-021 | POL-INV-006 | KB §4.1, §4.2 |
| US-INV-022 | POL-INV-007 | KB §4.1 |
| US-INV-023 | POL-INV-008 | KB §4.1; registry (`cu`, `notif`) |
| US-INV-024 | POL-INV-022, POL-INV-008 | INV-P10; KB §4.1, §4.2 |
| US-INV-025 | POL-INV-009 | KB §4.1, §4.2 |
| US-INV-026 | POL-INV-010 | KB §6.2, §6.5; DP §5 G3 |
| US-INV-027 | POL-INV-011 | KB §3 D2, §6.2, §7 |
| US-INV-028 | POL-INV-013 | KB §3 D8; registry (`sec`) |

Policy coverage: POL-INV-001 (US-INV-001, 002, 003, 013) · POL-INV-002 (005, 002, 008, 013, 015) · POL-INV-003 (004, 012) · POL-INV-004 (006, 007, 008) · POL-INV-005 (007, 008) · POL-INV-006 (021) · POL-INV-007 (022) · POL-INV-008 (023, 024) · POL-INV-009 (025) · POL-INV-010 (026) · POL-INV-011 (027) · POL-INV-012 (014, 015) · POL-INV-013 (028) · POL-INV-014 (020) · POL-INV-015 (009) · POL-INV-016 (010, 011) · POL-INV-017 (011) · POL-INV-018 (017, 012) · POL-INV-019 (018) · POL-INV-020 (019) · POL-INV-021 (016, 008, 012) · POL-INV-022 (024, 015). 22/22 policies covered.

## RESOLVED DECISIONS (dialogue)
| # | Question | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| INV-P11 | Story priorities | HIGH for flag-independent stories and `bundles` (Inventory is in delivery wave 1; bundles are required by launch activities); MEDIUM for `lotExpiry` stories and flag-off protection (grocery is "next"; a story covering two flags takes the higher level); LOW for `dualQuantity` (its activities are not in the launch priority). Priorities are derived from these sources, not client-stated | Converged in dialogue (round 2, claude:sonnet accepted with amendment); user confirms at the gate | KB §3 D2, D8; §4.2; §8 |
| INV-P12 | Opening stock as an INV story | Yes, as US-INV-012 at need level (quantities and cost, many items at once). P1 chooses the mechanism | Converged in dialogue (round 2, claude:sonnet accepted); user confirms at the gate | POL-INV-018 rationale; KB §6.9, §6.10; INV-P09 |
| INV-P13 | Success metrics | The KB §6.10 setup metric is named as the shared metric on the setup-path stories only (US-INV-004, US-INV-009, US-INV-012); every other story uses "—" | Converged in dialogue (round 2, claude:sonnet accepted with amendment); user confirms at the gate | KB §6.1, §6.10 |

The decisions inherited from P0 (INV-P05 … INV-P10) are recorded in module-registry-inv and business-policies-inv. The stories above restate them as needs.

## DEFERRED
| US | Reason | Activation trigger |
|---|---|---|
| — | None. The v1 exclusions (bundle assembly, native counting app, FIFO cost layers) are decided scope exceptions in business-policies-inv, not undecided stories | — |

## APPROVAL
Approved by : — (pending prd-approval)   Date : —
Once approved, no stage may raise a question; P1 onward self-resolve
per the ambiguity rule (shared/GOVERNANCE-CORE.md).
══════════════════════════════════════════════════════════════════

<<<END INPUT>>>

<<<INPUT: domain-profile>>>
# DOMAIN PROFILE — Retail & Wholesale Commerce SaaS on erp-core
══════════════════════════════════════════════════════════════════
Profile         : commerce (COMMERCE)
Version         : 1            (per shared/VERSIONING.md)
Last Updated    : 2026-10-22
Status          : FRESH — dialogue round 1 (claude:opus draft; proposals P-01…P-12 open in-dialogue, block 8)
Research        : 1 primary source cited (KB raw-idea.md); live web research unavailable this round — external rows are NO-RESEARCH (block 9)
Given           : erp-core 1.5.0 — manifest `deps/erp-core/core-manifest.yaml`, skill `.claude/skills/erp-core-usage@1.5.0`
Run module      : CAT (Catalog)
Input note      : the `raw-idea` input arrived MISSING. The profile's knowledge file `profiles/commerce/knowledge/raw-idea.md` (Platform Brief, owner Hesham Ezzat, approved 2026-10-21) is used as the raw idea and platform brief.
══════════════════════════════════════════════════════════════════

## 1. SCOPE

**In bounds** (stated in [KB:raw-idea.md §1, §3 D1, §5]):
- A multi-tenant SaaS for small and medium **retail and wholesale traders** in Egypt and the Gulf.
- Back-office: purchasing, inventory, sales, receivables/payables, accounting, tax & e-invoicing.
- Point of sale (offline-capable POS mode inside Sales).
- An integrated e-commerce **storefront**: one catalogue, one stock, one customer base.
- Many trading activities from one codebase: electronics & appliances, apparel, household goods, cosmetics, grocery, spare parts, building materials, and so on. Behaviour is driven by six capability flags and per-activity presets ([KB:raw-idea.md §4]).
- Deployment `erp.tenancy.mode=multi` by default, with `single` for dedicated or on-prem customers ([KB:raw-idea.md §3 D3]).

**Out of bounds** ([KB:raw-idea.md §3 D1, §9]):
- Pharmacies, restaurants, gold, vehicles, manufacturing, contracting and services, because they use different business models.
- Native mobile apps in v1 (responsive web only).
- Marketplace (multi-vendor in one storefront).
- Report designer.
- BPM workflow engine.
- AI assistant (deferred core module, "advanced" delivery tier [KB:raw-idea.md §8]).

## 2. PURPOSE

Mandatory e-invoicing (Saudi ZATCA phase-2 waves; Egypt's B2C e-receipt expansion from Sept 2025) forces every small trader to adopt compliant software. Wholesale and retail trade is the largest activity segment: 54.4% of Egypt's establishments and 30.7% of Saudi SMEs ([KB:raw-idea.md §2]). The platform gives these traders **one product for back-office + POS + storefront**. It is activity-aware and **feature-complete but simple**: every capability exists, nothing is forced on the user, and the UI shows only what is enabled ([KB:raw-idea.md §1, §2]).

## 3. RESPONSIBILITIES

As stated in [KB:raw-idea.md §5]. The platform owns:
1. **Catalog**: items, variants, media, categories, publish state.
2. **Pricing**: price lists, promotions, coupons, loyalty points.
3. **Inventory**: stock, reservations, transfers, counts, lot/expiry, dual quantity.
4. **Party**: customers (including CUSTOMER-realm users), suppliers, addresses, credit.
5. **Purchasing**: purchase order, goods receipt, supplier invoice.
6. **Sales & POS**: quotation, order, invoice, returns/exchange; offline-capable POS mode.
7. **Settlements**: collections, payments, AR/AP ageing, payment-gateway adapter.
8. **Tax & E-invoicing**: VAT, country adapters (ETA / ZATCA / Oman), tax returns.
9. **Accounting**: auto-posting rules, GL, cost centres, budgets, period close. Fully present but invisible until "accounting mode" ([KB:raw-idea.md §3 D7]).
10. **SerialWarranty**: serials, warranty cards, service returns (flag `serialTracking`).
11. **Approvals**: per-document-type approval steps (none / one step / role-based) on one screen ([KB:raw-idea.md §3 D6]).
12. **Shipping**: delivery status, carrier adapters.
13. **Storefront**: public catalogue/search, guest cart, checkout, customer area, tenant-by-host resolver ([KB:raw-idea.md §3 D4, D5]).
14. **Insights**: dashboards, 15–20 standard reports, saved filters.
15. **Activity capability flags & presets**: one `FeatureFlags` API, activity presets, 5-step setup wizard ([KB:raw-idea.md §4.1, §6.9]). Which module owns this is in-dialogue: P-02.

Capabilities the given `erp-core` 1.5.0 provides (tenancy, users/roles/customer auth, settings, lookups, files, numbering, events, audit, notifications, printing/reports, module entitlements) are **used, not owned** (block 4).

## 4. MAIN COMPONENTS

Module codes other than CAT are **PROPOSED** pending P-01. Bounded-context ids are PROPOSED pending P-01 (block 7.2).

| # | Component | Module code | Bounded context | Category (user-defined) | Core / extension | Notes |
|---|-----------|-------------|-----------------|--------------------------|------------------|-------|
| 1 | Catalog | CAT | catalog | Business — foundation | Core | Item, variants, media, categories, publish state. DAG #1, no business dependency. Variant attributes are platform-own unless a given provides them (P-03); item/variant model in P-04 |
| 2 | Pricing | PRC (PROPOSED) | pricing | Business | Core | Price lists, promotions, coupons, loyalty. Depends on Catalog. Loyalty is an advanced tier ([KB §8]) |
| 3 | Inventory | INV (PROPOSED) | inventory | Business | Core | Stock, reservations, transfers, counts, lot/expiry, dual quantity. Every stock move carries `warehouse_id` ([KB §6.8]). Lot/dual-quantity seams in P-03 |
| 4 | Party | PTY (PROPOSED) | party | Business — foundation | Core | Customers, suppliers, addresses, credit. Partial given hit: customer credentials/realm are `erp-core:modules.sec` (P-10) |
| 5 | Purchasing | PUR (PROPOSED) | purchasing | Business | Core | PO, goods receipt, supplier invoice |
| 6 | Sales & POS | SAL (PROPOSED) | sales | Business | Core | Quotation, order, invoice, returns/exchange; POS mode offline-capable |
| 7 | Settlements | STL (PROPOSED) | settlements | Business | Core | Collections, payments, AR/AP ageing, payment-gateway adapter. The "cash & bank seams" the brief assumes are not in the erp-core 1.5.0 manifest (P-03) |
| 8 | Tax & E-invoicing | TAX (PROPOSED) | tax | Business — compliance | Core | VAT, ETA/ZATCA/Oman adapters, tax returns. The "tax adapter" seam the brief assumes is not in the manifest (P-03) |
| 9 | Accounting | ACC (PROPOSED) | accounting | Business — finance | Core (hidden until accounting mode) | Auto-posting, GL, cost centres, budgets, period close |
| 10 | SerialWarranty | SRW (PROPOSED) | after-sales | Business | Extension — flag `serialTracking` (+ `warranty`) | Serials, warranty cards, service returns |
| 11 | Approvals | APR (PROPOSED) | approvals | Business — workflow | Extension — lightweight | No BPM engine (D6). Uses given events + notif |
| 12 | Shipping | SHP (PROPOSED) | shipping | Business — fulfilment | Core (carrier adapters advanced) | Delivery status, carrier adapters |
| 13 | Storefront | STF (PROPOSED) | storefront | Channel | Core | Public catalogue/search, guest cart, checkout, customer area. Partial given hit: tenant resolver feeds `erp-core:modules.tenant` TenantContext (P-11) |
| 14 | Insights | INS (PROPOSED) | insights | Analytics | Core | Dashboards, 15–20 standard reports, saved filters. Partial given hit: report/print engine is `erp-core:modules.report` |
| 15 | Activity flags & presets (FeatureFlags API, presets, setup wizard) | — owner in-dialogue (P-02) | — (P-02) | Platform — configuration | Core | Partial given hit: persisted through `erp-core:modules.cu` (SettingsApi). The flag/preset semantics are the platform's own |
| 16 | Tenancy, tenant context, branding, public app-config | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.tenant` (Tenant; TenantLookupApi, TenantProvisioningContributor, TenantPolicy). `GET /api/public/app-config` ([KB technical base]) |
| 17 | Users, roles, permissions, staff & customer auth, customer accounts, signup | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.sec` (User, Role, …; PermissionContributor, SecUserDirectoryApi). Role presets Owner / Seller-Cashier / Warehouse are configured on it (D8) |
| 18 | Settings / configuration / tenant overrides | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.cu` (AppConfiguration; SettingsApi) |
| 19 | Lookups / master data code lists | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.mdl` (LookupType, LookupValue; MdlLookupApi). Catalog's brand/UoM placement in P-05 |
| 20 | Files, images, public files, attachments | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.file` (FileDocument, FileCategory; FileImageStoreApi, FileDocumentLookupApi). Catalog media uses it (P-12) |
| 21 | Number series / document numbers | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.sequence` (NumberSeries; NumberSeriesApi) |
| 22 | Domain events / outbox | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.events` (DomainEventPublisher, OutboxPublisher, OutboxSubscriber) |
| 23 | Audit trail / change history | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.audit` (AuditEvent; AuditApi) |
| 24 | Notifications (email, in-app, templates) | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.notif` (NotificationDispatchApi, ChannelProvider, RecipientDirectory) |
| 25 | Reports, exports, printing (Jasper) | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.report` (PrintApi, ReportProvider, PrintTemplateSource) |
| 26 | Business-module catalogue & entitlements | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.module` (ModuleCatalogApi, ModuleEntitlementApi) |
| 27 | Base entity, audit columns, optimistic locking, validation, search, paging, idempotency, API envelope | — | — | Platform | EXISTING (given: erp-core) | `erp-core:modules.common` |

## 5. GOVERNING RULES

| # | Rule | Source |
|---|---|---|
| G1 | Business logic reads **capability flags only** (`variants`, `serialTracking`(+`warranty`), `lotExpiry`, `dualQuantity`, `bundles`, `b2bCredit`), never the activity name. An activity is a preset that sets flags. Enforced by ArchUnit/lint: `activity ==` or a preset read outside settings fails the build | [KB:raw-idea.md §3 D2, §4.1, §7] |
| G2 | Every flag is tested in both states. The UI shows fields, menus, reports and storefront features only when their flag is on | [KB:raw-idea.md §4.1, §6.3] |
| G3 | A flag may be turned **on** at any time. Turning it **off** is blocked while data depends on it. Changing presets never requires re-setup | [KB:raw-idea.md §6.2] |
| G4 | Business modules are tenant-agnostic. Tenancy is `multi` by default and `single` optional | [KB:raw-idea.md §3 D3]; given `erp-core:modules.tenant` |
| G5 | Module dependencies follow the fixed DAG of §5 and flow downward only. Cross-module calls go only through declared `crossmodule` APIs. Core is used only through manifest extension points | [KB:raw-idea.md §5 rules, §7]; [C:C-GIVEN-1] [C:C-GIVEN-2] |
| G6 | Nothing the given `erp-core` provides is rebuilt | [KB:raw-idea.md header]; [C:C-GIVEN-3] |
| G7 | Simplicity is measurable: ≤ 4 mandatory fields per screen. Setup plus first invoice takes ≤ 30 min without training; a new employee sells within ≤ 10 min | [KB:raw-idea.md §3 D9, §6.1, §6.10] |
| G8 | Multi-dimension defaults: one warehouse, branch, currency and price list to start. Every dimension exists in the data model from day one. A selector is hidden while its count is 1 and is revealed with its related actions and filters when a second one is added. No migration, no mode switch | [KB:raw-idea.md §6.8] |
| G9 | Guest checkout is allowed. The guest becomes a CUSTOMER-realm account after the first order | [KB:raw-idea.md §3 D5] |
| G10 | The storefront resolves the tenant by subdomain (custom domain optional). It feeds the core TenantContext and returns the default tenant in `single` mode | [KB:raw-idea.md §3 D4] |
| G11 | Approvals are a lightweight module (none / one step / role-based steps per document type). No BPM engine | [KB:raw-idea.md §3 D6] |
| G12 | Accounting is fully present but hidden by default, with auto-posting from documents | [KB:raw-idea.md §3 D7] |
| G13 | "Covers activity X" means: the preset exists, its conformance scenario (setup → receive → sell → return → collect → report) is green in CI, and every flag × activity matrix cell is traced | [KB:raw-idea.md §7] |
| G14 | Delivery is module-by-module in the fixed order of §8. A module is Done only with its reports, tests and conformance green | [KB:raw-idea.md §3 D10, §8] |
| G15 | E-invoicing compliance (ZATCA phase 2, Egypt ETA e-invoice/e-receipt, Oman) is a market driver and a Tax-module responsibility. Catalog-side obligations (item tax codes, Arabic names) are in-dialogue: P-08, P-09 | [KB:raw-idea.md §2, §5 #8]; external detail NO-RESEARCH (block 9) |

## 6. RELATIONSHIPS WITH OTHER DOMAINS

Business-module dependencies are taken from the fixed DAG [KB:raw-idea.md §5]. The HARD-FK / SOFT-READ kind for module-to-module edges is a recommendation (P-01 consequence) to be confirmed in-dialogue.

| This component | Depends on | Kind | Direction | Stated by |
|---|---|---|---|---|
| Catalog | erp-core file (public images) | SOFT-READ (file id via FileImageStoreApi) | erp-core:modules.file → Catalog | user [KB §5] |
| Catalog | erp-core mdl (lookups) | SOFT-READ (MdlLookupApi) | erp-core:modules.mdl → Catalog | user [KB §5] |
| Catalog | erp-core cu (settings / flags) | SOFT-READ (SettingsApi, via FeatureFlags) | erp-core:modules.cu → Catalog | user [KB §5, §4.1] |
| Pricing | Catalog | HARD-FK | Catalog → Pricing | user [KB §5] |
| Inventory | Catalog | HARD-FK | Catalog → Inventory | user [KB §5] |
| Inventory | erp-core sequence, events, audit | SOFT-READ (extension APIs) | erp-core → Inventory | user [KB §5] |
| Party | erp-core sec (customer realm), cu | SOFT-READ | erp-core → Party | user [KB §5] |
| Purchasing | Catalog, Inventory, Party | HARD-FK | owners → Purchasing | user [KB §5] |
| Sales & POS | Catalog, Pricing, Inventory, Party | HARD-FK | owners → Sales & POS | user [KB §5] |
| Settlements | Party, Purchasing, Sales & POS | HARD-FK | owners → Settlements | user [KB §5] |
| Tax & E-invoicing | Purchasing, Sales & POS | HARD-FK | owners → Tax | user [KB §5] |
| Accounting | Purchasing, Sales & POS, Settlements, Tax | SOFT-READ (posting from events) | owners → Accounting | user [KB §5, D7] |
| SerialWarranty | Inventory, Sales & POS | HARD-FK | owners → SerialWarranty | user [KB §5] |
| Approvals | any document module | SOFT-READ (events) | document modules → Approvals | user [KB §5, D6] |
| Shipping | Sales & POS; erp-core notif | HARD-FK / SOFT-READ | owners → Shipping | user [KB §5] |
| Storefront | Catalog, Pricing, Inventory, Party, Sales & POS, Settlements, Shipping; erp-core sec, file, tenant | SOFT-READ (crossmodule APIs) | owners → Storefront | user [KB §5, D4] |
| Insights | all modules; erp-core report | SOFT-READ | owners → Insights | user [KB §5] |
| Tax & E-invoicing | external tax authorities (Egypt ETA, Saudi ZATCA, Oman) | external integration (adapter) | Tax → authority | user [KB §5 #8] |
| Settlements | external payment gateways | external integration (adapter) | Settlements → gateway | user [KB §5 #7] |
| Shipping | external carriers | external integration (adapter) | Shipping → carrier | user [KB §5 #12] |

## 7. STEERING  (read verbatim by every later stage)

### 7.1 Ubiquitous language

The terms come from the brief [KB:raw-idea.md]. Terms marked † depend on an in-dialogue proposal and become binding when its DECISION lands.

| Term (en / ar) | Definition | Do not say | Module code |
|---|---|---|---|
| Item / صنف | A thing the trader buys, stocks or sells. It is the catalogue record that groups its variants | product (in code/UI labels), article, material, مادة, منتج (as entity name) | CAT |
| Variant / متغيّر الصنف † | A concrete sellable and stockable version of an Item (for example size × colour). It is the unit that stock, price and barcode attach to (P-04) | SKU (as entity name), child item, option, sub-item, صنف فرعي | CAT |
| Variant attribute / خاصية المتغيّر † | A dimension that variants differ by (size, colour, …) | option, property, characteristic | CAT |
| Category / فئة † | A node in the tenant's hierarchical item classification used for browsing and reporting (P-05) | group, family, department, مجموعة | CAT |
| Brand / علامة تجارية † | The maker or brand label of an item, held as a lookup (P-05) | manufacturer (unless distinct), make | CAT (via mdl) |
| Unit of measure / وحدة القياس † | The unit an item is counted, weighed or measured in (P-05) | UOM code, unit type | CAT (via mdl) |
| Barcode / باركود † | A scannable code (GTIN or internal) identifying a Variant (P-07) | item code, SKU | CAT |
| Bundle / حزمة | A composite item, kit or promo pack sold as one line and made of component items (flag `bundles`) | combo, package, composite (as entity name), طقم | CAT |
| Media / وسائط | The images and files attached to an item for display (P-12) | gallery, pictures, assets | CAT |
| Publish state / حالة النشر | Whether an item is visible in the storefront | status (unqualified), active flag, visibility | CAT |
| Capability flag / مفتاح القدرة | One of six switches (`variants`, `serialTracking`, `lotExpiry`, `dualQuantity`, `bundles`, `b2bCredit`) that business logic reads | feature toggle (in domain text), mode, activity type | P-02 |
| Activity / النشاط التجاري | The trading activity a tenant declares (for example apparel). It is never read by business logic | business type, sector, vertical (in code) | P-02 |
| Activity preset / القالب المسبق للنشاط | A named set of flag values and default settings applied when an activity is chosen | template, profile, package | P-02 |
| Dual quantity / الكمية المزدوجة | Counting an item in two quantities (count + weight or length), priced by either | secondary unit, catch weight (in UI) | INV (PROPOSED) |
| Lot / التشغيلة | A batch of stock received together, carrying an expiry date (flag `lotExpiry`) | batch (in UI), دفعة | INV (PROPOSED) |
| Serial number / الرقم التسلسلي | The unique identifier of one physical unit (flag `serialTracking`) | IMEI (except as a kind of serial), serial code | SRW (PROPOSED) |
| Price list / قائمة الأسعار | A set of variant prices for a channel or customer group | tariff, rate card | PRC (PROPOSED) |
| Warehouse / المستودع | A physical stock location. Present from day one, with the selector hidden while there is only one | store (ambiguous with storefront), depot, مخزن | INV (PROPOSED) |
| Customer / العميل | A party that buys. May be linked to a CUSTOMER-realm account | client, buyer, زبون | PTY (PROPOSED) |
| Supplier / المورّد | A party the trader buys from | vendor, provider | PTY (PROPOSED) |
| Guest / الزائر | A storefront shopper who checks out without an account. They become a Customer account after their first order | anonymous user, visitor account | STF (PROPOSED) |
| Storefront / المتجر الإلكتروني | The tenant's public online shop, resolved by subdomain or custom domain | webshop, e-shop, site | STF (PROPOSED) |
| Point of sale (POS) / نقطة البيع | The cashier selling mode of Sales & POS, offline-capable | till, checkout (reserved for storefront) | SAL (PROPOSED) |
| Tenant / المستأجر | One trader's isolated data space on the platform (given: erp-core) | company (as tenancy term), shop, account | — (given) |

### 7.2 Bounded contexts

The profile's own `bounded_contexts` list was not provided in this brief. The contexts below are PROPOSED with one context per business module (P-01).

| Context | Owns module codes | Boundary statement |
|---|---|---|
| catalog | CAT | What is sold: items, variants, attributes, categories, bundles, media, publish state. No price, no stock quantity |
| pricing | PRC | What it costs: price lists, promotions, coupons, loyalty |
| inventory | INV | Where it is and how much: stock, moves, reservations, transfers, counts, lots, dual quantity |
| party | PTY | Who: customers, suppliers, addresses, credit. Credentials belong to given sec |
| purchasing | PUR | Buying documents |
| sales | SAL | Selling documents and the POS mode |
| settlements | STL | Money in and out: collections, payments, ageing, gateway adapter |
| tax | TAX | Tax computation, e-invoice submission, returns |
| accounting | ACC | Ledger, posting rules, cost centres, budgets, close |
| after-sales | SRW | Serials, warranty, service returns |
| approvals | APR | Approval steps per document type |
| shipping | SHP | Delivery status and carriers |
| storefront | STF | Public online channel and host-to-tenant resolution |
| insights | INS | Dashboards, standard reports, saved filters |

### 7.3 Module prefixes proposal

| Code | Display (en / ar) | Status |
|---|---|---|
| CAT | Catalog / الكتالوج | IN PROFILE (module of this run) |
| PRC | Pricing / التسعير | PROPOSED (P-01) — carried into `P-1` as RESERVED |
| INV | Inventory / المخزون | PROPOSED (P-01) — RESERVED |
| PTY | Party / الأطراف | PROPOSED (P-01) — RESERVED |
| PUR | Purchasing / المشتريات | PROPOSED (P-01) — RESERVED |
| SAL | Sales & POS / المبيعات ونقطة البيع | PROPOSED (P-01) — RESERVED |
| STL | Settlements / التسويات | PROPOSED (P-01) — RESERVED |
| TAX | Tax & E-invoicing / الضرائب والفوترة الإلكترونية | PROPOSED (P-01) — RESERVED |
| ACC | Accounting / المحاسبة | PROPOSED (P-01) — RESERVED |
| SRW | SerialWarranty / الأرقام التسلسلية والضمان | PROPOSED (P-01) — RESERVED |
| APR | Approvals / الموافقات | PROPOSED (P-01) — RESERVED |
| SHP | Shipping / الشحن | PROPOSED (P-01) — RESERVED |
| STF | Storefront / المتجر الإلكتروني | PROPOSED (P-01) — RESERVED |
| INS | Insights / التحليلات | PROPOSED (P-01) — RESERVED |

No code is proposed for the given rows (block 4 rows 16–27), because they are used, not built. A code for the activity-flags owner depends on P-02.

### 7.4 Identifier rules

Later stages build IDs as `{prefix}-{MOD}-{seq}` (seq width 3) using the module codes above. Entity kinds are `master, transactional, lookup, config, security`. This profile adds no domain-specific atom (`profile.ids.atoms`). Entities provided by erp-core (block 4 given rows) receive no entity ID from this platform [C:C-GIVEN-3].

### 7.5 Knowledge sources to cite

- `profiles/commerce/knowledge/raw-idea.md`
- No external research source has been accepted yet (web research unavailable in round 1; block 9).

## 8. RESOLVED DECISIONS

These are round-1 proposals, recommended by claude:opus. Round 2 (claude:sonnet) challenges them, and the user confirms. None is binding until its DECISION lands.

| # | Point | Decision | Recommended by dialogue? | Confirmed by user | Sources |
|---|---|---|---|---|---|
| P-01 | Module codes and bounded contexts for the 14 business modules | Rec.: codes in 7.3, one context per module (7.2) | Round 1 recommendation | pending | KB §5 |
| P-02 | Owner of FeatureFlags API, activity presets, setup wizard | Rec.: a small platform foundation module (code proposed in round 2, e.g. `SET`) built on given `cu` | Round 1 recommendation | pending | KB §4.1, §6.2, §6.9, §7; NO-RESEARCH |
| P-03 | Core seams the brief assumes but erp-core 1.5.0's manifest does not list (custom attributes, `lot_id`, dual-quantity columns, cash & bank, tax adapter) | Rec.: treat as platform-own in CAT / INV / STL / TAX. Re-match if a later erp-core manifest provides them | Round 1 recommendation | pending | KB §4.1, §5; manifest table §1.5 |
| P-04 | Item / Variant model | Rec.: every Item has ≥ 1 Variant (a default one when `variants` is off). Stock, price, barcode and tax code key on the Variant | Round 1 recommendation | pending | KB §4.1; NO-RESEARCH |
| P-05 | Categories, brands, units of measure | Rec.: Category is a CAT-owned hierarchy. Brand and UoM are given `mdl` lookup types | Round 1 recommendation | pending | KB §5; manifest `mdl`; NO-RESEARCH |
| P-06 | Bundle ownership | Rec.: CAT defines the bundle composition. INV/SAL explode it to components at movement/sale | Round 1 recommendation | pending | KB §4.1; NO-RESEARCH |
| P-07 | Barcodes and weighed-item codes | Rec.: a Variant carries 0..n barcodes (GTIN or internal). Weighed and variable-measure items use an internal price/weight-embedded code under `dualQuantity` | Round 1 recommendation | pending | KB §4.1; NO-RESEARCH |
| P-08 | Tax item classification on the catalogue | Rec.: a Variant carries the e-invoice item classification code (ETA GS1/EGS) and a tax-category reference owned by TAX | Round 1 recommendation | pending | KB §2, §5 #8; NO-RESEARCH |
| P-09 | Item name languages | Rec.: names are bilingual ar/en, with Arabic mandatory and English optional | Round 1 recommendation | pending | KB §1; NO-RESEARCH |
| P-10 | Customer identity split | Rec.: given `sec` owns credentials and the CUSTOMER realm. PTY owns the commercial customer and links to the user by SOFT-READ | Round 1 recommendation | pending | KB §3 D5, §5 #4; manifest `sec` |
| P-11 | Storefront tenant resolver | Rec.: the STF-owned host→tenant resolver feeds given `tenant` context (partial given hit) | Round 1 recommendation | pending | KB §3 D4; manifest `tenant` |
| P-12 | Catalog media | Rec.: binaries live in given `file` (public image store). CAT owns ordering, primary image and alt text per Item/Variant | Round 1 recommendation | pending | KB §5 #1; manifest `file` |

## 9. RESEARCH LOG

```yaml name=research-log
rows:
  - {point: "Platform scope, modules, fixed decisions", finding: "Approved brief fixes retail & wholesale multi-activity scope, 14-module DAG, decisions D1-D10, six capability flags and per-activity presets", source: {title: "Platform Brief — Retail & Wholesale Commerce SaaS on erp-core", url_or_path: "profiles/commerce/knowledge/raw-idea.md", date: "2026-10-21"}, used_in: "1, 2, 3, 4, 5, 6, 7"}
  - {point: "Market drivers", finding: "Trade is 54.4% of Egypt's establishments and 30.7% of Saudi SMEs; mandatory e-invoicing (ZATCA phase 2, Egypt B2C e-receipt from Sept 2025) drives adoption", source: {title: "Platform Brief §2", url_or_path: "profiles/commerce/knowledge/raw-idea.md", date: "2026-10-21"}, used_in: "2, 5 G15"}
  - {point: "Given capability match (P-03, P-05, P-10, P-11, P-12)", finding: "erp-core 1.5.0 provides tenant, sec, cu, mdl, file, sequence, events, audit, notif, report, module, common; it lists no custom-attributes, lot, dual-quantity, cash & bank or tax-adapter capability", source: {title: "erp-core 1.5.0 core manifest (as embedded in the domain-profile engine §1.5)", url_or_path: "deps/erp-core/core-manifest.yaml", date: "2026-10-22"}, used_in: "4, 6, 8"}
  - {point: "Item / Variant decomposition in established commerce systems (P-04)", finding: "Unverified (web research denied this round): mature platforms are believed to model a parent product with sellable variants keyed by SKU, with a default variant when there are no options", source: NO-RESEARCH, used_in: "8 P-04"}
  - {point: "Egypt ETA item classification codes (P-08)", finding: "Unverified: ETA e-invoice lines are believed to require a GS1 or EGS item code per item", source: NO-RESEARCH, used_in: "8 P-08"}
  - {point: "ZATCA invoice language (P-09)", finding: "Unverified: Saudi tax invoices are believed to require Arabic, with other languages allowed alongside", source: NO-RESEARCH, used_in: "8 P-09"}
  - {point: "Barcode standards for variable-measure items (P-07)", finding: "Unverified: GS1 is believed to reserve restricted-circulation prefixes for in-store weighed/priced items", source: NO-RESEARCH, used_in: "8 P-07"}
  - {point: "Bundle / kit ownership patterns (P-06)", finding: "Unverified: back-office systems differ between catalogue-level bundles and inventory-level kits", source: NO-RESEARCH, used_in: "8 P-06"}
  - {point: "Feature-flag / preset ownership patterns (P-02)", finding: "Unverified: no external comparison gathered", source: NO-RESEARCH, used_in: "8 P-02"}
```

## 10. OPEN ITEMS

None outside the dialogue. Proposals P-01…P-12 (block 8) are in-dialogue and close by DECISION in later rounds. Nothing is deferred to an open-questions file.
══════════════════════════════════════════════════════════════════

<<<END INPUT>>>

<<<INPUT: project-registry>>>
# PROJECT REGISTRY — Retail & Wholesale Commerce SaaS on erp-core
══════════════════════════════════════════════════════════════════
Profile            : commerce
Registry Version   : 1.0.0
Domain Profile     : analysis/domain/domain-profile.md v1
Last Updated       : 2026-10-26 by P-1
Modules registered : 14   Entity candidates : 58   Open items : 17
══════════════════════════════════════════════════════════════════

Module count note: 14 business modules are registered. The index also lists 1 platform component whose owner is OPEN (row 15) and 12 capabilities that erp-core already provides (rows 16–27, EXISTING). These 13 extra rows are not counted as registered modules.

## SCHEMA COMPLIANCE MAP

```yaml name=compliance-map
categories:
  CAT-1: "1. Identity & versioning"
  CAT-2: "2. Conventions & steering"
  CAT-3: "3. Module / component index"
  CAT-4: "4. Entity ownership"
  CAT-5: "5. Shared entity declarations"
  CAT-6: "6. Structural / implementation registry"
  CAT-7: "7. Cross-module dependency index"
  CAT-8: "8. Open question index"
  CAT-9: "9. Pipeline / progress status"
  CAT-10: "10. Change / event history"
uncovered: []
```

## 1. Identity & versioning

| Field | Value | Source |
|---|---|---|
| Platform | Retail & Wholesale Commerce SaaS on erp-core | DP header; KB:raw-idea.md title |
| Profile | commerce (COMMERCE) | DP header |
| Owner of the brief | Hesham Ezzat (brief approved 2026-10-21) | KB:raw-idea.md header |
| Given | erp-core. The domain-profile v1 cites 1.5.0. The change-manifest for CAT v2 records an upgrade to 1.6.0, so the domain-profile has not yet been re-run against 1.6.0 (see OI-02) | DP header; change-manifest CAT v2 |
| Tenancy | `erp.tenancy.mode=multi` by default; `single` for dedicated or on-prem customers | DP §1, §5 G4; KB §3 D3 |
| Language policy | Narrative in `en`; names and terms in `ar, en` | profile |
| Technical base | Spring Boot (Java 25) on erp-core · React (one build, reads `GET /api/public/app-config`) · PostgreSQL 16 (Flyway, app migrations `V1000+`) · Jasper via core `PrintApi` | KB:raw-idea.md header |

**Version history**

| Registry version | Date | Stage | Change | Source |
|---|---|---|---|---|
| 1.0.0 | 2026-10-26 | P-1 | Bootstrap from domain-profile v1. No baseline registry was supplied to this run (OI-03) | DP v1; change-manifest CAT v2 |

## 2. Conventions & steering

### 2.1 Steering — verbatim copy of domain-profile §7

### 7.1 Ubiquitous language

The terms come from the brief [KB:raw-idea.md]. Terms marked † depend on an in-dialogue proposal and become binding when its DECISION lands.

| Term (en / ar) | Definition | Do not say | Module code |
|---|---|---|---|
| Item / صنف | A thing the trader buys, stocks or sells. It is the catalogue record that groups its variants | product (in code/UI labels), article, material, مادة, منتج (as entity name) | CAT |
| Variant / متغيّر الصنف † | A concrete sellable and stockable version of an Item (for example size × colour). It is the unit that stock, price and barcode attach to (P-04) | SKU (as entity name), child item, option, sub-item, صنف فرعي | CAT |
| Variant attribute / خاصية المتغيّر † | A dimension that variants differ by (size, colour, …) | option, property, characteristic | CAT |
| Category / فئة † | A node in the tenant's hierarchical item classification used for browsing and reporting (P-05) | group, family, department, مجموعة | CAT |
| Brand / علامة تجارية † | The maker or brand label of an item, held as a lookup (P-05) | manufacturer (unless distinct), make | CAT (via mdl) |
| Unit of measure / وحدة القياس † | The unit an item is counted, weighed or measured in (P-05) | UOM code, unit type | CAT (via mdl) |
| Barcode / باركود † | A scannable code (GTIN or internal) identifying a Variant (P-07) | item code, SKU | CAT |
| Bundle / حزمة | A composite item, kit or promo pack sold as one line and made of component items (flag `bundles`) | combo, package, composite (as entity name), طقم | CAT |
| Media / وسائط | The images and files attached to an item for display (P-12) | gallery, pictures, assets | CAT |
| Publish state / حالة النشر | Whether an item is visible in the storefront | status (unqualified), active flag, visibility | CAT |
| Capability flag / مفتاح القدرة | One of six switches (`variants`, `serialTracking`, `lotExpiry`, `dualQuantity`, `bundles`, `b2bCredit`) that business logic reads | feature toggle (in domain text), mode, activity type | P-02 |
| Activity / النشاط التجاري | The trading activity a tenant declares (for example apparel). It is never read by business logic | business type, sector, vertical (in code) | P-02 |
| Activity preset / القالب المسبق للنشاط | A named set of flag values and default settings applied when an activity is chosen | template, profile, package | P-02 |
| Dual quantity / الكمية المزدوجة | Counting an item in two quantities (count + weight or length), priced by either | secondary unit, catch weight (in UI) | INV (PROPOSED) |
| Lot / التشغيلة | A batch of stock received together, carrying an expiry date (flag `lotExpiry`) | batch (in UI), دفعة | INV (PROPOSED) |
| Serial number / الرقم التسلسلي | The unique identifier of one physical unit (flag `serialTracking`) | IMEI (except as a kind of serial), serial code | SRW (PROPOSED) |
| Price list / قائمة الأسعار | A set of variant prices for a channel or customer group | tariff, rate card | PRC (PROPOSED) |
| Warehouse / المستودع | A physical stock location. Present from day one, with the selector hidden while there is only one | store (ambiguous with storefront), depot, مخزن | INV (PROPOSED) |
| Customer / العميل | A party that buys. May be linked to a CUSTOMER-realm account | client, buyer, زبون | PTY (PROPOSED) |
| Supplier / المورّد | A party the trader buys from | vendor, provider | PTY (PROPOSED) |
| Guest / الزائر | A storefront shopper who checks out without an account. They become a Customer account after their first order | anonymous user, visitor account | STF (PROPOSED) |
| Storefront / المتجر الإلكتروني | The tenant's public online shop, resolved by subdomain or custom domain | webshop, e-shop, site | STF (PROPOSED) |
| Point of sale (POS) / نقطة البيع | The cashier selling mode of Sales & POS, offline-capable | till, checkout (reserved for storefront) | SAL (PROPOSED) |
| Tenant / المستأجر | One trader's isolated data space on the platform (given: erp-core) | company (as tenancy term), shop, account | — (given) |

### 7.2 Bounded contexts

The profile's own `bounded_contexts` list was not provided in this brief. The contexts below are PROPOSED with one context per business module (P-01).

| Context | Owns module codes | Boundary statement |
|---|---|---|
| catalog | CAT | What is sold: items, variants, attributes, categories, bundles, media, publish state. No price, no stock quantity |
| pricing | PRC | What it costs: price lists, promotions, coupons, loyalty |
| inventory | INV | Where it is and how much: stock, moves, reservations, transfers, counts, lots, dual quantity |
| party | PTY | Who: customers, suppliers, addresses, credit. Credentials belong to given sec |
| purchasing | PUR | Buying documents |
| sales | SAL | Selling documents and the POS mode |
| settlements | STL | Money in and out: collections, payments, ageing, gateway adapter |
| tax | TAX | Tax computation, e-invoice submission, returns |
| accounting | ACC | Ledger, posting rules, cost centres, budgets, close |
| after-sales | SRW | Serials, warranty, service returns |
| approvals | APR | Approval steps per document type |
| shipping | SHP | Delivery status and carriers |
| storefront | STF | Public online channel and host-to-tenant resolution |
| insights | INS | Dashboards, standard reports, saved filters |

### 7.3 Module prefixes proposal

| Code | Display (en / ar) | Status |
|---|---|---|
| CAT | Catalog / الكتالوج | IN PROFILE (module of this run) |
| PRC | Pricing / التسعير | PROPOSED (P-01) — carried into `P-1` as RESERVED |
| INV | Inventory / المخزون | PROPOSED (P-01) — RESERVED |
| PTY | Party / الأطراف | PROPOSED (P-01) — RESERVED |
| PUR | Purchasing / المشتريات | PROPOSED (P-01) — RESERVED |
| SAL | Sales & POS / المبيعات ونقطة البيع | PROPOSED (P-01) — RESERVED |
| STL | Settlements / التسويات | PROPOSED (P-01) — RESERVED |
| TAX | Tax & E-invoicing / الضرائب والفوترة الإلكترونية | PROPOSED (P-01) — RESERVED |
| ACC | Accounting / المحاسبة | PROPOSED (P-01) — RESERVED |
| SRW | SerialWarranty / الأرقام التسلسلية والضمان | PROPOSED (P-01) — RESERVED |
| APR | Approvals / الموافقات | PROPOSED (P-01) — RESERVED |
| SHP | Shipping / الشحن | PROPOSED (P-01) — RESERVED |
| STF | Storefront / المتجر الإلكتروني | PROPOSED (P-01) — RESERVED |
| INS | Insights / التحليلات | PROPOSED (P-01) — RESERVED |

No code is proposed for the given rows (block 4 rows 16–27), because they are used, not built. A code for the activity-flags owner depends on P-02.

### 7.4 Identifier rules

Later stages build IDs as `{prefix}-{MOD}-{seq}` (seq width 3) using the module codes above. Entity kinds are `master, transactional, lookup, config, security`. This profile adds no domain-specific atom (`profile.ids.atoms`). Entities provided by erp-core (block 4 given rows) receive no entity ID from this platform [C:C-GIVEN-3].

### 7.5 Knowledge sources to cite

- `profiles/commerce/knowledge/raw-idea.md`
- No external research source has been accepted yet (web research unavailable in round 1; block 9).

### 2.2 Module codes compared with the profile record

`profile.vocabulary.module_prefixes` is the durable record of module codes. It holds CAT, PRC, INV, PTY, PUR, SLS, STL, TAX, ACC, SWR, APR, SHP, SFR, INS. Compared with §7.3 above:

| Module | Profile code | Domain-profile code | Registry status | Note | Source |
|---|---|---|---|---|---|
| Catalog | CAT | CAT | IN PROFILE | Codes agree | profile; DP §7.3 |
| Pricing | PRC | PRC | IN PROFILE | DP marks it PROPOSED, but the code is already in the profile | profile; DP §7.3 |
| Inventory | INV | INV | IN PROFILE | Same as Pricing | profile; DP §7.3 |
| Party | PTY | PTY | IN PROFILE | Same as Pricing | profile; DP §7.3 |
| Purchasing | PUR | PUR | IN PROFILE | Same as Pricing | profile; DP §7.3 |
| Sales & POS | SLS | SAL | SLS IN PROFILE · SAL RESERVED | SAL is not yet in profile.vocabulary.module_prefixes. Codes conflict (OI-01) | profile; DP §4 #6, §7.3 |
| Settlements | STL | STL | IN PROFILE | Same as Pricing | profile; DP §7.3 |
| Tax & E-invoicing | TAX | TAX | IN PROFILE | Same as Pricing | profile; DP §7.3 |
| Accounting | ACC | ACC | IN PROFILE | Same as Pricing | profile; DP §7.3 |
| SerialWarranty | SWR | SRW | SWR IN PROFILE · SRW RESERVED | SRW is not yet in profile.vocabulary.module_prefixes. Codes conflict (OI-01) | profile; DP §4 #10, §7.3 |
| Approvals | APR | APR | IN PROFILE | Same as Pricing | profile; DP §7.3 |
| Shipping | SHP | SHP | IN PROFILE | Same as Pricing | profile; DP §7.3 |
| Storefront | SFR | STF | SFR IN PROFILE · STF RESERVED | STF is not yet in profile.vocabulary.module_prefixes. Codes conflict (OI-01) | profile; DP §4 #13, §7.3 |
| Insights | INS | INS | IN PROFILE | Same as Pricing | profile; DP §7.3 |

### 2.3 Enforcement notes

- **E1.** Every later artifact uses the terms of §7.1 verbatim. A synonym listed under "do not say" is a consistency finding at the pass gate (`gov.py analyze` checks that the registry and each artifact agree). Terms marked † become binding when their proposal's DECISION lands.
- **E2.** IDs follow `{prefix}-{MOD}-{seq}` (seq width 3) and use only the module codes of this section, i.e. the IN PROFILE codes of §2.2. The RESERVED aliases SAL, SRW and STF are not used to mint IDs while OI-01 is OPEN. No ID is minted for an activity-flags owner until P-02 (OI-05) gives it a code.
- **E3.** Entities are classified with the kinds `master, transactional, lookup, config, security`. Entities provided by erp-core receive no entity ID from this platform [C:C-GIVEN-3].
- **E4.** When a stage resolves an ambiguity, it cites the knowledge sources of §7.5 first (`profiles/commerce/knowledge/raw-idea.md`), then the domain-profile itself.
- **E5.** The pipeline status of each module (§9) is maintained by the orchestrator from commits. This engine seeds the rows as NOT STARTED, except where an input records progress.

### 2.4 Governance decisions log (confirmed only)

The domain-profile §8 proposals P-01…P-12 are still pending, so none of them appear here; they are tracked in §8. The rows below are user-fixed decisions from the approved brief, as carried by the domain-profile.

| Ref | Decision | Rationale | Source |
|---|---|---|---|
| D1 | Scope is retail & wholesale, multi-activity. Pharmacies, restaurants, gold, vehicles, manufacturing, contracting and services are out of scope. Native apps, marketplace, report designer, BPM engine and AI assistant are excluded from v1 | These activities use different business models | DP §1; KB §3 D1, §9 |
| D8 | Three role presets (Owner, Seller/Cashier, Warehouse) plus custom roles, configured on given `erp-core:modules.sec` | Fixed decision | DP §4 #17; KB §3 D8 |
| G1 | Business logic reads capability flags only, never the activity name. An activity is a preset that sets flags. Enforced by ArchUnit/lint | One codebase serves all activities | DP §5 G1; KB §3 D2, §4.1, §7 |
| G2 | Every flag is tested in both states. The UI shows a flagged feature only when its flag is on | No dead menus | DP §5 G2; KB §4.1, §6.3 |
| G3 | A flag may be turned on at any time. Turning it off is blocked while data depends on it. Changing presets never requires re-setup | Presets first, then customise | DP §5 G3; KB §6.2 |
| G4 | Business modules are tenant-agnostic. Tenancy is `multi` by default and `single` optional | Core rule | DP §5 G4; KB §3 D3 |
| G5 | Fixed downward-only DAG. Modules call each other only through declared `crossmodule` APIs. Core is used only through manifest extension points | Prevents ad-hoc inter-module design | DP §5 G5; KB §5, §7 |
| G6 | Nothing the given erp-core provides is rebuilt | Given-first | DP §5 G6; KB header |
| G7 | At most 4 mandatory fields per screen. Setup plus the first invoice takes at most 30 min; a new employee sells within 10 min | Simplicity is measurable | DP §5 G7; KB §3 D9, §6.1, §6.10 |
| G8 | Defaults are one warehouse, branch, currency and price list. Every dimension is in the model from day one. A selector stays hidden while its count is 1 | No migration, no mode switch | DP §5 G8; KB §6.8 |
| G9 | Guest checkout is allowed. The guest becomes a CUSTOMER-realm account after the first order | Fixed decision | DP §5 G9; KB §3 D5 |
| G10 | The storefront resolves the tenant by subdomain (custom domain optional) and feeds the core TenantContext. In `single` mode it returns the default tenant | Fixed decision | DP §5 G10; KB §3 D4 |
| G11 | Approvals are lightweight (none / one step / role-based per document type). No BPM engine | Fixed decision | DP §5 G11; KB §3 D6 |
| G12 | Accounting is fully present but hidden by default, with auto-posting from documents | Fixed decision | DP §5 G12; KB §3 D7 |
| G13 | "Covers activity X" means: the preset exists, its conformance scenario is green in CI, and every flag × activity cell is traced | Coverage guarantee | DP §5 G13; KB §7 |
| G14 | Delivery is module by module in the fixed order. A module is Done only when its reports, tests and conformance are green | Fixed decision | DP §5 G14; KB §3 D10, §8 |
| G15 | E-invoicing compliance (ZATCA phase 2, Egypt ETA, Oman) is a Tax-module responsibility. The Catalog-side obligations are OPEN (OI-11, OI-12) | Market driver | DP §5 G15; KB §2, §5 #8 |

## 3. Module / component index

Bounded contexts are PROPOSED pending P-01 (OI-04). Codes follow §2.2.

| # | Code | Display (en / ar) | Bounded context | Category | Core / extension | Status | Source |
|---|---|---|---|---|---|---|---|
| 1 | CAT | Catalog / الكتالوج | catalog | Business — foundation | Core | RESERVED; pipeline started (§9) | DP §4 #1, §7.3; profile |
| 2 | PRC | Pricing / التسعير | pricing | Business | Core (loyalty is an advanced tier) | RESERVED | DP §4 #2; profile; KB §8 |
| 3 | INV | Inventory / المخزون | inventory | Business | Core | RESERVED | DP §4 #3; profile |
| 4 | PTY | Party / الأطراف | party | Business — foundation | Core | RESERVED | DP §4 #4; profile |
| 5 | PUR | Purchasing / المشتريات | purchasing | Business | Core | RESERVED | DP §4 #5; profile |
| 6 | SLS (alias SAL RESERVED, OI-01) | Sales & POS / المبيعات ونقطة البيع | sales | Business | Core | RESERVED | DP §4 #6; profile |
| 7 | STL | Settlements / التسويات | settlements | Business | Core | RESERVED | DP §4 #7; profile |
| 8 | TAX | Tax & E-invoicing / الضرائب والفوترة الإلكترونية | tax | Business — compliance | Core | RESERVED | DP §4 #8; profile |
| 9 | ACC | Accounting / المحاسبة | accounting | Business — finance | Core (hidden until accounting mode) | RESERVED | DP §4 #9; profile; KB §3 D7 |
| 10 | SWR (alias SRW RESERVED, OI-01) | SerialWarranty / الأرقام التسلسلية والضمان | after-sales | Business | Extension — flag `serialTracking` (+ `warranty`) | RESERVED | DP §4 #10; profile |
| 11 | APR | Approvals / الموافقات | approvals | Business — workflow | Extension — lightweight | RESERVED | DP §4 #11; profile; KB §3 D6 |
| 12 | SHP | Shipping / الشحن | shipping | Business — fulfilment | Core (carrier adapters are advanced) | RESERVED | DP §4 #12; profile |
| 13 | SFR (alias STF RESERVED, OI-01) | Storefront / المتجر الإلكتروني | storefront | Channel | Core | RESERVED | DP §4 #13; profile |
| 14 | INS | Insights / التحليلات | insights | Analytics | Core | RESERVED | DP §4 #14; profile |
| 15 | — (owner OPEN, P-02) | Activity flags & presets (FeatureFlags API, presets, setup wizard) / مفاتيح القدرة والقوالب المسبقة | — (P-02) | Platform — configuration | Core | OPEN (OI-05) | DP §3 #15, §4 #15; KB §4.1, §6.9 |
| 16 | — | Tenancy, tenant context, branding, public app-config / المستأجرون | — | Platform | EXISTING (given: `erp-core:modules.tenant`) | EXISTING | DP §4 #16 |
| 17 | — | Users, roles, permissions, staff & customer auth / المستخدمون والصلاحيات | — | Platform | EXISTING (given: `erp-core:modules.sec`) | EXISTING | DP §4 #17 |
| 18 | — | Settings / configuration / tenant overrides / الإعدادات | — | Platform | EXISTING (given: `erp-core:modules.cu`) | EXISTING | DP §4 #18 |
| 19 | — | Lookups / master-data code lists / القوائم المرجعية | — | Platform | EXISTING (given: `erp-core:modules.mdl`) | EXISTING | DP §4 #19 |
| 20 | — | Files, images, public files, attachments / الملفات | — | Platform | EXISTING (given: `erp-core:modules.file`) | EXISTING | DP §4 #20 |
| 21 | — | Number series / document numbers / تسلسل الأرقام | — | Platform | EXISTING (given: `erp-core:modules.sequence`) | EXISTING | DP §4 #21 |
| 22 | — | Domain events / outbox / الأحداث | — | Platform | EXISTING (given: `erp-core:modules.events`) | EXISTING | DP §4 #22 |
| 23 | — | Audit trail / change history / سجل التدقيق | — | Platform | EXISTING (given: `erp-core:modules.audit`) | EXISTING | DP §4 #23 |
| 24 | — | Notifications / الإشعارات | — | Platform | EXISTING (given: `erp-core:modules.notif`) | EXISTING | DP §4 #24 |
| 25 | — | Reports, exports, printing (Jasper) / التقارير والطباعة | — | Platform | EXISTING (given: `erp-core:modules.report`) | EXISTING | DP §4 #25 |
| 26 | — | Business-module catalogue & entitlements / كتالوج الوحدات والاستحقاقات | — | Platform | EXISTING (given: `erp-core:modules.module`) | EXISTING | DP §4 #26 |
| 27 | — | Base entity, audit columns, locking, validation, search, paging, idempotency, API envelope / الأساس المشترك | — | Platform | EXISTING (given: `erp-core:modules.common`) | EXISTING | DP §4 #27 |

The given rows are matched against erp-core 1.5.0, as cited by the domain-profile. Re-matching them against 1.6.0 is OI-02.

## 4. Entity ownership

`CAND-*` refs are registry-local handles, not pipeline IDs. Every kind is a candidate classification; "(inferred)" in the Source column marks a row the inputs imply but do not name.

| Candidate ref | Entity name (en / ar) | Owner module | Kind | PRIVATE/SHARED | Status | Source |
|---|---|---|---|---|---|---|
| CAND-CAT-001 | Item / صنف | CAT | master | SHARED | CANDIDATE | DP §3 #1, §7.1; KB §5 #1 |
| CAND-CAT-002 | Variant / متغيّر الصنف | CAT | master | SHARED? | OPEN (OI-07) | DP §3 #1, §7.1, §8 P-04 |
| CAND-CAT-003 | Variant attribute / خاصية المتغيّر | CAT | lookup | PRIVATE | OPEN (OI-06) | DP §4 #1, §7.1, §8 P-03; KB §4.1 |
| CAND-CAT-004 | Category / فئة | CAT | master | SHARED? | CANDIDATE | DP §3 #1, §7.1; KB §5 #1; placement P-05 (OI-08) |
| CAND-CAT-005 | Bundle / حزمة (composition) | CAT | master | SHARED? | OPEN (OI-09) | DP §7.1, §8 P-06; KB §4.1 |
| CAND-CAT-006 | Media / وسائط (ordering, primary image, alt text) | CAT | master | PRIVATE | CANDIDATE (binaries stay in given `file`, OI-15) | DP §3 #1, §4 #20, §8 P-12; KB §5 #1 |
| CAND-CAT-007 | Barcode / باركود | CAT | master | SHARED? | OPEN (OI-10) | DP §7.1, §8 P-07 |
| CAND-CAT-008 | Brand / علامة تجارية | CAT | lookup | SHARED? | OPEN (OI-08) | DP §7.1, §8 P-05 |
| CAND-CAT-009 | Unit of measure / وحدة القياس | CAT | lookup | SHARED? | OPEN (OI-08) | DP §7.1, §8 P-05 |
| CAND-OPEN-001 | Brand / علامة تجارية, as a given lookup type | given `erp-core:modules.mdl` | lookup | SHARED? | OPEN (OI-08); no entity ID if given | DP §4 #19, §8 P-05 |
| CAND-OPEN-002 | Unit of measure / وحدة القياس, as a given lookup type | given `erp-core:modules.mdl` | lookup | SHARED? | OPEN (OI-08); no entity ID if given | DP §4 #19, §8 P-05 |
| CAND-PRC-001 | Price list / قائمة الأسعار | PRC | master | SHARED | CANDIDATE | DP §3 #2, §7.1; KB §5 #2, §4.1 |
| CAND-PRC-002 | Promotion / عرض ترويجي | PRC | master | PRIVATE | CANDIDATE | DP §3 #2; KB §5 #2 |
| CAND-PRC-003 | Coupon / قسيمة خصم | PRC | master | PRIVATE | CANDIDATE | DP §3 #2; KB §5 #2 |
| CAND-PRC-004 | Loyalty points / نقاط الولاء | PRC | transactional | PRIVATE | CANDIDATE (advanced tier) | DP §3 #2; KB §5 #2, §8 |
| CAND-INV-001 | Warehouse / المستودع | INV | master | SHARED | CANDIDATE | DP §3 #3, §5 G8, §7.1; KB §6.8 |
| CAND-INV-002 | Stock balance / رصيد المخزون | INV | transactional | SHARED? | CANDIDATE | DP §3 #3; KB §5 #3 |
| CAND-INV-003 | Stock move / حركة المخزون | INV | transactional | PRIVATE | CANDIDATE | DP §4 #3; KB §6.8 |
| CAND-INV-004 | Reservation / حجز المخزون | INV | transactional | PRIVATE | CANDIDATE | DP §3 #3; KB §5 #3 |
| CAND-INV-005 | Transfer / تحويل مخزني | INV | transactional | PRIVATE | CANDIDATE | DP §3 #3; KB §5 #3, §6.8 |
| CAND-INV-006 | Stock count / جرد المخزون | INV | transactional | PRIVATE | CANDIDATE | DP §3 #3; KB §5 #3 |
| CAND-INV-007 | Lot / التشغيلة | INV | master | SHARED? | OPEN (OI-06) | DP §3 #3, §7.1, §8 P-03; KB §4.1 |
| CAND-INV-008 | Bundle / حزمة (inventory-level kit) | INV | master | SHARED? | OPEN (OI-09) | DP §8 P-06, §9 research-log (bundle patterns) |
| CAND-PTY-001 | Customer / العميل | PTY | master | SHARED | CANDIDATE (credential split OI-13) | DP §3 #4, §7.1, §8 P-10; KB §5 #4 |
| CAND-PTY-002 | Supplier / المورّد | PTY | master | SHARED | CANDIDATE | DP §3 #4, §7.1; KB §5 #4 |
| CAND-PTY-003 | Address / العنوان | PTY | master | PRIVATE | CANDIDATE | DP §3 #4; KB §5 #4 |
| CAND-PTY-004 | Credit limit & terms / حد الائتمان وشروطه | PTY | master | SHARED? | OPEN (OI-16) | DP §3 #4; KB §5 #4 |
| CAND-PUR-001 | Purchase order / أمر شراء | PUR | transactional | PRIVATE | CANDIDATE | DP §3 #5; KB §5 #5 |
| CAND-PUR-002 | Goods receipt / استلام بضاعة | PUR | transactional | SHARED? | CANDIDATE | DP §3 #5; KB §5 #5 |
| CAND-PUR-003 | Supplier invoice / فاتورة مورّد | PUR | transactional | SHARED | CANDIDATE | DP §3 #5, §6; KB §5 #5 |
| CAND-SLS-001 | Quotation / عرض سعر | SLS | transactional | PRIVATE | CANDIDATE | DP §3 #6; KB §5 #6 |
| CAND-SLS-002 | Sales order / أمر بيع | SLS | transactional | SHARED? | CANDIDATE | DP §3 #6, §6; KB §5 #6 |
| CAND-SLS-003 | Sales invoice / فاتورة مبيعات | SLS | transactional | SHARED | CANDIDATE | DP §3 #6, §6; KB §5 #6 |
| CAND-SLS-004 | Sales return / exchange / مرتجع واستبدال المبيعات | SLS | transactional | SHARED? | CANDIDATE | DP §3 #6; KB §5 #6, §6.4 |
| CAND-STL-001 | Collection / تحصيل | STL | transactional | PRIVATE | CANDIDATE | DP §3 #7; KB §5 #7 |
| CAND-STL-002 | Payment / سداد | STL | transactional | PRIVATE | CANDIDATE | DP §3 #7; KB §5 #7 |
| CAND-STL-003 | Credit limit & terms / حد الائتمان وشروطه (as an AR concern) | STL | master | SHARED? | OPEN (OI-16) | KB §4.1 (`b2bCredit` → "Pricing + AR"), §5 #7 |
| CAND-TAX-001 | Tax category / التصنيف الضريبي | TAX | config | SHARED? | CANDIDATE | DP §3 #8, §8 P-08; KB §5 #8 |
| CAND-TAX-002 | E-invoice submission / إرسال الفاتورة الإلكترونية | TAX | transactional | PRIVATE | CANDIDATE (inferred) | DP §7.2 tax, §5 G15; KB §5 #8 |
| CAND-TAX-003 | Tax return / الإقرار الضريبي | TAX | transactional | PRIVATE | CANDIDATE | DP §3 #8; KB §5 #8 |
| CAND-ACC-001 | Posting rule / قاعدة الترحيل | ACC | config | PRIVATE | CANDIDATE | DP §3 #9; KB §5 #9 |
| CAND-ACC-002 | GL account / حساب الأستاذ العام | ACC | master | PRIVATE | CANDIDATE (inferred from "GL") | DP §3 #9; KB §5 #9 |
| CAND-ACC-003 | Journal entry / قيد يومية | ACC | transactional | PRIVATE | CANDIDATE (inferred from "GL", "auto-posting") | DP §3 #9, §5 G12; KB §3 D7 |
| CAND-ACC-004 | Cost centre / مركز التكلفة | ACC | master | PRIVATE | CANDIDATE | DP §3 #9; KB §5 #9 |
| CAND-ACC-005 | Budget / الموازنة | ACC | transactional | PRIVATE | CANDIDATE | DP §3 #9; KB §5 #9 |
| CAND-ACC-006 | Accounting period / الفترة المحاسبية | ACC | config | PRIVATE | CANDIDATE (inferred from "period close") | DP §3 #9; KB §5 #9 |
| CAND-SWR-001 | Serial number / الرقم التسلسلي | SWR | master | SHARED? | CANDIDATE | DP §3 #10, §7.1; KB §4.1, §5 #10 |
| CAND-SWR-002 | Warranty card / بطاقة الضمان | SWR | transactional | PRIVATE | CANDIDATE | DP §3 #10; KB §5 #10 |
| CAND-SWR-003 | Service return / مرتجع الصيانة | SWR | transactional | PRIVATE | CANDIDATE | DP §3 #10; KB §5 #10 |
| CAND-APR-001 | Approval step configuration / إعداد خطوات الموافقة | APR | config | PRIVATE | CANDIDATE | DP §3 #11, §5 G11; KB §3 D6 |
| CAND-SHP-001 | Delivery / التوصيل | SHP | transactional | PRIVATE | CANDIDATE (inferred from "delivery status") | DP §3 #12; KB §5 #12 |
| CAND-SHP-002 | Carrier / شركة الشحن | SHP | config | PRIVATE | CANDIDATE | DP §3 #12; KB §5 #12 |
| CAND-SFR-001 | Cart / سلة التسوق | SFR | transactional | PRIVATE | CANDIDATE | DP §3 #13; KB §5 #13 |
| CAND-SFR-002 | Storefront domain (subdomain / custom domain → tenant) / نطاق المتجر | SFR | config | PRIVATE | OPEN (OI-14) | DP §5 G10, §8 P-11; KB §3 D4 |
| CAND-INS-001 | Dashboard / لوحة المؤشرات | INS | config | PRIVATE | CANDIDATE | DP §3 #14; KB §5 #14 |
| CAND-INS-002 | Saved filter / مرشّح محفوظ | INS | config | PRIVATE | CANDIDATE | DP §3 #14; KB §5 #14 |
| CAND-OPEN-003 | Capability flag setting / إعداد مفتاح القدرة | UNCLEAR (P-02) | config | SHARED | OPEN (OI-05) | DP §3 #15, §7.1; KB §4.1 |
| CAND-OPEN-004 | Activity preset / القالب المسبق للنشاط | UNCLEAR (P-02) | config | PRIVATE | OPEN (OI-05) | DP §3 #15, §7.1; KB §4.2, §6.9 |

Not registered as entities:
- *Publish state* is an attribute of Item (DP §7.1).
- *Dual quantity* is an attribute of stock and lines (OI-06).
- *Customer account (CUSTOMER realm)*, *Tenant*, *LookupType/Value*, *FileDocument* and *NumberSeries* are provided by erp-core and receive no entity ID [C:C-GIVEN-3].

## 5. Shared entity declarations

Consumers are taken from the stated dependencies (DP §6; KB §5). "SHARED?" marks a row where sharing is inferred and still needs confirmation.

| Candidate ref | Entity | Owner | Consumers (stated or inferred) | Status | Source |
|---|---|---|---|---|---|
| CAND-CAT-001 | Item | CAT | PRC, INV, PUR, SLS, SFR | SHARED — CANDIDATE | DP §6 (Pricing, Inventory, Purchasing, Sales & POS, Storefront → Catalog) |
| CAND-CAT-002 | Variant | CAT | PRC, INV, PUR, SLS, SFR (if stock/price key on Variant) | SHARED? — OPEN (OI-07) | DP §7.1, §8 P-04 |
| CAND-CAT-004 | Category | CAT | SFR (browse), INS (reporting) | SHARED? — CANDIDATE | DP §7.1; DP §6 Storefront, Insights |
| CAND-CAT-005 / CAND-INV-008 | Bundle | CAT or INV | INV, SLS (explode to components) | SHARED? — OPEN (OI-09) | DP §8 P-06 |
| CAND-CAT-007 | Barcode | CAT | SLS (POS scanning), PUR (receipt) (inferred) | SHARED? — OPEN (OI-10) | DP §8 P-07 |
| CAND-CAT-008 / CAND-OPEN-001 | Brand | CAT or given mdl | SFR, INS (inferred) | SHARED? — OPEN (OI-08) | DP §8 P-05 |
| CAND-CAT-009 / CAND-OPEN-002 | Unit of measure | CAT or given mdl | INV, PUR, SLS (inferred; dual quantity) | SHARED? — OPEN (OI-08) | DP §8 P-05; KB §4.1 |
| CAND-PRC-001 | Price list | PRC | SLS, SFR; PTY / STL for `b2bCredit` | SHARED — CANDIDATE | DP §6 (Sales & POS, Storefront → Pricing); KB §4.1 |
| CAND-INV-001 | Warehouse | INV | PUR, SLS, SFR | SHARED — CANDIDATE | DP §6; KB §6.8 |
| CAND-INV-002 | Stock balance | INV | SLS, SFR (availability) | SHARED? — CANDIDATE | DP §6 |
| CAND-INV-007 | Lot | INV | PUR (receipt), SLS (FEFO issue) | SHARED? — OPEN (OI-06) | KB §4.1 |
| CAND-PTY-001 | Customer | PTY | SLS, STL, SFR; linked to given sec user (P-10) | SHARED — CANDIDATE | DP §6; DP §8 P-10 |
| CAND-PTY-002 | Supplier | PTY | PUR, STL | SHARED — CANDIDATE | DP §6 |
| CAND-PTY-004 / CAND-STL-003 | Credit limit & terms | PTY or STL | SLS, PRC, STL / PTY | SHARED? — OPEN (OI-16) | KB §4.1, §5 #4, §5 #7 |
| CAND-PUR-002 | Goods receipt | PUR | INV (stock move), SWR (serial capture) | SHARED? — CANDIDATE | DP §6; KB §4.1 |
| CAND-PUR-003 | Supplier invoice | PUR | STL, TAX, ACC | SHARED — CANDIDATE | DP §6 (Settlements, Tax, Accounting → Purchasing) |
| CAND-SLS-002 | Sales order | SLS | SHP, SFR, INV (reservation) | SHARED? — CANDIDATE | DP §6 |
| CAND-SLS-003 | Sales invoice | SLS | STL, TAX, ACC, SWR, SHP | SHARED — CANDIDATE | DP §6 |
| CAND-SLS-004 | Sales return / exchange | SLS | INV, STL, SWR | SHARED? — CANDIDATE | DP §6 |
| CAND-TAX-001 | Tax category | TAX | CAT (Variant reference, P-08), PUR, SLS | SHARED? — OPEN (OI-11) | DP §8 P-08 |
| CAND-SWR-001 | Serial number | SWR | INV (receipt), SLS (sale) | SHARED? — CANDIDATE | DP §6; KB §4.1 |
| CAND-OPEN-003 | Capability flag setting | UNCLEAR (P-02) | all business modules (read only, via the `FeatureFlags` API) | SHARED — OPEN (OI-05) | DP §5 G1; KB §4.1 |

## 6. Structural / implementation registry

None yet. This section is filled by P2 / P3.1.

## 7. Cross-module dependency index

Derived: `platform/dependency-graph.json` (`gov.py graph`, from P0's `platform-dependencies` block, each SRS's `module-dependencies` block and the XM registers). This registry writes no dependency rows. The relations stated in domain-profile §6 reach P0 (see OI-17).

## 8. Open question index

Every row is OPEN and is to be resolved by P0 dialogue.

| Ref | Question | Evidence | Affected rows | Status |
|---|---|---|---|---|
| OI-01 | Which code is binding for Sales & POS, SerialWarranty and Storefront? | The profile `vocabulary.module_prefixes` holds SLS, SWR and SFR. The domain-profile §4, §7.1, §7.2 and §7.3 use SAL, SRW and STF. The domain-profile also marks the ten codes already in the profile as "PROPOSED" | §2.2; §3 rows 6, 10, 13; CAND-SLS-*, CAND-SWR-*, CAND-SFR-*; steering §7.1–7.3 | OPEN |
| OI-02 | Does the given erp-core 1.6.0 change any match the domain-profile made against 1.5.0, such as the P-03 seams or the given rows 16–27? | The domain-profile header and §9 research-log cite erp-core 1.5.0. The change-manifest for CAT v2 records "given erp-core upgraded 1.5.0 → 1.6.0", re-runs from P0, and notes the domain-profile is re-run by hand when the given's module set changes | §1; §3 rows 16–27; OI-06, OI-08, OI-13, OI-14, OI-15 | OPEN |
| OI-03 | Is there an earlier project-registry whose entries must be kept (RULE-3)? | This run received only the domain-profile and the change-manifest. The change-manifest says CAT v2 has a baseline v1, but no baseline registry was supplied, so this file was written as bootstrap 1.0.0 | whole registry; §1 version history; §10 | OPEN |
| OI-04 | P-01: what are the module codes and bounded contexts of the 14 business modules? | DP §7.2 says the profile's `bounded_contexts` list was not provided, so it proposes one context per module. DP §8 P-01 is pending | §3 context column; steering §7.2 | OPEN |
| OI-05 | P-02: who owns the FeatureFlags API, the activity presets and the setup wizard, and under what code? | DP §3 #15 and §4 #15 leave the owner in-dialogue. DP §8 P-02 recommends a small foundation module on given `cu` (e.g. `SET`), pending | §3 row 15; CAND-OPEN-003, CAND-OPEN-004 | OPEN |
| OI-06 | P-03: are custom attributes, `lot_id`, dual-quantity columns, cash & bank and the tax adapter provided by the core or owned by the platform? | KB §4.1 and §5 name them as "core seams". DP §8 P-03 and §9 say the erp-core 1.5.0 manifest does not list them and recommend platform ownership in CAT / INV / STL / TAX, pending | CAND-CAT-003, CAND-INV-007; §3 rows 1, 3, 7, 8 | OPEN |
| OI-07 | P-04: does every Item have at least one Variant, with stock, price, barcode and tax code keyed on the Variant? | DP §8 P-04 is pending. The §9 research-log row is NO-RESEARCH | CAND-CAT-001, CAND-CAT-002; §5 | OPEN |
| OI-08 | P-05: are Brand and UoM CAT-owned or given `mdl` lookup types, and is Category a CAT-owned hierarchy? | DP §7.1 says "CAT (via mdl)". DP §8 P-05 recommends given `mdl` for Brand/UoM and CAT for Category, pending | CAND-CAT-004, 008, 009; CAND-OPEN-001, 002 | OPEN |
| OI-09 | P-06: is the bundle composition owned by CAT or by INV (as kits)? | DP §8 P-06 recommends CAT defines the composition and INV/SLS explode it. The §9 research-log says systems differ between catalogue bundles and inventory kits (NO-RESEARCH) | CAND-CAT-005, CAND-INV-008 | OPEN |
| OI-10 | P-07: how do barcodes and weighed-item codes work (0..n per Variant; embedded price/weight under `dualQuantity`)? | DP §7.1 and §8 P-07 are pending. The KB does not mention barcodes | CAND-CAT-007 | OPEN |
| OI-11 | P-08: what are the Catalog-side e-invoice obligations (ETA GS1/EGS item code on the Variant; tax-category reference owned by TAX)? | DP §5 G15 and §8 P-08 are pending. The §9 research-log row is NO-RESEARCH | CAND-CAT-002, CAND-TAX-001 | OPEN |
| OI-12 | P-09: are item names Arabic-mandatory with English optional? | DP §8 P-09 is pending (ZATCA language requirement NO-RESEARCH). The profile language policy is `ar, en` | CAND-CAT-001, CAND-CAT-002 | OPEN |
| OI-13 | P-10: how is customer identity split between given `sec` (credentials, CUSTOMER realm) and PTY (commercial customer, SOFT-READ link)? | DP §4 #4, §8 P-10; KB §3 D5, §5 #4 | CAND-PTY-001; §3 row 17 | OPEN |
| OI-14 | P-11: does an SFR-owned host→tenant resolver feed the given `tenant` context? | DP §5 G10 and §8 P-11 are pending. KB §3 D4 says "designed once in the Storefront API" | CAND-SFR-002; §3 rows 13, 16 | OPEN |
| OI-15 | P-12: do media binaries live in given `file`, with CAT owning ordering, primary image and alt text? | DP §4 #20 and §8 P-12 are pending | CAND-CAT-006; §3 row 20 | OPEN |
| OI-16 | Who owns credit limits and terms? | KB §5 #4 and DP §3 #4 give "credit" to Party. KB §4.1 maps `b2bCredit` to "Pricing + AR", and AR belongs to Settlements (KB §5 #7) | CAND-PTY-004, CAND-STL-003, CAND-PRC-001 | OPEN |
| OI-17 | Are the HARD-FK / SOFT-READ kinds of the module-to-module edges confirmed? | DP §6 calls them "a recommendation (P-01 consequence) to be confirmed in-dialogue". P0 turns them into `platform-dependencies` | §7 | OPEN |

## 9. Pipeline / progress status

These rows are maintained by the orchestrator from commits (E5).

| # | Module | Code | Status | Source |
|---|---|---|---|---|
| 1 | Catalog | CAT | IN PROGRESS: v2 (baseline v1), change set CS-CAT-001 ADDITIVE, re-run from P0 | change-manifest CAT v2 |
| 2 | Pricing | PRC | NOT STARTED | DP §4 #2; E5 |
| 3 | Inventory | INV | NOT STARTED | DP §4 #3; E5 |
| 4 | Party | PTY | NOT STARTED | DP §4 #4; E5 |
| 5 | Purchasing | PUR | NOT STARTED | DP §4 #5; E5 |
| 6 | Sales & POS | SLS | NOT STARTED | DP §4 #6; E5 |
| 7 | Settlements | STL | NOT STARTED | DP §4 #7; E5 |
| 8 | Tax & E-invoicing | TAX | NOT STARTED | DP §4 #8; E5 |
| 9 | Accounting | ACC | NOT STARTED | DP §4 #9; E5 |
| 10 | SerialWarranty | SWR | NOT STARTED | DP §4 #10; E5 |
| 11 | Approvals | APR | NOT STARTED | DP §4 #11; E5 |
| 12 | Shipping | SHP | NOT STARTED | DP §4 #12; E5 |
| 13 | Storefront | SFR | NOT STARTED | DP §4 #13; E5 |
| 14 | Insights | INS | NOT STARTED | DP §4 #14; E5 |

Delivery order (G14; KB §8):
1. CAT, PTY, INV, PUR, SLS, STL, TAX
2. SFR, SHP
3. ACC (visible mode), SWR, APR, INS
4. Advanced tier

## 10. Change / event history

| Date | Event | Ref | Stage ref | Summary |
|---|---|---|---|---|
| 2026-10-26 | BOOTSTRAP | — | — | P-1 extracted from domain-profile v1 (KB raw-idea.md as the brief; change-manifest CAT v2). Modules 14 (+1 OPEN component, +12 given EXISTING) · codes 14 IN PROFILE + 3 RESERVED aliases · entity candidates 58 · shared declarations 22 · confirmed decisions 17 · open items 17 · steering 24 terms / 14 contexts / 14 codes copied verbatim · structural 0 · dependency rows 0 (derived) |

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


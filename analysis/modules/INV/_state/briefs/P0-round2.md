# BRIEF — stage `P0` (Platform Inception) · module INV · v1 · profile `commerce`

Lane `analysis-dialogue` · implementer claude:sonnet · effort high · round 2

## Rules that bind this run
- Questions: **allowed**. Close every open point inside this dialogue with a researched, recommended answer; never write an external open-questions file.
- Owns IDs: POL — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-INV-001`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files):
- `governance-shared\analysis\modules\INV\P0\platform-summary.md`
- `governance-shared\analysis\modules\INV\P0\module-registry-inv.md`
- `governance-shared\analysis\modules\INV\P0\business-policies-inv.md`
- Respond with one `<<<FILE: path/from/the/repo/root.md>>>` … `<<<END FILE>>>` block per file, complete. You have NO write access to the project checkout: a file written anywhere else is lost, and a response without the blocks produces nothing.

## Dialogue protocol (converging, in-brief)
Implementers claude:opus, claude:sonnet alternate for at most 4 rounds; converge on **mutually-acceptable**.
Round 1 drafts the artifacts and, for every open point, a `PROPOSAL:` block (options, researched recommendation, sources).
Each later round answers every open PROPOSAL with a `DECISION: <title>` line followed by the reasoning (accept / amend, with the source), refines the artifacts, and appends `<!-- CONVERGED -->` at the end of the response when nothing material remains open. The last response is final. Every `DECISION:` block is persisted by the orchestrator as an ADR (`analysis/decisions/INV/ADR-{MOD}-{seq:03d}.md`, status RESOLVED-IN-DIALOGUE) — write the title as the decision, and cite the ids it binds on a `traces` line.

## Previous round
(see `governance-shared\analysis\modules\INV\_state\briefs\P0.response1.md` — appended below)

## Contracts checked by `gov.py analyze` after this stage
- **C2** project registry → inception: C2.1 exists {'artifact': 'project-registry'} [CRITICAL]; C2.2 registry-agree {'registry': 'project-registry', 'categories': 'all'} [MAJOR]; C2.3 ids-owned {'artifact': 'project-registry', 'defines': []} [MAJOR]
- **C3** inception → PRD: C3.1 exists {'artifact': 'platform-summary'} [CRITICAL]; C3.2 exists {'artifact': 'module-registry'} [CRITICAL]; C3.3 exists {'artifact': 'business-policies'} [CRITICAL]; C3.4 ids-owned {'stage': 'P0'} [CRITICAL]; C3.5 no-questions {'stage': 'P0'} [CRITICAL]; C3.6 languages {'stage': 'P0'} [MAJOR]; C3.7 registry-agree {'artifact': 'business-policies', 'registry': 'module-registry', 'kinds': ['POL']} [MAJOR]; C3.8 ears {'kind': 'POL', 'patterns': 'factory.ids.ears.patterns'} [MAJOR]; C3.9 ids-continue {'stage': 'P0'} [CRITICAL]
- **C-GIVEN-P0** given dependency → inception (the matching is data): C-GIVEN-3 given-registry-agrees {'spec': 'capabilities.given', 'summary': 'platform-summary', 'when': 'factory.capabilities.given'} [MAJOR]

## Blocks this stage emits — each a ```yaml name={name} fence, held to its schema by C0.1 (`gov.py analyze`)
- `platform-dependencies` in `platform-summary` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"platform-dependencies","description":"P0 platform-summary: every module of the platform, its tier (derived from the graph's levels when absent) and the modules it reads from (factory.yaml → xm.graph.platform_block)","type":"object","required":["modules"],"additionalProperties":false,"properties":{"modules":{"type":"object","propertyNames":{"pattern":"^[A-Z][A-Z0-9]*$"},"additionalProperties":{"type":"object","required":[],"additionalProperties":false,"properties":{"tier":{"type":"integer","minimum":0},"depends_on":{"type":"array","items":{"type":"string","pattern":"^[A-Z][A-Z0-9]*$"}}}}}}}
  ```
- `given-modules` in `platform-summary` (optional) — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"given-modules","description":"P0 platform summary: the candidates the inception matched against the project's given dependencies (factory.yaml → capabilities.given) — one row per matched capability: its name as the vision states it, the given, the manifest entry (`entry_pointer`) and the provides words the platform uses; `rows: []` when nothing hit","type":"object","required":["rows"],"additionalProperties":false,"properties":{"rows":{"type":"array","items":{"type":"object","required":["name","given","entry"],"additionalProperties":false,"properties":{"name":{"type":"string","minLength":1},"given":{"type":"string","minLength":1},"entry":{"type":"string","minLength":1},"provides_used":{"type":"array","items":{"type":"string"}}}}}}}
  ```

---
# ENGINE
# Platform Inception — ENGINE

```
Engine        : Platform Inception
Stage id      : P0
Pass          : 1
Questions     : allowed — resolved in-dialogue with recommended answers; user confirms
Dialogue      : yes — lane analysis-dialogue (claude:opus + claude:sonnet; ≤ 4 rounds; converge on "mutually-acceptable"; output: resolved-decisions)
Inputs        : domain-profile, project-registry
Produces      : platform-summary.md · module-registry-{mod}.md · business-policies-{mod}.md
Owns IDs      : POL   → `{prefix}-{MOD}-{seq}` (seq width 3)
Given         : erp-core 1.6.0 (§1.5 — matched first)
Next          : P0.5
Module        : INV   Version: 1
Profile       : commerce — COMMERCE
```

This engine turns free-form vision text into a closed architectural context: first a
**platform summary** (tiered module table, dependency map), then — per module — a
**module registry** and **business policies** written as EARS statements with
`POL` IDs. Its outputs are CONTEXT for `P0.5`, never requirements. [C:C3.4]

Completion (write → registry → analyze → commit) is owned by the orchestrator — see
`shared/GOVERNANCE-CORE.md`. In a delta version (version > 1): read `_state/current-{artifact}` of the previous
version for every input and for this stage's own artifacts, and emit only ADDED / [C:C12.2]
MODIFIED / REMOVED elements plus the `change-manifest.md` per `shared/VERSIONING.md`.

```
╔══════════════════════════════════════════════════════════════════════╗
║ ABSOLUTE BOUNDARY                                                    ║
║ This stage does not write requirements, screens, field lists,        ║
║ validation rules, entity IDs or any content owned by P0.5 or later.  ║
║ A request for such content → produce this stage's artifacts, then    ║
║ redirect once (§7). No partial draft. No exception.                  ║
╚══════════════════════════════════════════════════════════════════════╝
```

Language policy: narrative in `en`; every module name, entity name and policy
statement carries all of `ar, en`.

---

## 1 — Reading protocol (before any analysis)

```
STEP A — domain-profile.md (steering — read first)
  §7 STEERING → vocabulary (use verbatim), bounded contexts, module codes,
                identifier rules, knowledge sources
  §1–§6       → scope, purpose, responsibilities, components, rules, relations
  §8          → resolved decisions — do not re-open one [G]

STEP B — project-registry.md (categories per shared/REGISTRY-SCHEMA.md)
  module index            → known modules — do NOT re-discover
  entity ownership        → known owners — apply directly
  shared declarations     → known shared entities — apply directly
  dependency graph        → platform/dependency-graph.json (gov.py graph) — extend, do not contradict [G]
  open-question index     → OPEN rows this stage may resolve in dialogue (§5)
  pipeline status         → modules already past this stage → EXISTING / EXCEPTION

STEP C — prior module artifacts (this stage's own outputs for other modules,
         from _state/ when they exist)
  entities owned / lookups owned / lookups consumed / dependencies → ground truth;
  a conflict between vision text and a module registry → the registry wins and the
  conflict is listed under OPEN ITEMS of the platform summary.

STEP D — knowledge sources (cite when applying a default)
  - profiles/commerce/knowledge/raw-idea.md
```

Every structural decision comes from A → B → C → D in that order, then from domain
best practice; the user is asked only what none of these settle (§5). [G]

### 1.5 — GIVEN DEPENDENCIES (STEP 0 of every extraction)

The project builds ON these libraries; what they provide already exists and is never a module of [C:C-GIVEN-3]
this platform. Before STEP 1 of §2.1, every candidate module, component or entity the vision names [C:C-GIVEN-3]
is matched against the tables below (modules.*.provides, modules.*.entities): a hit is a row of the
MODULES table with Status `EXISTING (given)` and a row of the `GIVEN MODULES` block
pointing at its entry (e.g. `erp-core:modules.audit` — the pattern `{name}:modules.{module}`); Phase 2 produces nothing for it. A module that USES
a given reaches it through the given's interfaces and extension points — the SRS stage declares each
use in its `given-usage` block, and the module registry lists the given under DEPENDENCIES. [C:C-GIVEN-2]

**`erp-core` 1.6.0** — manifest `deps/erp-core/core-manifest.yaml`, usage skill `.claude/skills/erp-core-usage@1.6.0` [L:given-version]

| Module | Entry | Provides | Entities | Interfaces · extension points |
|---|---|---|---|---|
| `audit` | `erp-core:modules.audit` | audit, audit-events, audit-trail, change-history, retention | AuditEvent | com.erp.audit.crossmodule.AuditApi · api `docs/api-docs/audit/index.md` |
| `common` | `erp-core:modules.common` | base-entity, audit-columns, optimistic-locking, service-result, error-codes, localized-errors, api-envelope, validation, search, paging, idempotency, boolean-converters, owned-lookups | — | — |
| `cu` | `erp-core:modules.cu` | settings, configuration, app-configuration, platform-defaults, tenant-overrides | AppConfiguration | com.erp.cu.crossmodule.SettingsApi · api `docs/api-docs/cu/index.md` |
| `events` | `erp-core:modules.events` | events, domain-events, event-bus, async-listeners | — | com.erp.events.DomainEventPublisher, com.erp.events.OutboxPublisher, com.erp.events.OutboxReplayApi, com.erp.events.OutboxDeliveryGate, com.erp.events.OutboxSubscriber |
| `file` | `erp-core:modules.file` | files, documents, file-categories, images, uploads, downloads, public-files, storage, download-tokens, attachments | FileCategory, FileDocument | com.erp.file.crossmodule.FileDocumentLookupApi, com.erp.file.crossmodule.FileImageStoreApi, com.erp.file.crossmodule.FilePrivateStoreApi, com.erp.file.service.DownloadTokenStore, com.erp.file.storage.StorageProvider · api `docs/api-docs/file/index.md` |
| `gl` | `erp-core:modules.gl` | chart-of-accounts, fiscal-periods, journal-entries, trial-balance, ledger-currencies | — | com.erp.gl.crossmodule.GlAccountLookupApi, com.erp.gl.crossmodule.GlEntryQueryApi, com.erp.gl.crossmodule.GlPostingApi · api `docs/api-docs/gl/index.md` |
| `mdl` | `erp-core:modules.mdl` | lookups, lookup-types, lookup-values, reference-lists, code-lists, master-data | LookupType, LookupValue | com.erp.mdl.crossmodule.MdlLookupApi · api `docs/api-docs/mdl/index.md` |
| `module` | `erp-core:modules.module` | business-modules, module-manifests, module-entitlements, module-license, module-catalog, home-cards | — | com.erp.module.crossmodule.HomeCardApi, com.erp.module.crossmodule.ModuleCatalogApi, com.erp.module.crossmodule.ModuleEntitlementApi, com.erp.module.HomeCardProvider · api `docs/api-docs/module/index.md` |
| `notif` | `erp-core:modules.notif` | notifications, templates, channels, email, in-app, inbox, dispatch, notification-log | NotificationChannelConfig, NotificationInboxItem, NotificationLog, NotificationLogAttachment, NotificationTemplate | com.erp.notif.crossmodule.NotificationChannelAdminApi, com.erp.notif.crossmodule.NotificationDispatchApi, com.erp.notif.crossmodule.NotificationLogQueryApi, com.erp.notif.channel.ChannelProvider, com.erp.notif.crossmodule.RecipientDirectory · api `docs/api-docs/notif/index.md` |
| `report` | `erp-core:modules.report` | reports, report-definitions, exports, printing, csv-export, pdf-export, print-templates, payment-file-export, payment-layouts | — | com.erp.report.crossmodule.PaymentFileExportApi, com.erp.report.crossmodule.PrintApi, com.erp.report.ReportProvider, com.erp.report.crossmodule.PrintAuthorizationHook, com.erp.report.crossmodule.PrintParameterContributor, com.erp.report.crossmodule.PrintTemplateSource · api `docs/api-docs/report/index.md` |
| `sec` | `erp-core:modules.sec` | users, roles, permissions, grants, staff-auth, customer-auth, customer-accounts, customer-default-roles, customer-invitations, login, jwt, sessions, password-reset, signup, module-registry, screens, audit-log, security-dashboard | ActionRegistry, AuditLogEntry, ModuleRegistry, Role, RoleActionGrant, RoleModuleGrant, RoleScreenGrant, ScreenRegistry, SignupRequest, User, UserRoleAssignment | com.erp.sec.crossmodule.SecAdminRecoveryApi, com.erp.sec.crossmodule.SecCustomerDefaultRoleApi, com.erp.sec.crossmodule.SecCustomerInvitationApi, com.erp.sec.crossmodule.SecModuleRegistryApi, com.erp.sec.crossmodule.SecUserDirectoryApi, com.erp.sec.permission.PermissionContributor · api `docs/api-docs/sec/index.md` |
| `sequence` | `erp-core:modules.sequence` | number-series, numbering, document-numbers, sequences | NumberSeries | com.erp.sequence.crossmodule.NumberSeriesApi · api `docs/api-docs/sequence/index.md` |
| `tenant` | `erp-core:modules.tenant` | tenants, tenancy, tenant-context, tenant-provisioning, tenant-export, tenant-branding, tenant-logo, public-app-config, tenant-data-residency | Tenant | com.erp.tenant.TenantPolicy, com.erp.tenant.TenantRunner, com.erp.tenant.crossmodule.TenantLookupApi, com.erp.tenant.TenantExportContributor, com.erp.tenant.TenantProvisioningContributor · api `docs/api-docs/tenant/index.md` |


---

## 2 — Phase 1: vision → `platform-summary.md`

### 2.1 Transformation (three steps)

```
STEP 0 — MATCH against the given(s) (§1.5) — before anything is extracted
  Every candidate module / component / entity → compare with every `provides` word and every
  entity of every given module. A hit is `EXISTING (given)` in the MODULES table and a row of
  the GIVEN MODULES block (e.g. `erp-core:modules.audit` — the pattern `{name}:modules.{module}`));
  it is listed, never extracted as a module of this platform. [C:C-GIVEN-3]
STEP 1 — EXTRACT from the vision text
  Modules (explicit or implied) — detection table from the profile:
    CAT    Catalog  ← catalog / catalogue / item / product / SKU / variant / category / barcode / bundle / kit / صنف / منتج / تصنيف / باركود
    PRC    Pricing  ← price / price list / promotion / discount / coupon / loyalty / سعر / قائمة أسعار / عرض / خصم / كوبون / نقاط
    INV    Inventory  ← inventory / stock / warehouse / transfer / stock count / reservation / lot / expiry / FEFO / dual quantity / مخزون / مستودع / مخزن / تحويل / جرد / تشغيلة / صلاحية
    PTY    Party  ← customer / supplier / vendor / party / address / credit limit / payment terms / عميل / مورد / عنوان / حد ائتمان
    PUR    Purchasing  ← purchase / purchase order / PO / goods receipt / GRN / supplier invoice / شراء / أمر شراء / استلام / فاتورة مورد
    SLS    Sales & POS  ← sale / sales / quotation / sales order / invoice / return / exchange / POS / point of sale / cashier / shift / بيع / عرض سعر / فاتورة / مرتجع / نقطة بيع / كاشير
    STL    Settlements  ← collection / payment / receipt voucher / payment voucher / ageing / receivable / payable / AR / AP / payment gateway / تحصيل / سداد / سند قبض / سند صرف / ذمم
    TAX    Tax & E-invoicing  ← VAT / tax / e-invoice / e-invoicing / e-receipt / ETA / ZATCA / tax return / ضريبة / فاتورة إلكترونية / إيصال إلكتروني / إقرار ضريبي
    ACC    Accounting  ← accounting / journal / GL / ledger / posting / chart of accounts / cost centre / cost center / budget / period close / محاسبة / قيد / دفتر الأستاذ / مركز تكلفة / موازنة / إقفال
    SWR    Serial & Warranty  ← serial / serial number / IMEI / warranty / warranty card / service return / رقم تسلسلي / ضمان / بطاقة ضمان / صيانة
    APR    Approvals  ← approval / approve / reject / approval step / موافقة / اعتماد / رفض
    SHP    Shipping  ← shipping / delivery / carrier / courier / shipment / tracking / شحن / توصيل / شركة شحن / تتبع
    SFR    Storefront  ← storefront / online store / e-commerce / shop / cart / checkout / guest checkout / subdomain / custom domain / customer area / متجر إلكتروني / سلة / إتمام الشراء
    INS    Insights  ← dashboard / report / insight / KPI / saved filter / لوحة / تقرير / مؤشر
    (unlisted) → domain-profile §4 components + layer heuristics; still one of the
               profile's codes (CAT, PRC, INV, PTY, PUR, SLS, STL, TAX, ACC, SWR, APR, SHP, SFR, INS) or RESERVED per the registry.
  Explicit statements:
    scope exclusions ("without X", "not now")
    specific policies (limits, thresholds, exceptions)   → §3.3 candidates
    custom values (named lookup values)                   → §3.3 candidates

STEP 2 — ENRICH from knowledge sources + domain-profile
  For each module: layer, type, tier, dependencies from the knowledge files and
  the domain-profile relations; remove what the user excluded; add what the user
  mentioned beyond the pattern. Cite the source of every enrichment.

STEP 3 — RESOLVE from the registry
  module past this stage in pipeline status   → EXCEPTION (read as-is; skip in Phase 2)
  module with a prior module-registry artifact → EXISTING (Phase 2 extends it)
  otherwise                                    → NEW
```

### 2.2 Platform summary — template

```markdown
# PLATFORM SUMMARY — [Platform name — from the vision text or domain-profile]
══════════════════════════════════════════════════════════════════
Profile : commerce   Domain profile : v[N]   Registry : v[semver]
══════════════════════════════════════════════════════════════════

## OVERVIEW
[One paragraph — what the platform does; enriched with domain context, not a restatement.]

## MODULES
| #   | Code | Module | Bounded context | Layer | Type | Status |
|-----|------|--------|-----------------|-------|------|--------|
| 1.1 | [code] | [display] | [context] | L1 | [master data / engine / reference / transactional / reporting] | NEW |
| 2.1 | [code] | [display] | [context] | L3 | [type] | EXISTING |
| 0.1 | — | [capability name] | [context] | L0 | [type] | EXISTING (given) |
Status: NEW (Phase 2 produces) · EXISTING (Phase 2 extends) · EXCEPTION (read as-is) · EXISTING (given) (provided by the library — a row of GIVEN MODULES below names which; nothing produced)
Numbering: [tier].[sequence within tier] — the user requests Phase 2 by this number.

## GIVEN MODULES — data, not prose (`gov.py analyze` reads this named block; C0.1 holds it to its schema)
```yaml name=given-modules
rows:                        # one row per candidate a given covers; `rows: []` when nothing hit
  - {name: "[capability as the vision names it]", given: erp-core, entry: erp-core:modules.audit, provides_used: [the `provides` words the platform uses]}
```
`entry` is the manifest entry in the form `{name}:modules.{module}` and `provides_used` lists only what that entry [C:C-GIVEN-3]
provides; the Status of the same candidate in the MODULES table is `EXISTING (given)`, its label
elsewhere `EXISTING (given: erp-core)`. A row here is never also a NEW module above, and a NEW module is never named [C:C-GIVEN-3]
like what a given provides. The build order ignores these rows: a given is DELIVERED before the platform exists. [C:C-GIVEN-3]


## DEPENDENCIES — data, not prose (`gov.py graph` reads this named block; the build order is DERIVED from it; C0.1 holds it to its schema)
```yaml name=platform-dependencies
modules:
  [CODE-A]: {tier: 1, depends_on: []}
  [CODE-B]: {tier: 2, depends_on: [CODE-A]}
```
One entry per module of the platform, every one with its tier; `depends_on` lists the
modules it reads from (a lower tier never depends on a higher one — `xm.tier_rule`). The build [C:C6.11]
order, the waves and the critical path are computed from this block (`gov.py plan-order`);
never write them by hand. [C:C9.23]

## DEFERRED (not in scope for this version)
| Item | Reason / activation trigger |
| Workflow engine | profile: `forbidden` |
| [user-excluded item] | user stated "not now" |

## RESOLVED DECISIONS (this phase)
| # | Point | Recommended | Confirmed by user | Sources |

## OPEN ITEMS
[Only a registry ↔ vision conflict or a genuinely ambiguous scope boundary that the
 dialogue could not close. Otherwise: "None — platform scope fully determined."]

## NEXT STEP
Reply with a plain instruction to adjust, or with a module number to start Phase 2.
```

### 2.3 Confirmation and number stability

```
After producing the summary: "[N] modules, [N] exceptions. Confirm, or state one change."
Adjustments apply immediately and only the MODULES table is re-shown: [G]
  add a module      → next number at the end of its tier
  defer / remove    → status changes, number kept
  change a status   → status changes, number kept
NUMBER STABILITY: a number is fixed at first assignment — "1.1" goes on meaning [G]
the same module. Phase 2 begins on the user's first number request.
```

---

## 3 — Phase 2: module convergence (per requested module)

### 3.1 Request protocol

```
Status EXISTING (given) → no files; confirm "[n] [name] is provided by `erp-core`
                   (its GIVEN MODULES row, e.g. erp-core:modules.audit) — used, not produced" and offer the next module. [C:C-GIVEN-3]
Status EXCEPTION → no files; confirm "[n] [module] is EXCEPTION — read as-is" and offer
                   the next module.
Status EXISTING  → read the prior module registry; EXTEND it (fill gaps, do not replace); [G]
                   note "[N] gaps filled from [source]".
Status NEW       → full pattern from knowledge sources + domain-profile.
Always append the readiness block (§3.5) after the two files.
```

### 3.2 Auto-completion protocol (for every gap in the module structure)

```
STEP 1 → prior module registry (EXISTING)         → use it
STEP 2 → project-registry ownership / dependency  → use it
STEP 3 → knowledge sources (§1 STEP D)            → apply, cite
STEP 4 → domain-profile rules + domain best practice → apply, cite
Document every auto-decision:   AUTO: [decision]  FROM: [step / source]  IF WRONG: [override]
STEPS 1–4 all fail → the point is a QUESTION (§5) — resolved in dialogue with a
recommended answer; the user confirms. Never an assumption written as fact.
```

### 3.3 Module registry — template (`module-registry-{mod}.md`)

```markdown
## MODULE REGISTRY — [Module display] ([CODE])
══════════════════════════════════════════════════════════════════
Module Code    : [CODE]   (profile.vocabulary.module_prefixes)
Bounded context: [context id]
Layer / Type   : [L1–L4] / [type]     Execution tier : [n.m]
Source         : NEW / EXTENDED from prior registry
Knowledge      : [knowledge file(s) / domain-profile §]
Readiness      : READY / PARTIALLY_READY
══════════════════════════════════════════════════════════════════

ENTITIES OWNED   (names only — entity IDs are assigned by P1) [C:C3.4]
| Entity (ar/en) | Kind (master / transactional / lookup / config / security) | PRIVATE / SHARED | Source |

LOOKUPS OWNED    (value lists this module masters)
| Lookup key | Description | Initial values (only those the user named) | Source [G] |
Rule (profile): all LOV values runtime-loaded from the core lookup module (mdl); no hardcoded enums in APIs or field specs; activity presets and capability flags are core settings, read through FeatureFlags

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
| [a given module, e.g. `erp-core:modules.audit`] | (given — no XM edge; the SRS declares the use in its `given-usage` block) | [interface / extension point used] |
ROOT: YES / NO

AUTO-DECISIONS
AUTO: [decision]  FROM: [source]  IF WRONG: [override]

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
══════════════════════════════════════════════════════════════════
```

### 3.4 Business policies — template (`business-policies-{mod}.md`)

This file carries what the domain's standards cannot know: the client's own policies,
custom values and scope exceptions. Standard domain behaviour is applied by
`P0.5` and later stages from the knowledge sources — it is not repeated here.
If the user stated nothing specific, the file is minimal by design.

Every policy is one `POL` record written in **EARS** form (one pattern per
statement; `factory.ids.ears.patterns`):

```
  ubiquitous  The system shall …
  state       While <condition>, the system shall …
  event       When <condition>, the system shall …
  optional    Where <condition>, the system shall …
  unwanted    If <condition>, then the system shall …
```

```markdown
## BUSINESS POLICIES — [Module display] ([CODE])
══════════════════════════════════════════════════════════════════
Module   : [CODE]     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers) [G]
POL-[CODE]-001 — [short name]
  Statement : [EARS — exactly one pattern; the subject is "the system"] [C:C3.8]
  Pattern   : [ubiquitous | state | event | optional | unwanted]
  Trigger   : [Create / Update / Submit / Approve / …]
  Rationale : [why the client wants it — one line]
  Source    : [vision text quote / dialogue resolution #]
  Status    : CONFIRMED
(If none: "None — standard domain rules apply.")

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
| Lookup key | Added values | Source |
(If none: "None — standard values apply.")

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
(If none: "None — standard scope applies.")

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
══════════════════════════════════════════════════════════════════
```

Policy rules: a policy is a NEED at platform level, not a validation rule — no field
names, no error messages, no API shapes. Sequence numbers are continuous per module
and never reused. A policy the user did not state and did not confirm is not written. [C:C3.9]

### 3.5 Readiness block (after every module)

```
✓ [Module] — P0 complete
  Next : P0.5 reads platform-summary.md · module-registry-{mod}.md · business-policies-{mod}.md
  Precondition for P0.5: every module in `depends_on` has a published contract or a passed gate
  Another module? [the next module of `gov.py plan-order`]
```

---

## 4 — Registry step content

`module-registry-{mod}.md` IS this stage's registry output. In addition the
orchestrator merges into `project-registry.md`:

```
module index          : status of the module (NEW → IN PROGRESS), tier, layer, type
entity ownership      : ENTITIES OWNED rows (CANDIDATE → REGISTERED, still no ID)
shared declarations   : SHARED rows
dependency graph      : derived by gov.py graph from the `platform-dependencies` block (never merged by hand) [C:C9.23]
open-question index   : rows RESOLVED by this stage's dialogue (+ resolution)
pipeline status       : P0 = DONE for the module
event history         : "P0 completed: [module list]"
```

---

## 5 — Questions (allowed here) — how they are asked and closed

```
A QUESTION exists only when §1 A–D and §3.2 STEPS 1–4 leave a point unresolved. [G]

QUESTION — [point]
  Affects       : [module / entity / dependency / scope]
  Options       : A) … (trade-off)   B) … (trade-off)
  Researched    : [what the knowledge sources / domain-profile say — cited]
  Recommended   : [option] — because [rationale]
```

Lane `analysis-dialogue`: the implementers (claude:opus, claude:sonnet) converge on each QUESTION —
challenge, answer, ≤ 4 rounds, until **mutually-acceptable**. The converged
block is presented to the user as the recommended answer; the user confirms or adjusts.

```
Resolution is recorded in the RESOLVED DECISIONS table of the artifact it affects.
No external open-questions file. A point the user leaves undecided stays under
OPEN ITEMS of the platform summary and is carried to P0.5 (the last stage that may ask).
Never ask about: anything in the domain-profile, the registry, a prior module registry,
or the knowledge sources.
```

---

## 6 — Continuation

```
Resume with: platform summary + the module artifacts of completed modules (from
_state/ or the version folder) + registry pipeline status.
Announce: "Resuming P0. Completed: [list]. Pending: [NEW/EXISTING from the summary]."
No re-analysis of completed modules. Always re-emit the platform summary before
ending if in-session adjustments were made.
```

---

## 7 — Boundaries and enforcement

```
OWNS      : platform-summary.md · module-registry-{mod}.md · business-policies-{mod}.md · POL IDs · tier and build-order
            assignment · entity / lookup candidate discovery and ownership · dependency map
DOES NOT  : any ID of US (P0.5), REQ (P1), AC (P1), ENT (P1), RULE (P1), DBF (P2), XM (P2), CON (P1.5), API (P3.1), QR (P3.1), UXD (P3.2), SCR (P3.2), SCR-REQ (P1), TC (P4), FEAT (feature) ·
            requirements · screens · field lists · validation rules · DDL · execution phases

VIOLATION (this stage's output contains any of these):
  screens with field lists · validation logic · requirement statements other than
  POL policies · any ID owned by another stage · permission tables · test scenarios

RUNTIME REDIRECT — when the user asks for requirements / screens / rules / fields:
  1. Complete this stage's artifacts for the module (they ARE the correct answer).
  2. Redirect once: "Requirements begin in P0.5 and later stages; these files are
     their input."
  3. Offer the next valid action (another module number, or proceed).
```

---

## 8 — Self-check before emitting

- [ ] Every module in the summary has a code from the profile (or RESERVED in the registry), a tier, a status — or is a `EXISTING (given)` row with its entry in the GIVEN MODULES block.
- [ ] Every candidate was matched against the given(s) first (§1.5 STEP 0); no NEW module re-states what a given provides; the GIVEN MODULES block is present (`rows: []` when nothing hit). [C:C-GIVEN-3]
- [ ] Every entity owned has a kind from `master, transactional, lookup, config, security` and a source; no entity ID.
- [ ] Every policy is exactly one EARS pattern, has a Source, a Trigger, a continuous sequence number. [C:C3.8]
- [ ] Every auto-decision carries AUTO / FROM / IF WRONG; every default cites a knowledge source.
- [ ] Every QUESTION raised appears in a RESOLVED DECISIONS table (or under OPEN ITEMS with the user's explicit deferral).
- [ ] No requirement, screen, field, rule, permission or later-stage ID anywhere.
- [ ] Vocabulary matches the domain-profile STEERING block verbatim.
- [ ] Names and statements carry all of `ar, en`.


---
# INPUTS (generated current state)

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

---
# PREVIOUS ROUND
Round 1 draft for INV (claude:opus). There are 3 complete files below, followed by 10 `PROPOSAL:` blocks for claude:sonnet to answer in round 2. I didn't write anything to the checkout; the orchestrator saves the files from the blocks.

**Before round 2, one mismatch needs your attention.** The registry in this brief lists PTY as NOT STARTED. Your workspace git log shows PTY v1 has already been through its backend and frontend splits. I followed the brief and marked PTY as NEW. If the git log is correct, refresh the registry and change PTY to EXCEPTION, keeping its number 1.2.

<<<FILE: governance-shared\analysis\modules\INV\P0\platform-summary.md>>>
# PLATFORM SUMMARY — Retail & Wholesale Commerce SaaS on erp-core
══════════════════════════════════════════════════════════════════
Profile : commerce   Domain profile : v1   Registry : v1.0.0   Given : erp-core 1.6.0
Run module : INV (Inventory / المخزون) · version 1 · dialogue round 1 (claude:opus)
══════════════════════════════════════════════════════════════════

## OVERVIEW
A multi-tenant SaaS for small and medium retail and wholesale traders in Egypt and the Gulf. It covers the back-office (purchasing, inventory, sales, receivables and payables, accounting, tax and e-invoicing), an offline-capable point of sale (POS) and an integrated storefront that share one catalogue, one stock and one customer base [KB:raw-idea.md §1]. Adoption is driven by regulation: ZATCA phase-2 waves and Egypt's B2C e-receipt expansion require every small trader to issue compliant documents [KB:raw-idea.md §2]. One codebase serves many trading activities. Business logic reads only six capability flags, and an activity is a preset that sets them [KB:raw-idea.md §3 D2, §4]. The platform builds on erp-core 1.6.0 and rebuilds nothing it provides: tenancy, identity, settings, lookups, files, numbering, events, audit, notifications, printing, entitlements and, new in 1.6.0, the general ledger [§1.5 manifest; DP §5 G6]. Simplicity is measured: at most 4 mandatory fields per screen, and setup plus the first invoice in 30 minutes or less [KB:raw-idea.md §6.10].

## MODULES
| #    | Code | Module (en / ar) | Bounded context | Layer | Type | Status |
|------|------|------------------|-----------------|-------|------|--------|
| 1.1  | CAT | Catalog / الكتالوج | catalog | L1 | master data | EXISTING |
| 1.2  | PTY | Party / الأطراف | party | L1 | master data | NEW |
| 2.1  | INV | Inventory / المخزون | inventory | L2 | engine | NEW |
| 2.2  | PRC | Pricing / التسعير | pricing | L2 | engine | NEW |
| 3.1  | PUR | Purchasing / المشتريات | purchasing | L3 | transactional | NEW |
| 3.2  | SLS | Sales & POS / المبيعات ونقطة البيع | sales | L3 | transactional | NEW |
| 4.1  | STL | Settlements / التسويات | settlements | L3 | transactional | NEW |
| 4.2  | TAX | Tax & E-invoicing / الضرائب والفوترة الإلكترونية | tax | L3 | engine | NEW |
| 4.3  | SWR | SerialWarranty / الأرقام التسلسلية والضمان | after-sales | L3 | transactional | NEW |
| 4.4  | SHP | Shipping / الشحن | shipping | L3 | transactional | NEW |
| 5.1  | ACC | Accounting / المحاسبة | accounting | L3 | engine | NEW |
| 5.2  | APR | Approvals / الموافقات | approvals | L2 | engine | NEW |
| 5.3  | SFR | Storefront / المتجر الإلكتروني | storefront | L4 | transactional | NEW |
| 6.1  | INS | Insights / التحليلات | insights | L4 | reporting | NEW |
| 0.1  | — | Tenancy, tenant context, branding, public app-config / المستأجرون | — (given) | L0 | reference | EXISTING (given) |
| 0.2  | — | Users, roles, permissions, staff & customer auth / المستخدمون والصلاحيات | — (given) | L0 | reference | EXISTING (given) |
| 0.3  | — | Settings, capability flags & activity presets / الإعدادات ومفاتيح القدرة والقوالب المسبقة | — (given) | L0 | reference | EXISTING (given) |
| 0.4  | — | Lookups / master-data code lists / القوائم المرجعية | — (given) | L0 | reference | EXISTING (given) |
| 0.5  | — | Files, images, attachments / الملفات | — (given) | L0 | reference | EXISTING (given) |
| 0.6  | — | Number series / document numbers / تسلسل الأرقام | — (given) | L0 | reference | EXISTING (given) |
| 0.7  | — | Domain events / outbox / الأحداث | — (given) | L0 | engine | EXISTING (given) |
| 0.8  | — | Audit trail / change history / سجل التدقيق | — (given) | L0 | reference | EXISTING (given) |
| 0.9  | — | Notifications / الإشعارات | — (given) | L0 | engine | EXISTING (given) |
| 0.10 | — | Reports, exports, printing / التقارير والطباعة | — (given) | L0 | reporting | EXISTING (given) |
| 0.11 | — | Business-module catalogue & entitlements / كتالوج الوحدات والاستحقاقات | — (given) | L0 | reference | EXISTING (given) |
| 0.12 | — | Base entity, audit columns, locking, validation, search, paging, idempotency, API envelope / الأساس المشترك | — (given) | L0 | reference | EXISTING (given) |
| 0.13 | — | General ledger: chart of accounts, journal entries, fiscal periods / دفتر الأستاذ العام | — (given) | L0 | engine | EXISTING (given) |

Status: NEW (Phase 2 produces) · EXISTING (Phase 2 extends) · EXCEPTION (read as-is) · EXISTING (given) (provided by erp-core; the GIVEN MODULES block below names which entry; nothing produced).
Numbering: [tier].[sequence within tier]. The user requests Phase 2 by this number.
Status source: registry §9 pipeline status. CAT is re-running from P0 in v2 with a v1 baseline, so it is EXISTING. Every other module is NOT STARTED, so it is NEW. The orchestrator re-derives a status from pipeline status whenever that status advances.

## GIVEN MODULES — data, not prose
```yaml name=given-modules
rows:
  - {name: "Tenancy, tenant context, branding, public app-config", given: erp-core, entry: "erp-core:modules.tenant", provides_used: [tenants, tenancy, tenant-context, tenant-provisioning, tenant-branding, public-app-config]}
  - {name: "Users, roles, permissions, staff & customer auth, customer accounts, signup", given: erp-core, entry: "erp-core:modules.sec", provides_used: [users, roles, permissions, grants, staff-auth, customer-auth, customer-accounts, signup]}
  - {name: "Settings, capability flags and activity presets (stored settings)", given: erp-core, entry: "erp-core:modules.cu", provides_used: [settings, configuration, app-configuration, platform-defaults, tenant-overrides]}
  - {name: "Lookups / master-data code lists", given: erp-core, entry: "erp-core:modules.mdl", provides_used: [lookups, lookup-types, lookup-values, code-lists]}
  - {name: "Files, public images, attachments", given: erp-core, entry: "erp-core:modules.file", provides_used: [files, images, public-files, uploads, attachments]}
  - {name: "Number series / document numbers", given: erp-core, entry: "erp-core:modules.sequence", provides_used: [number-series, document-numbers]}
  - {name: "Domain events / outbox", given: erp-core, entry: "erp-core:modules.events", provides_used: [events, domain-events, event-bus, async-listeners]}
  - {name: "Audit trail / change history", given: erp-core, entry: "erp-core:modules.audit", provides_used: [audit, audit-trail, change-history]}
  - {name: "Notifications (email, in-app)", given: erp-core, entry: "erp-core:modules.notif", provides_used: [notifications, email, in-app, dispatch]}
  - {name: "Reports, exports, printing (Jasper)", given: erp-core, entry: "erp-core:modules.report", provides_used: [reports, printing, pdf-export, csv-export]}
  - {name: "Business-module catalogue & entitlements", given: erp-core, entry: "erp-core:modules.module", provides_used: [business-modules, module-entitlements, module-catalog, home-cards]}
  - {name: "Base entity, audit columns, locking, validation, search, paging, idempotency, API envelope", given: erp-core, entry: "erp-core:modules.common", provides_used: [base-entity, audit-columns, optimistic-locking, validation, search, paging, idempotency, api-envelope, localized-errors]}
  - {name: "GL — chart of accounts, journal entries, fiscal periods, ledger currencies", given: erp-core, entry: "erp-core:modules.gl", provides_used: [chart-of-accounts, fiscal-periods, journal-entries, trial-balance, ledger-currencies]}
```
Each candidate here is labelled `EXISTING (given: erp-core)` elsewhere. No row is also a NEW module above. ACC keeps only what `gl` does not provide: auto-posting rules, cost centres, budgets and period close. It posts through `GlPostingApi`.

## DEPENDENCIES — data, not prose
```yaml name=platform-dependencies
modules:
  CAT: {tier: 1, depends_on: []}
  PTY: {tier: 1, depends_on: []}
  INV: {tier: 2, depends_on: [CAT]}
  PRC: {tier: 2, depends_on: [CAT]}
  PUR: {tier: 3, depends_on: [CAT, INV, PTY]}
  SLS: {tier: 3, depends_on: [CAT, PRC, INV, PTY]}
  STL: {tier: 4, depends_on: [PTY, PUR, SLS]}
  TAX: {tier: 4, depends_on: [PUR, SLS]}
  SWR: {tier: 4, depends_on: [INV, SLS]}
  SHP: {tier: 4, depends_on: [SLS]}
  ACC: {tier: 5, depends_on: [INV, PUR, SLS, STL, TAX]}
  APR: {tier: 5, depends_on: [INV, PUR, SLS, STL]}
  SFR: {tier: 5, depends_on: [CAT, PRC, INV, PTY, SLS, STL, SHP]}
  INS: {tier: 6, depends_on: [CAT, PRC, INV, PTY, PUR, SLS, STL, TAX, ACC, SWR, APR, SHP]}
```
Edges come from the fixed DAG [KB:raw-idea.md §5], with two refinements set out under PROPOSAL INV-P04:
- ACC reads INV stock-move events, because stock adjustments and count differences post to the ledger.
- APR names its document modules explicitly. An approval outcome returns to them through given `events`, so there is no reverse edge.

INS reads every business module and therefore lists all 13 modules below it, including SFR. Tiers are the longest path from a root. The build order, waves and critical path are derived from this block by `gov.py plan-order`.

## DEFERRED (not in scope for this version)
| Item | Reason / activation trigger |
|---|---|
| Workflow engine (BPM) | profile: `forbidden`; Approvals stays lightweight [KB §3 D6] |
| Pharmacy, restaurant, gold, vehicle models; manufacturing, contracting, services | user excluded: different business models [KB §3 D1, §9] |
| Native mobile apps | user excluded v1: responsive web only [KB §9, §6.7] |
| Marketplace (multi-vendor in one storefront) | user excluded v1 [KB §9] |
| Report designer | user excluded v1 [KB §9] |
| AI assistant | deferred core module, advanced delivery tier [KB §8] |
| Multi-currency, cost centres/budgets, loyalty, carrier adapters | in scope but delivered in wave 4 (advanced tier). Every such dimension exists in the data model from day one [KB §6.8, §8] |

## RESOLVED DECISIONS (this phase)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| R-01 | Binding module codes and bounded contexts (registry OI-01, OI-04) | Profile codes CAT, PRC, INV, PTY, PUR, SLS, STL, TAX, ACC, SWR, APR, SHP, SFR, INS are binding. The aliases SAL, SRW and STF are retired. One bounded context per module, as in DP §7.2 | Round 1 recommendation (PROPOSAL INV-P01); DECISION due in round 2 | profile.vocabulary.module_prefixes; engine §2.1 detection table; registry §2.2, E2; DP §7.2 |
| R-02 | Re-match against erp-core 1.6.0 (registry OI-02, OI-06) | 1.6.0 adds `gl`, so GL accounts, journal entries and fiscal periods are given and ACC narrows. No entry provides custom attributes, lot, dual quantity, cash & bank or a tax adapter, so they are platform-own: variant attributes in CAT, lot and dual quantity in INV, cash & bank in STL, the tax adapter in TAX | Round 1 recommendation (PROPOSAL INV-P02) | §1.5 manifest table (erp-core 1.6.0); KB §4.1, §5; DP §8 P-03 |
| R-03 | Owner of capability flags, activity presets and the setup wizard (registry OI-05) | No new module code. Flags and presets are given `cu` settings, read through one `FeatureFlags` facade over `SettingsApi`. The 5-step setup wizard is a frontend flow that calls each step's owner | Round 1 recommendation (PROPOSAL INV-P03) | profile rule (flags and presets are core settings read through FeatureFlags); KB §4.1, §6.9; DP §5 G1, G6 |
| R-04 | Module-to-module edge kinds (registry OI-17) | DP §6 kinds confirmed (HARD-FK to masters and documents; SOFT-READ for ACC, APR, SFR, INS), plus ACC → INV and APR → INV/PUR/SLS/STL | Round 1 recommendation (PROPOSAL INV-P04) | KB §5; DP §6; registry OI-17 |
| R-05 | Pricing is absent from the delivery order of KB §8, but Sales & POS depends on it [KB §5] | AUTO: the build order is derived from the dependency block, so PRC is built before SLS. Only loyalty stays in the advanced tier | AUTO (derivation) | KB §5, §8; engine §2.2 (build order derived, never hand-written) |

Module-scoped registry items OI-08, OI-10, OI-11, OI-12, OI-13, OI-14, OI-15 and OI-16 belong to the P0 runs of CAT, PTY, SFR and STL. Registry OI-03 is an orchestrator matter. None of them changes the platform scope set out above.

## OPEN ITEMS
None — platform scope fully determined.

## NEXT STEP
14 modules, 0 exceptions, 13 given capabilities. Reply with an instruction to adjust, or with a module number to start Phase 2. This run produces 2.1 INV.
<<<END FILE>>>

<<<FILE: governance-shared\analysis\modules\INV\P0\module-registry-inv.md>>>
## MODULE REGISTRY — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module Code    : INV   (profile.vocabulary.module_prefixes)
Bounded context: inventory — where stock is and how much: stock, moves, reservations, transfers, counts, lots, dual quantity
Layer / Type   : L2 / engine     Execution tier : 2.1
Source         : NEW
Knowledge      : KB:raw-idea.md §1, §4.1, §4.2, §5 #3, §6.2, §6.7, §6.8; DP §3 #3, §5 G1 G3 G8, §7.1; erp-core 1.6.0 manifest (§1.5)
Readiness      : PARTIALLY_READY (PROPOSALS INV-P05 … INV-P10 close by DECISION in round 2)
══════════════════════════════════════════════════════════════════

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity (ar/en) | Kind | PRIVATE / SHARED | Source |
|---|---|---|---|
| Warehouse / المستودع | master | SHARED (PUR, SLS, SFR, INS) | KB §6.8; DP §7.1, §5 G8; registry §4 |
| Branch / الفرع | master | SHARED (PUR, SLS, STL, TAX, INS) | KB §6.8 ("one warehouse/branch … to start"); PROPOSAL INV-P07 |
| Stock balance / رصيد المخزون | transactional | SHARED (SLS, SFR, INS — read only) | KB §5 #3; DP §6 |
| Stock move / حركة المخزون | transactional | PRIVATE (published as domain events to ACC, SWR, INS) | KB §6.8; DP §4 #3 |
| Reservation / حجز المخزون | transactional | PRIVATE (created by SLS and SFR through an INV crossmodule API) | KB §5 #3 |
| Transfer / تحويل مخزني | transactional | PRIVATE | KB §5 #3, §6.8 |
| Stock count / جرد المخزون | transactional | PRIVATE | KB §5 #3, §6.7 |
| Stock adjustment / تسوية المخزون | transactional | PRIVATE | AUTO (count differences and write-offs need a stock document); KB §5 #3 |
| Lot / التشغيلة | master | SHARED (PUR receipt, SLS FEFO issue) | KB §4.1 (`lotExpiry`); DP §7.1; PROPOSAL INV-P02 |
| Stock valuation (average cost) / تقييم المخزون (متوسط التكلفة) | transactional | SHARED (SLS margin, ACC posting, INS) | PROPOSAL INV-P09 |

Not registered here:
- Dual quantity / الكمية المزدوجة is an attribute of stock balances, moves and lots, not an entity (registry §4).
- An inventory-level kit is not registered (PROPOSAL INV-P06).
- Serial number / الرقم التسلسلي belongs to SWR.
- Inventory settings are given `cu` settings.

LOOKUPS OWNED    (value lists this module masters)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| inv.adjustment-reason | Stock adjustment reason / سبب تسوية المخزون (count difference, damage, write-off and similar) | — (none named by the user) | AUTO; KB §5 #3 |

Rule (profile): all LOV values are loaded at runtime from the core lookup module (mdl). There are no hardcoded enums in APIs or field specs. Activity presets and capability flags are core settings, read through FeatureFlags.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| Unit of measure / وحدة القياس | CAT (as a given `mdl` lookup type, DP §8 P-05) |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| Item / صنف | CAT | HARD-FK | Stock belongs to an item of the catalogue |
| Variant / متغيّر الصنف | CAT | HARD-FK | Stock, moves, reservations and lots key on the Variant; the default Variant applies when `variants` is off (PROPOSAL INV-P05) |
| Bundle / حزمة (composition) | CAT | SOFT-READ | Components are exploded into stock moves when a bundle is issued (PROPOSAL INV-P06) |
| Unit of measure conversions | CAT | SOFT-READ | Stock is held in the Variant's base unit of measure |
| Tenant / المستأجر | given `erp-core:modules.tenant` | (given) | Default warehouse seeded at provisioning |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| CAT | HARD-FK | Item, Variant, Bundle composition, unit-of-measure conversions |
| `erp-core:modules.cu` | (given — no XM edge; the SRS declares the use in its `given-usage` block) | `SettingsApi` through `FeatureFlags`: `lotExpiry`, `dualQuantity`, `bundles`; inventory settings (PROPOSALS INV-P08, INV-P10) |
| `erp-core:modules.sequence` | (given — no XM edge) | `NumberSeriesApi`: transfer, count and adjustment numbers |
| `erp-core:modules.events` | (given — no XM edge) | `DomainEventPublisher`, `OutboxPublisher`: stock-move events for ACC, SWR, INS; `OutboxSubscriber`: offline POS sales and documents from SLS and PUR |
| `erp-core:modules.audit` | (given — no XM edge) | `AuditApi`: change history of stock documents |
| `erp-core:modules.mdl` | (given — no XM edge) | `MdlLookupApi`: adjustment reason, unit of measure |
| `erp-core:modules.tenant` | (given — no XM edge) | `TenantProvisioningContributor`: default warehouse (and branch, PROPOSAL INV-P07) at tenant creation |
| `erp-core:modules.sec` | (given — no XM edge) | `PermissionContributor`: inventory permissions for the Warehouse role preset (D8) |
| `erp-core:modules.notif` | (given — no XM edge) | `NotificationDispatchApi`: near-expiry and negative-stock alerts |
| `erp-core:modules.report` | (given — no XM edge) | `ReportProvider`, `PrintApi`: count sheets, transfer prints, near-expiry and stock reports |
| `erp-core:modules.common` | (given — no XM edge) | base entity, audit columns, optimistic locking, paging, search, idempotency |
ROOT: NO
Consumed by: PUR, SLS, SWR, ACC, APR, SFR, INS (platform-dependencies).

AUTO-DECISIONS
AUTO: Serial identity is owned by SWR. INV holds only the quantities of serial-tracked Variants.  FROM: KB §4.1 (`serialTracking` → new module SerialWarranty), §5 (SWR depends on INV)  IF WRONG: move the serial ledger into INV and make SWR consume it.
AUTO: Stock is held in the Variant's base unit of measure. Purchase and sale units are converted through CAT-owned conversions.  FROM: domain best practice; DP §8 P-05  IF WRONG: hold stock per unit of measure with INV-owned conversions.
AUTO: One default warehouse is seeded at tenant provisioning through `TenantProvisioningContributor`.  FROM: KB §6.8; manifest `erp-core:modules.tenant`  IF WRONG: create the first warehouse in the setup wizard.
AUTO: Inventory settings (negative-stock allowance, near-expiry horizon) are given `cu` settings, not an INV entity.  FROM: profile rule (core settings read through FeatureFlags); DP §5 G6  IF WRONG: register an INV config entity.
AUTO: SLS (orders) and SFR (checkout) create reservations through an INV crossmodule API. A POS sale issues stock directly, without a reservation.  FROM: KB §1 (one stock), §5 (SLS, SFR depend on INV); DP §5 G5  IF WRONG: reserve on POS cart lines as well.
AUTO: Stock moves are published as domain events through the given outbox. ACC, SWR and INS consume them, and INV holds no reverse edge.  FROM: KB §5 #3 (Inventory uses events); DP §5 G5  IF WRONG: expose a crossmodule query API in their place.
AUTO: Dual quantity is an attribute of stock balances, moves and lots, not a separate entity.  FROM: registry §4; KB §4.1  IF WRONG: register a dual-quantity entity.
AUTO: Stock adjustment is a transactional candidate of INV.  FROM: KB §5 #3 (counts); domain best practice (a count difference posts through an adjustment document)  IF WRONG: post count differences directly from the stock count.

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| INV-P05 | Stock key (registry OI-07, INV side) | Stock, moves, reservations and lots key on the Variant; the default Variant applies when `variants` is off. A conflicting CAT registry decision wins (engine §1 STEP C) | Round 1 recommendation; DECISION due in round 2 | DP §8 P-04; KB §4.1; DP §5 G2 |
| INV-P06 | Bundle stock (registry OI-09) | CAT owns the composition. INV explodes a bundle into component stock moves at issue. A bundle holds no stock of its own, and kit assembly is excluded | Round 1 recommendation | DP §8 P-06; KB §3 D1, §9 |
| INV-P07 | Branch ownership | INV owns Branch next to Warehouse. One default branch is seeded and its selector stays hidden while its count is 1 | Round 1 recommendation | KB §6.8; DP §5 G8 |
| INV-P08 | Negative stock | Tenant setting, default off: back-office issues below zero are blocked. Synced offline POS sales are always accepted and alert the Warehouse role | Round 1 recommendation | KB §5 #6, §6.5; domain best practice |
| INV-P09 | Costing method | Perpetual weighted-average cost per Variant across the tenant; transfers carry cost unchanged; FIFO not offered in v1 | Round 1 recommendation | IAS 2 ¶25; IFRS for SMEs 13.18; KB §3 D7 |
| INV-P10 | Expired lots | An expired lot is excluded from issue for sale. It leaves stock only through a stock adjustment | Round 1 recommendation | KB §4.1, §4.2; consumer-protection practice |
══════════════════════════════════════════════════════════════════
<<<END FILE>>>

<<<FILE: governance-shared\analysis\modules\INV\P0\business-policies-inv.md>>>
## BUSINESS POLICIES — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module   : INV     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-INV-001 — One stock across channels / مخزون واحد لجميع القنوات
  Statement      : The system shall keep a single stock per warehouse that back-office documents, the point of sale and the storefront all draw from.
  Statement (ar) : يجب على النظام أن يحتفظ بمخزون واحد لكل مستودع تسحب منه مستندات المكتب الخلفي ونقطة البيع والمتجر الإلكتروني جميعًا.
  Pattern   : ubiquitous
  Trigger   : Stock move (any channel)
  Rationale : The product promise is one catalogue, one stock and one customer base across back-office, POS and storefront.
  Source    : KB:raw-idea.md §1 "one catalogue, one stock, one customer base"
  Status    : CONFIRMED

POL-INV-002 — Warehouse on every stock move / المستودع في كل حركة مخزون
  Statement      : The system shall record the warehouse on every stock move, including while the tenant has only one warehouse.
  Statement (ar) : يجب على النظام أن يسجّل المستودع في كل حركة مخزون، حتى عندما يكون لدى المستأجر مستودع واحد فقط.
  Pattern   : ubiquitous
  Trigger   : Create (stock move)
  Rationale : A second warehouse can be added later without data migration.
  Source    : KB:raw-idea.md §6.8 "every stock move carries warehouse_id"; DP §5 G8
  Status    : CONFIRMED

POL-INV-003 — Default warehouse at start / مستودع افتراضي عند البدء
  Statement      : When a tenant is provisioned, the system shall create one default warehouse so that stock can be received without any warehouse setup.
  Statement (ar) : عند إنشاء مستأجر جديد، يجب على النظام أن ينشئ مستودعًا افتراضيًا واحدًا بحيث يمكن استلام المخزون دون أي إعداد للمستودعات.
  Pattern   : event
  Trigger   : Tenant provisioning
  Rationale : The platform starts with one warehouse and nothing is imposed on the user.
  Source    : KB:raw-idea.md §6.8 "one warehouse/branch/currency/price list to start"; DP §5 G8
  Status    : CONFIRMED

POL-INV-004 — Hidden warehouse selector while one warehouse / إخفاء محدد المستودع عند وجود مستودع واحد
  Statement      : While the tenant has exactly one active warehouse, the system shall hide the warehouse selector, the inter-warehouse transfer action and the warehouse report filters.
  Statement (ar) : أثناء امتلاك المستأجر مستودعًا نشطًا واحدًا فقط، يجب على النظام أن يخفي محدد المستودع وإجراء التحويل بين المستودعات ومرشحات المستودع في التقارير.
  Pattern   : state
  Trigger   : View (any inventory screen or report)
  Rationale : Only what is meaningful is shown, with no dead menus.
  Source    : KB:raw-idea.md §6.3, §6.8; DP §5 G8
  Status    : CONFIRMED

POL-INV-005 — Reveal on second warehouse / الإظهار عند إضافة مستودع ثانٍ
  Statement      : When a second warehouse is added, the system shall reveal the warehouse selector, the inter-warehouse transfer action and the warehouse report filters without data migration or re-setup.
  Statement (ar) : عند إضافة مستودع ثانٍ، يجب على النظام أن يُظهر محدد المستودع وإجراء التحويل بين المستودعات ومرشحات المستودع في التقارير دون ترحيل للبيانات ودون إعادة إعداد.
  Pattern   : event
  Trigger   : Create (warehouse)
  Rationale : Multi-warehouse appears automatically when the business grows.
  Source    : KB:raw-idea.md §6.8 "reveals it — plus the related actions (e.g. inter-warehouse transfer) and report filters — automatically"
  Status    : CONFIRMED

POL-INV-006 — Lot and expiry on receipt / التشغيلة والصلاحية عند الاستلام
  Statement      : Where the lotExpiry capability flag is on, the system shall capture a lot and its expiry date on every stock receipt of an item tracked by lot.
  Statement (ar) : حيثما كان مفتاح القدرة lotExpiry مفعّلًا، يجب على النظام أن يسجّل التشغيلة وتاريخ صلاحيتها في كل استلام مخزون لصنف يُتتبَّع بالتشغيلة.
  Pattern   : optional
  Trigger   : Receive (stock receipt)
  Rationale : Grocery, cleaning-supplies, feed and produce presets need lot and expiry.
  Source    : KB:raw-idea.md §4.1 (`lotExpiry`: "lot + expiry on receipt"), §4.2
  Status    : CONFIRMED

POL-INV-007 — FEFO issue / الصرف بالأقرب انتهاءً
  Statement      : Where the lotExpiry capability flag is on, the system shall issue stock of items tracked by lot first-expiry-first-out (FEFO).
  Statement (ar) : حيثما كان مفتاح القدرة lotExpiry مفعّلًا، يجب على النظام أن يصرف مخزون الأصناف المتتبَّعة بالتشغيلة وفق مبدأ الأقرب انتهاءً يُصرف أولًا (FEFO).
  Pattern   : optional
  Trigger   : Issue (stock issue)
  Rationale : Stock nearest to expiry leaves first, which reduces write-offs.
  Source    : KB:raw-idea.md §4.1 (`lotExpiry`: "FEFO issue")
  Status    : CONFIRMED

POL-INV-008 — Near-expiry reporting / تقرير قرب انتهاء الصلاحية
  Statement      : Where the lotExpiry capability flag is on, the system shall report the stock that is approaching its expiry date.
  Statement (ar) : حيثما كان مفتاح القدرة lotExpiry مفعّلًا، يجب على النظام أن يقدّم تقريرًا بالمخزون الذي يقترب من تاريخ انتهاء صلاحيته.
  Pattern   : optional
  Trigger   : Report
  Rationale : Traders act on stock close to expiry before it is lost.
  Source    : KB:raw-idea.md §4.1 (`lotExpiry`: "near-expiry reports")
  Status    : CONFIRMED

POL-INV-009 — Dual quantity stock / مخزون بالكمية المزدوجة
  Statement      : Where the dualQuantity capability flag is on, the system shall track the stock of dual-quantity items in both a count and a measure of weight or length.
  Statement (ar) : حيثما كان مفتاح القدرة dualQuantity مفعّلًا، يجب على النظام أن يتتبّع مخزون الأصناف ذات الكمية المزدوجة بالعدد وبالقياس (الوزن أو الطول) معًا.
  Pattern   : optional
  Trigger   : Stock move (any)
  Rationale : Fabrics (metre), building materials and steel, and produce or meat by weight are stocked in two quantities.
  Source    : KB:raw-idea.md §4.1 (`dualQuantity`: "count + weight/length"), §4.2
  Status    : CONFIRMED

POL-INV-010 — Flag turn-off blocked while data depends on it / منع إيقاف المفتاح عند وجود بيانات تعتمد عليه
  Statement      : If a user turns off the lotExpiry or dualQuantity capability flag while stock data depends on it, then the system shall block the change and show which data depends on it.
  Statement (ar) : إذا حاول مستخدم إيقاف مفتاح القدرة lotExpiry أو dualQuantity بينما توجد بيانات مخزون تعتمد عليه، فيجب على النظام أن يمنع التغيير وأن يبيّن البيانات التي تعتمد عليه.
  Pattern   : unwanted
  Trigger   : Update (capability flag)
  Rationale : A flag may be turned on at any time, but never off while data depends on it, and no error is a dead end.
  Source    : KB:raw-idea.md §6.2, §6.5; DP §5 G3
  Status    : CONFIRMED

POL-INV-011 — Flags only, never the activity / المفاتيح فقط وليس النشاط
  Statement      : The system shall vary inventory behaviour only by capability flags and never by the tenant's declared activity.
  Statement (ar) : يجب على النظام أن يغيّر سلوك المخزون وفق مفاتيح القدرة فقط، وألّا يعتمد أبدًا على النشاط التجاري المعلن للمستأجر.
  Pattern   : ubiquitous
  Trigger   : Any inventory operation
  Rationale : One codebase serves every activity. The activity is only a preset that sets flags.
  Source    : KB:raw-idea.md §3 D2, §7; DP §5 G1
  Status    : CONFIRMED

POL-INV-012 — Mobile stock counting / الجرد عبر الهاتف المحمول
  Statement      : The system shall let a user perform a stock count from a mobile-sized responsive web screen.
  Statement (ar) : يجب على النظام أن يتيح للمستخدم إجراء جرد المخزون من شاشة ويب متجاوبة بحجم الهاتف المحمول.
  Pattern   : ubiquitous
  Trigger   : Count (stock count)
  Rationale : Counting is a mobile-first activity, and there is no native app in v1.
  Source    : KB:raw-idea.md §6.7 "Mobile-first for selling, collecting, counting", §9
  Status    : CONFIRMED

POL-INV-013 — Warehouse role preset / الدور المسبق «المستودع»
  Statement      : The system shall provide a Warehouse role preset that grants access to the inventory functions.
  Statement (ar) : يجب على النظام أن يوفّر دورًا مسبق الإعداد باسم «المستودع» يمنح الوصول إلى وظائف المخزون.
  Pattern   : ubiquitous
  Trigger   : Tenant provisioning / role assignment
  Rationale : Three role presets (Owner, Seller/Cashier, Warehouse) let a new tenant start without designing roles.
  Source    : KB:raw-idea.md §3 D8; registry §2.4 D8
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
None — standard values apply.

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Kit assembly and disassembly (stocked pre-built bundles) / تجميع الحزم وتفكيكها | Assembly orders are light manufacturing, which is excluded. Bundles are exploded into component stock moves | A later version that admits manufacturing scope | KB §3 D1, §9; PROPOSAL INV-P06 |
| Native mobile counting app / تطبيق جرد أصلي للهاتف | Counting runs on responsive web only in v1 | A version that admits native apps | KB §6.7, §9 |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| INV-P06 | Bundle stock | Explode to component moves; no kit stock. Once decided, adds the next free POL-INV id | Round 1 recommendation; DECISION due in round 2 | DP §8 P-06; KB §3 D1, §9 |
| INV-P08 | Negative stock | Tenant setting, default off; synced offline POS sales always accepted, with an alert. Once decided, adds the next free POL-INV ids | Round 1 recommendation | KB §5 #6, §6.5 |
| INV-P09 | Costing method | Perpetual weighted-average cost per Variant across the tenant. Once decided, adds the next free POL-INV id | Round 1 recommendation | IAS 2 ¶25 |
| INV-P10 | Expired lots | Excluded from issue for sale; removed only by a stock adjustment. Once decided, adds the next free POL-INV id | Round 1 recommendation | KB §4.1 |
══════════════════════════════════════════════════════════════════
<<<END FILE>>>

---

## PROPOSALS — round 1, for claude:sonnet to answer in round 2

**Live web research was not available this round.** External sources are cited from domain knowledge and marked *(not live-verified)*.

**PROPOSAL: INV-P01 — The profile codes (SLS, SWR, SFR) are binding, with one bounded context per module**
- **Affects:** all module codes and contexts (registry OI-01, OI-04).
- **Options:**
  - **A)** The profile codes. Nothing needs re-labelling.
  - **B)** The domain-profile aliases SAL, SRW and STF. This needs a change to the profile and a sweep of every code reference.
- **Researched:**
  - `profile.vocabulary.module_prefixes` is the durable record.
  - The engine's detection table uses SLS, SWR and SFR.
  - Registry rule E2 mints IDs only from IN PROFILE codes.
  - The CAT and PTY work already uses profile codes.
- **Recommended:** A. Keep the contexts from DP §7.2, with the codes swapped to SLS, SWR and SFR.
- **traces:** OI-01, OI-04

**PROPOSAL: INV-P02 — The erp-core 1.6.0 re-match adds `gl` as given; lot and dual quantity stay platform-own (INV)**
- **Affects:** ACC scope, INV entities, STL and TAX seams (OI-02, OI-06).
- **Options:**
  - **A)** Re-match against the 1.6.0 table. `gl` becomes EXISTING (given). Seams the manifest doesn't list become platform-own.
  - **B)** Keep the 1.5.0 matches. This rebuilds the GL and breaks G6.
- **Researched:**
  - The 1.6.0 `gl` entry provides chart-of-accounts, fiscal-periods, journal-entries, trial-balance and ledger-currencies, through `GlPostingApi`.
  - No 1.6.0 entry provides custom attributes, lot, dual quantity, cash & bank or a tax adapter.
  - KB §4.1 calls `lot_id` and the dual-quantity columns "core seams", but the manifest is authoritative (engine §1.5).
- **Recommended:** A. ACC keeps posting rules, cost centres, budgets and period close. INV owns Lot and the dual-quantity attributes.
- **traces:** OI-02, OI-06, POL-INV-006, POL-INV-009

**PROPOSAL: INV-P03 — Capability flags and presets are given `cu` settings behind one `FeatureFlags` facade, with no new module code**
- **Affects:** the owner of flags, presets and the setup wizard (OI-05).
- **Options:**
  - **A)** A new foundation module (for example SET). Its code isn't in the profile, and it would rebuild `cu` settings.
  - **B)** Given `cu` with a platform-shared `FeatureFlags` read facade. The wizard is a frontend flow that calls each step's owner: tenant, `cu`, TAX, STL, CAT.
  - **C)** Host it in CAT. That couples configuration to the catalogue and forces a PTY → CAT edge.
- **Researched:**
  - The profile rule says flags and presets "are core settings, read through FeatureFlags".
  - `cu` provides platform-defaults and tenant-overrides, which together are exactly a preset plus overrides.
  - G1 and G6 apply.
- **Recommended:** B.
- **traces:** OI-05, POL-INV-010, POL-INV-011

**PROPOSAL: INV-P04 — Confirm the DAG edge kinds; ACC reads INV, and APR depends on INV, PUR, SLS and STL**
- **Affects:** `platform-dependencies` (OI-17).
- **Researched:**
  - DP §6 kinds: HARD-FK to masters and documents; SOFT-READ (events) for ACC, APR and INS.
  - Under auto-posting (D7), stock adjustments and count differences must reach the ledger, but KB §5 doesn't list INV under ACC.
  - Approvals are "any document module" (D6), and INV transfers and adjustments are documents.
  - An approval outcome returns through given `events`, so there is no reverse edge.
- **Recommended:** accept the block as drafted. The tier rule holds: ACC and APR are tier 5, INS is tier 6.
- **traces:** OI-17

**PROPOSAL: INV-P05 — Stock is keyed on the Variant (the default Variant when `variants` is off)**
- **Options:**
  - **A)** Key on the Variant everywhere. One code path.
  - **B)** Key on the Item, with a nullable variant reference. Two code paths, which doubles the both-states test burden (G2).
- **Recommended:** A. A conflicting CAT registry decision wins (engine STEP C).
- **traces:** OI-07

**PROPOSAL: INV-P06 — CAT owns the bundle composition and INV explodes bundles into component moves; no kit stock**
- **Options:**
  - **A)** Explode at issue. Bundle availability is derived from its components.
  - **B)** INV-owned kits with stock that is assembled ahead of time. This needs assembly orders, which is light manufacturing and excluded by D1 and §9.
- **Recommended:** A. Once decided, it adds the next POL-INV id: "Where the bundles capability flag is on, when a bundle is issued, the system shall move the stock of its components."
- **traces:** OI-09

**PROPOSAL: INV-P07 — INV owns Branch alongside Warehouse**
- **Options:**
  - **A)** INV, at tier 2, is the lowest location owner that PUR, SLS, STL and TAX can all read.
  - **B)** No Branch in v1. This contradicts G8, which names branch.
  - **C)** SLS owns it. PUR and STL would then depend on SLS, which breaks the tiers.
  - **D)** PTY owns it. The trader's own branches are not parties.
- **Researched:** Egypt ETA issuer addresses carry a branch identifier, and ZATCA phase-2 onboarding is per EGS unit, which is usually per branch *(not live-verified)*.
- **Recommended:** A, with a default branch seeded and its selector hidden while its count is 1.
- **traces:** POL-INV-003

**PROPOSAL: INV-P08 — Negative stock is a tenant setting, default off; synced offline POS sales are always accepted, with an alert**
- **Options:**
  - **A)** Always block. This is impossible for an offline POS (KB §5 #6).
  - **B)** Always allow. Balances silently go wrong.
  - **C)** The hybrid above.
- **Researched:** Business Central has "Prevent Negative Inventory" and SAP Business One has "Block negative inventory", both as setup settings. Odoo allows negative stock by default *(not live-verified)*.
- **Recommended:** C. Once decided, it adds 2 POL-INV ids:
  - "Where the tenant disallows negative stock, the system shall block a back-office issue that would take a warehouse balance below zero."
  - "If a synchronised offline point-of-sale sale takes a warehouse balance below zero, then the system shall accept the sale and alert the Warehouse role."

**PROPOSAL: INV-P09 — Costing is perpetual weighted-average cost per Variant across the tenant**
- **Options:**
  - **A)** Weighted average.
  - **B)** FIFO layers.
  - **C)** Average cost per warehouse.
- **Researched:**
  - IAS 2 ¶25 allows FIFO or weighted average and prohibits LIFO.
  - IFRS for SMEs 13.18 says the same.
  - EAS 2 (Egypt) and SOCPA's IFRS adoption (KSA) follow these standards.
  - Specific identification (IAS 2 ¶23) applies only to units that are not interchangeable. Serial units of one Variant are interchangeable.
  - *(not live-verified)*
- **Recommended:** A. Transfers carry cost unchanged, and FIFO can be added in the advanced tier if demanded.

**PROPOSAL: INV-P10 — An expired lot is excluded from issue for sale and leaves stock only through a stock adjustment**
- **Options:**
  - **A)** Block it.
  - **B)** Warn only.
- **Researched:** Consumer-protection and food-safety rules in Egypt and KSA prohibit selling expired goods *(not live-verified)*.
- **Recommended:** A. Once decided, it adds the next POL-INV id: "If a lot has passed its expiry date, then the system shall exclude it from issue for sale."

---

```
✓ Inventory (INV) — P0 complete (round 1 draft; PROPOSALS INV-P01…P10 open)
  Next : P0.5 reads platform-summary.md · module-registry-inv.md · business-policies-inv.md
  Precondition for P0.5: CAT (the only module in INV's depends_on) has a published contract or a passed gate
  Another module? 2.2 PRC (next in plan-order after INV)
```

**Points round 2 should check:**
- **Policy IDs:** POL-INV-001 to 013 come only from the vision text. Policies that depend on a proposal take the next free IDs only after their DECISION, so the numbering never has gaps.
- **Vocabulary:** the files avoid the "do not say" terms ("store", "batch", "SKU", "client", "مخزن").
- **Language checks:** the EARS check is on the English `Statement` line. The `Statement (ar)` line is there for the bilingual check (C3.6).

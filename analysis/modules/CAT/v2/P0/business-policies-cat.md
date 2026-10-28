## BUSINESS POLICIES — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module   : CAT     Source of truth : user vision text (approved brief, profiles/commerce/knowledge/raw-idea.md) + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
Sequence : POL-CAT-001 … POL-CAT-009 (no earlier POL-CAT id in the generated current state; the stream starts here and continues from 010)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-CAT-001 — One catalogue for every channel / كتالوج واحد لكل القنوات
  Statement : The system shall maintain one catalogue that serves the back-office, the point of sale and the storefront.
  Statement (ar): يجب على النظام أن يحتفظ بكتالوج واحد يخدم الإدارة الخلفية ونقطة البيع والمتجر الإلكتروني.
  Pattern   : ubiquitous
  Trigger   : Create / Update
  Rationale : The differentiator is one product with one catalogue, one stock and one customer base.
  Source    : KB:raw-idea.md §1 "one catalogue, one stock, one customer base"; §2
  Status    : CONFIRMED

POL-CAT-002 — Catalogue behaviour from capability flags only / سلوك الكتالوج من مفاتيح القدرة فقط
  Statement : The system shall derive every activity-dependent catalogue behaviour from the capability flags `variants` and `bundles` only, never from the tenant's activity.
  Statement (ar): يجب على النظام أن يستمد كل سلوك للكتالوج يختلف باختلاف النشاط من مفتاحَي القدرة `variants` و`bundles` فقط، وألا يستمده أبدًا من النشاط التجاري للمستأجر.
  Pattern   : ubiquitous
  Trigger   : Create / Update / Display
  Rationale : One codebase serves every activity; an activity is only a preset that sets flags.
  Source    : KB:raw-idea.md §3 D2, §4.1, §7
  Status    : CONFIRMED

POL-CAT-003 — Show only what is enabled / عرض المُفعَّل فقط
  Statement : While a catalogue capability flag (`variants` or `bundles`) is off, the system shall hide the catalogue functions bound to that flag.
  Statement (ar): أثناء إيقاف مفتاح قدرة خاص بالكتالوج (`variants` أو `bundles`)، يجب على النظام أن يُخفي وظائف الكتالوج المرتبطة بذلك المفتاح.
  Pattern   : state
  Trigger   : Display
  Rationale : No dead menus; the UI shows only what is enabled.
  Source    : KB:raw-idea.md §4.1, §6.3
  Status    : CONFIRMED

POL-CAT-004 — Turning a catalogue flag on / تشغيل مفتاح قدرة للكتالوج
  Statement : When a catalogue capability flag is turned on, the system shall make the catalogue functions bound to it available to existing items without re-setup or data migration.
  Statement (ar): عند تشغيل مفتاح قدرة خاص بالكتالوج، يجب على النظام أن يتيح وظائف الكتالوج المرتبطة به للأصناف القائمة دون إعادة إعداد أو ترحيل بيانات.
  Pattern   : event
  Trigger   : Flag change (on)
  Rationale : Presets first, then customise; flags may be turned on at any time.
  Source    : KB:raw-idea.md §6.2, §6.8 "No data migration, no mode switch"
  Status    : CONFIRMED

POL-CAT-005 — Turning a catalogue flag off while data depends on it / إيقاف مفتاح قدرة تعتمد عليه بيانات
  Statement : If a user attempts to turn off a catalogue capability flag while catalogue data depends on it, then the system shall refuse the change and offer the user a next action.
  Statement (ar): إذا حاول مستخدم إيقاف مفتاح قدرة خاص بالكتالوج بينما توجد بيانات كتالوج تعتمد عليه، فيجب على النظام أن يرفض التغيير وأن يعرض على المستخدم الإجراء التالي.
  Pattern   : unwanted
  Trigger   : Flag change (off)
  Rationale : Turning a flag off is blocked while data depends on it, and no error is a dead end.
  Source    : KB:raw-idea.md §6.2, §6.5
  Status    : CONFIRMED

POL-CAT-006 — Minimal item creation / إنشاء صنف بأقل المدخلات
  Statement : The system shall let a user create an item by completing at most four mandatory entries.
  Statement (ar): يجب على النظام أن يتيح للمستخدم إنشاء صنف بإكمال أربعة مدخلات إلزامية على الأكثر.
  Pattern   : ubiquitous
  Trigger   : Create
  Rationale : Simplicity is a measurable requirement; setup plus the first invoice takes at most 30 minutes.
  Source    : KB:raw-idea.md §3 D9, §6.1, §6.10
  Status    : CONFIRMED

POL-CAT-007 — Item import in the setup wizard / استيراد الأصناف في معالج الإعداد
  Statement : When the owner reaches the item-import step of the setup wizard, the system shall import the tenant's items from an Excel file.
  Statement (ar): عند وصول المالك إلى خطوة استيراد الأصناف في معالج الإعداد، يجب على النظام أن يستورد أصناف المستأجر من ملف Excel.
  Pattern   : event
  Trigger   : Import
  Rationale : The fifth setup step brings an existing item list in without re-typing it.
  Source    : KB:raw-idea.md §6.9
  Status    : CONFIRMED

POL-CAT-008 — Publish state controls storefront visibility / حالة النشر تحكم الظهور في المتجر
  Statement : While an item is unpublished, the system shall keep the item out of the storefront.
  Statement (ar): أثناء كون الصنف غير منشور، يجب على النظام أن يُبقي الصنف خارج المتجر الإلكتروني.
  Pattern   : state
  Trigger   : Publish / Unpublish
  Rationale : The catalogue is shared with the storefront, so the trader decides what is offered online.
  Source    : KB:raw-idea.md §5 #1 "publish state"
  Status    : CONFIRMED

POL-CAT-009 — Items in the one global search / الأصناف في البحث الشامل
  Statement : The system shall make every item findable through the one global search.
  Statement (ar): يجب على النظام أن يجعل كل صنف قابلًا للعثور عليه من خلال البحث الشامل الموحّد.
  Pattern   : ubiquitous
  Trigger   : Search
  Rationale : One global search covers items, customers and documents.
  Source    : KB:raw-idea.md §6.6
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
None. Standard values apply.

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Pre-assembled kits (bill of materials, assembly into stock) | Bundles are sold as one line and stock moves on their components; nothing is assembled into stock | A decision to bring manufacturing into scope | KB §3 D1, §9; OI-09 |
| Activity-specific catalogue models (pharmacy drug data, restaurant menus/recipes, gold karat pricing, vehicle VIN) | Not modelled in the catalogue | A new activity enters scope (one matrix column + one conformance scenario) | KB §3 D1, §7 |
| Multi-vendor catalogue (marketplace) | One trader's catalogue per tenant | Marketplace enters scope | KB §9 |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| OI-05 | Where are the flags POL-CAT-002…005 read from | The FeatureFlags facade over given `cu`. CAT contributes the flag-off guard for `variants` and `bundles` | Round-1 recommendation; confirmed at convergence | KB §4.1, §6.2; manifest `cu` |
| OI-12 | Item name languages | Arabic name mandatory, English optional. A standard language rule applied by P0.5 from the profile language policy, so it is not written as a POL | Round-1 recommendation; confirmed at convergence | profile `ar, en`; KSA VAT Implementing Regulations Art. 53 |

PLATFORM FINDINGS   (§7 — obligations outside CAT that a CAT policy or artifact depends on; each row stays OPEN until its owner closes it. They are recorded here because the project registry's platform-findings category is outside this module's writable set; `gov.py state` copies them there.)
| # | Finding | Owner of the fix | Evidence | Status |
|---|---|---|---|---|
| 1 | `gov.py analyze` C6.15 (db-column-only) must exempt, explicitly, the given tables that the erp-core checklist C1 names. In P2 repair round 1 it refused `FK_<TABLE>_TENANT → CORE_TENANT(ID)` as a cross-module constraint, and ADR-CAT-054 removed the 12 FKs in response. C1 requires that FK on every table, and the erp-core conventions (Migrations rule 5) allow exactly that one. With the FKs restored, the current run examines 0 subjects for C6.15, so the exemption is only implicit and a later run could refuse the FK again. | factory / `gov.py` | ADR-CAT-055; db-script BLOCK 5e (12 constraints); erp-core checklist C1; erp-core conventions § Migrations rule 5 | OPEN |
| 2 | A write that turns the capability flag `variants` or `bundles` off must first call `CatalogueReadApi.checkCapabilityTurnOff(flag)`, and must refuse the write, showing the returned message and dependent Items, when `allowed` is false. CAT only answers the check; no flag writer is obliged to call it, so POL-CAT-005 ("the system shall refuse the change") is not enforced anywhere yet. | erp-core `cu` / the platform shell that owns the flag-settings screen (flag owner OPEN, P-02) | POL-CAT-005; CON-CAT-017; REQ-CAT-021, REQ-CAT-022, REQ-CAT-023; ADR-CAT-041; ADR-CAT-017; contract §0 (obligation of the flag writer) | OPEN |
| 3 | The Host data parser of `gov.py analyze` finds no subject. C10.8 (tc-data) and C10.9 (tc-consistency) report 0 subjects, although at least 10 TCs carry `Host data` and `Test data` lines (TC-CAT-019, 036, 037, 063, 068, 120, 121, 122, 137, 139, 175). The ADR-CAT-053 repair is therefore unproven mechanically. Analyze must be re-run until both clauses report a non-zero count, before the gate can APPROVE. | `gov.py` | analyze-all report "12 clause(s) examined nothing"; backend-test-plan-cat.md and frontend-test-plan-cat.md Host data lines; ADR-CAT-052 §3, ADR-CAT-053 | RESOLVED (gate-analysis round 2, G9). The cause was in CAT, not the parser: the SRS lookups block did not list the host values in `values`, and the Host data lines named no verb or path. Both are fixed (SRS A6; TC-CAT-019, 036, 037, 068, 137, 139 name the mdl `POST /api/v1/mdl/lookup-types/{id}/values` / `DELETE /api/v1/mdl/lookup-values/{id}` calls). The re-run reports C10.8 = 11 and C10.9 = 9 subjects, with no finding. |
| 4 | The erp-core manifest publishes no in-process interface for the `common` capability `owned-lookups` (`com.erp.common.lookup.OwnedLookups`). CAT's given-usage row for API-CAT-052 must therefore name the mdl interface the call goes through (`com.erp.mdl.crossmodule.MdlLookupApi`), not the helper that checklist C6 and mdl.md conventions §Lookups rule 4 require. The manifest should list `OwnedLookups` under `modules.common`, so that a module serving its own keys can cite it directly. | erp-core (manifest of `modules.common`) | backend plan PHASE CORE given-usage row "modules.mdl, through common `owned-lookups`"; SRS A9; ADR-CAT-065; erp-core checklist C6 | OPEN |
| 5 | erp-core gives no pattern for authorizing a cross-module read API (`com.erp.<mod>.crossmodule.*`) whose callers hold no permission of the owning module — a storefront shopper (SFR), a cashier (SLS), a system job (INV, PUR, TAX). Checklist C13 requires `@PreAuthorize` on every service method and states no exception, so CAT's `CatCatalogueReadService` carries none while the HTTP facades keep the screen permission and the calling module authorizes its own caller. The gap is raised as `CORE-GAP-in-process-authorization.md`, which asks the core to choose one of three options: (1) a core-provided caller authority (for example `CROSSMODULE_READ`) granted to in-process calls and checked by a `crossmodule` read service; (2) a documented exception to C13 for read-only services reached only through a `crossmodule` interface, enforced by `ModuleBoundaryRules`; (3) a core helper that runs an in-process read as a system principal on behalf of the calling module. Until the core answers, ADR-CAT-061 holds only conditionally. | erp-core | erp-core checklist C13 and F9; ADR-CAT-061; ADR-CAT-066; decisions/CAT/CORE-GAP-in-process-authorization.md (the three options above); backend plan PHASE CORE "Core gaps" | OPEN |
| 6 | erp-core 1.6.0 lets a module register lookup types only by migration, with flat values that other tables reference by code. CAT's Variant attributes are types the tenant creates at runtime, each with its own values, referenced by foreign key from Variant assignments, so they stay CAT tables — a C5 / F3 deviation justified in ADR-CAT-070 and raised as `CORE-GAP-tenant-lookup-types.md`. | erp-core | erp-core checklist C5, F3 and F9; ADR-CAT-070; decisions/CAT/CORE-GAP-tenant-lookup-types.md | OPEN |
| 7 | `NumberSeriesApi` allocates one number per call, so the item import draws up to 10,000 codes one by one (ADR-CAT-071). A block allocation would replace them with one call per batch. Raised as `CORE-GAP-number-series-block-allocation.md`. | erp-core | erp-core checklist F9; ADR-CAT-071; API-CAT-049; decisions/CAT/CORE-GAP-number-series-block-allocation.md | OPEN |
| 8 | A revise re-renders only the artifacts it edits, so the header counts and next-id lines of untouched downstream artifacts go stale (the contract said REQ 127, the db-script and registry-db said RULE 49 and next free ADR 068, while the registries said REQ 138, RULE 53 and ADR 072). The revise checklist needs one more step: after the findings are applied, re-stamp the Inputs, Counts and next-id lines of every downstream artifact (contract, db-script, registry-db, backend and frontend plans and their registries, test plans) from the current registries. Until the checklist carries the step, CAT applies it by hand: the fourth gate-analysis revise re-stamped those lines (REQ 139 · AC 161 · RULE 53 · next free ADR 072), the sixth re-stamped them again (REQ 139 · AC 164 · RULE 53 · next free ADR 073), the seventh re-stamped them after splitting three REQs (REQ 147 · AC 164 · RULE 53 · next free ADR 076), and the eighth re-stamped the next free ADR to 077. | factory (revise checklist / `gov.py`) | gate-analysis round 4, G5; contract-cat.md, db-script-cat.md, registry-db-cat.md headers | OPEN |
| 9 | Profile check CM-1 requires every entity of kind master to carry the master defaults code, nameAr and nameEn. CAT's dependent masters — ENT-CAT-005 (Variant attribute assignment), ENT-CAT-007 (Bundle component) and ENT-CAT-008 (Item unit) — carry none of the three: they are identified through their parent and show the parent's or the referenced record's names. CM-1 passes today only through the reading recorded in ADR-CAT-039 ("dependent records take their parent's kind and carry the audit fields and isActiveFl"), which no check can apply mechanically. The commerce profile's `conventions.entity_defaults` should state that a dependent master (one whose identity is its parent plus a business key among the parent's active lines) takes its parent's identity defaults, so that CM-1 can tell a dependent master apart and pass it without a hand-written ADR. ADR-CAT-039 stays in force until then. The seventh gate-analysis revise (G8) confirmed it: CAT makes no change while ADR-CAT-039 stands, and the finding stays OPEN with the factory. | factory (profile `commerce` — `conventions.entity_defaults`; CM-1) | gate-analysis round 6, G6; gate-analysis round 7, G8; ADR-CAT-039; SRS A3 (ENT-CAT-005, 007, 008); profile CM-1 | OPEN |
| 10 | erp-core 1.6.0 publishes only `MdlLookupApi.readActiveValuesByKey`. A module that keeps a lookup code on its records after the value is deactivated (REQ-CAT-076) cannot read that value's ar / en label, so every CAT screen renders such a value as its raw code (ADR-CAT-073). Raised as `CORE-GAP-lookup-inactive-value-label.md`, which asks for a read of a key's values by code, active or not. When it is answered, API-CAT-052 gains an optional `codes` parameter and `LookupOption` gains `isActiveFl`. | erp-core (`modules.mdl`) | gate-analysis round 7, G1; ADR-CAT-073; API-CAT-052; UXD-CAT-001; decisions/CAT/CORE-GAP-lookup-inactive-value-label.md | OPEN |
| 11 | erp-core 1.6.0 publishes only `isAvailable`, `publicUrl` and `publicUrls` on `FileDocumentLookupApi`, and no in-process read of an uploaded document's content type, size, visibility or bytes. CAT therefore cannot check an image's type and size itself (that would also rebuild the image validator `modules.file` forbids), and cannot read the xlsx of an item import. The image checks move to the FILE category `CAT_ITEM_IMAGE` (allowPublic = true, PNG / JPEG / WEBP, 5 MB) that CAT registers, enforced at upload (ADR-CAT-076); API-CAT-018 keeps only availability, a public URL and the 20-image count. The import's byte read and type / size check (RULE-CAT-044, API-CAT-049) wait on the core. Raised as `CORE-GAP-file-document-metadata-read.md`, which also asks for the supported way to register a module's FILE categories for every tenant. | erp-core (`modules.file`) | gate-analysis round 8, G1; erp-core conventions §Files rule 3; `modules/file.md` (Do not rebuild: an image validator); ADR-CAT-056, ADR-CAT-076; RULE-CAT-038, RULE-CAT-044; API-CAT-018, API-CAT-049; decisions/CAT/CORE-GAP-file-document-metadata-read.md | OPEN |
══════════════════════════════════════════════════════════════════

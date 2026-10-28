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
| 3 | The Host data parser of `gov.py analyze` finds no subject. C10.8 (tc-data) and C10.9 (tc-consistency) report 0 subjects, although at least 10 TCs carry `Host data` and `Test data` lines (TC-CAT-019, 036, 037, 063, 068, 120, 121, 122, 137, 139, 175). The ADR-CAT-053 repair is therefore unproven mechanically. Analyze must be re-run until both clauses report a non-zero count, before the gate can APPROVE. | `gov.py` | analyze-all report "12 clause(s) examined nothing"; backend-test-plan-cat.md and frontend-test-plan-cat.md Host data lines; ADR-CAT-052 §3, ADR-CAT-053 | OPEN |
══════════════════════════════════════════════════════════════════

## BUSINESS POLICIES — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module   : INV     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-INV-001 — One stock across channels / مخزون واحد لجميع القنوات
  Statement : The system shall keep a single stock per warehouse that back-office documents, the point of sale and the storefront all draw from.
  Statement (ar) : يجب على النظام أن يحتفظ بمخزون واحد لكل مستودع تسحب منه مستندات المكتب الخلفي ونقطة البيع والمتجر الإلكتروني جميعًا.
  Pattern   : ubiquitous
  Trigger   : Stock move (any channel)
  Rationale : The product promise is one catalogue, one stock and one customer base across back-office, POS and storefront.
  Source    : KB:raw-idea.md §1 "one catalogue, one stock, one customer base"
  Status    : CONFIRMED

POL-INV-002 — Warehouse on every stock move / المستودع في كل حركة مخزون
  Statement : The system shall record the warehouse on every stock move, including while the tenant has only one warehouse.
  Statement (ar) : يجب على النظام أن يسجّل المستودع في كل حركة مخزون، حتى عندما يكون لدى المستأجر مستودع واحد فقط.
  Pattern   : ubiquitous
  Trigger   : Create (stock move)
  Rationale : A second warehouse can be added later without data migration.
  Source    : KB:raw-idea.md §6.8 "every stock move carries warehouse_id"; DP §5 G8
  Status    : CONFIRMED

POL-INV-003 — Default warehouse at start / مستودع افتراضي عند البدء
  Statement : When a tenant is provisioned, the system shall create one default warehouse so that stock can be received without any warehouse setup.
  Statement (ar) : عند إنشاء مستأجر جديد، يجب على النظام أن ينشئ مستودعًا افتراضيًا واحدًا بحيث يمكن استلام المخزون دون أي إعداد للمستودعات.
  Pattern   : event
  Trigger   : Tenant provisioning
  Rationale : The platform starts with one warehouse and nothing is imposed on the user.
  Source    : KB:raw-idea.md §6.8 "one warehouse/branch/currency/price list to start"; DP §5 G8
  Status    : CONFIRMED

POL-INV-004 — Hidden warehouse selector while one warehouse / إخفاء محدد المستودع عند وجود مستودع واحد
  Statement : While the tenant has exactly one active warehouse, the system shall hide the warehouse selector, the inter-warehouse transfer action and the warehouse report filters.
  Statement (ar) : أثناء امتلاك المستأجر مستودعًا نشطًا واحدًا فقط، يجب على النظام أن يخفي محدد المستودع وإجراء التحويل بين المستودعات ومرشحات المستودع في التقارير.
  Pattern   : state
  Trigger   : View (any inventory screen or report)
  Rationale : Only what is meaningful is shown, with no dead menus.
  Source    : KB:raw-idea.md §6.3, §6.8; DP §5 G8
  Status    : CONFIRMED

POL-INV-005 — Reveal on second warehouse / الإظهار عند إضافة مستودع ثانٍ
  Statement : When a second warehouse is added, the system shall reveal the warehouse selector, the inter-warehouse transfer action and the warehouse report filters without data migration or re-setup.
  Statement (ar) : عند إضافة مستودع ثانٍ، يجب على النظام أن يُظهر محدد المستودع وإجراء التحويل بين المستودعات ومرشحات المستودع في التقارير دون ترحيل للبيانات ودون إعادة إعداد.
  Pattern   : event
  Trigger   : Create (warehouse)
  Rationale : Multi-warehouse appears automatically when the business grows.
  Source    : KB:raw-idea.md §6.8 "reveals it — plus the related actions (e.g. inter-warehouse transfer) and report filters — automatically"
  Status    : CONFIRMED

POL-INV-006 — Lot and expiry on receipt / التشغيلة والصلاحية عند الاستلام
  Statement : Where the lotExpiry capability flag is on, the system shall capture a lot and its expiry date on every stock receipt of an item tracked by lot.
  Statement (ar) : حيثما كان مفتاح القدرة lotExpiry مفعّلًا، يجب على النظام أن يسجّل التشغيلة وتاريخ صلاحيتها في كل استلام مخزون لصنف يُتتبَّع بالتشغيلة.
  Pattern   : optional
  Trigger   : Receive (stock receipt)
  Rationale : Grocery, cleaning-supplies, feed and produce presets need lot and expiry.
  Source    : KB:raw-idea.md §4.1 (`lotExpiry`: "lot + expiry on receipt"), §4.2
  Status    : CONFIRMED

POL-INV-007 — FEFO issue / الصرف بالأقرب انتهاءً
  Statement : Where the lotExpiry capability flag is on, the system shall issue stock of items tracked by lot first-expiry-first-out (FEFO).
  Statement (ar) : حيثما كان مفتاح القدرة lotExpiry مفعّلًا، يجب على النظام أن يصرف مخزون الأصناف المتتبَّعة بالتشغيلة وفق مبدأ الأقرب انتهاءً يُصرف أولًا (FEFO).
  Pattern   : optional
  Trigger   : Issue (stock issue)
  Rationale : Stock nearest to expiry leaves first, which reduces write-offs.
  Source    : KB:raw-idea.md §4.1 (`lotExpiry`: "FEFO issue")
  Status    : CONFIRMED

POL-INV-008 — Near-expiry reporting / تقرير قرب انتهاء الصلاحية
  Statement : Where the lotExpiry capability flag is on, the system shall report the stock that is approaching its expiry date.
  Statement (ar) : حيثما كان مفتاح القدرة lotExpiry مفعّلًا، يجب على النظام أن يقدّم تقريرًا بالمخزون الذي يقترب من تاريخ انتهاء صلاحيته.
  Pattern   : optional
  Trigger   : Report
  Rationale : Traders act on stock close to expiry before it is lost.
  Source    : KB:raw-idea.md §4.1 (`lotExpiry`: "near-expiry reports")
  Status    : CONFIRMED

POL-INV-009 — Dual quantity stock / مخزون بالكمية المزدوجة
  Statement : Where the dualQuantity capability flag is on, the system shall track the stock of dual-quantity items in both a count and a measure of weight or length.
  Statement (ar) : حيثما كان مفتاح القدرة dualQuantity مفعّلًا، يجب على النظام أن يتتبّع مخزون الأصناف ذات الكمية المزدوجة بالعدد وبالقياس (الوزن أو الطول) معًا.
  Pattern   : optional
  Trigger   : Stock move (any)
  Rationale : Fabrics (metre), building materials and steel, and produce or meat by weight are stocked in two quantities.
  Source    : KB:raw-idea.md §4.1 (`dualQuantity`: "count + weight/length"), §4.2
  Status    : CONFIRMED

POL-INV-010 — Flag turn-off blocked when data depends on it / منع إيقاف المفتاح عند وجود بيانات تعتمد عليه
  Statement : If a user turns off the lotExpiry or dualQuantity capability flag and stock data depends on it, then the system shall block the change and show which data depends on it.
  Statement (ar) : إذا أوقف مستخدم مفتاح القدرة lotExpiry أو dualQuantity وكانت بيانات مخزون تعتمد عليه، فيجب على النظام أن يمنع التغيير وأن يبيّن البيانات التي تعتمد عليه.
  Pattern   : unwanted
  Trigger   : Update (capability flag)
  Rationale : A flag may be turned on at any time, but never off when data depends on it, and no error is a dead end.
  Source    : KB:raw-idea.md §6.2, §6.5; DP §5 G3
  Status    : CONFIRMED

POL-INV-011 — Flags only, never the activity / المفاتيح فقط وليس النشاط
  Statement : The system shall vary inventory behaviour only by capability flags and never by the tenant's declared activity.
  Statement (ar) : يجب على النظام أن يغيّر سلوك المخزون وفق مفاتيح القدرة فقط، وألّا يعتمد أبدًا على النشاط التجاري المعلن للمستأجر.
  Pattern   : ubiquitous
  Trigger   : Any inventory operation
  Rationale : One codebase serves every activity. The activity is only a preset that sets flags.
  Source    : KB:raw-idea.md §3 D2, §7; DP §5 G1
  Status    : CONFIRMED

POL-INV-012 — Mobile stock counting / الجرد عبر الهاتف المحمول
  Statement : The system shall let a user perform a stock count from a mobile-sized responsive web screen.
  Statement (ar) : يجب على النظام أن يتيح للمستخدم إجراء جرد المخزون من شاشة ويب متجاوبة بحجم الهاتف المحمول.
  Pattern   : ubiquitous
  Trigger   : Count (stock count)
  Rationale : Counting is a mobile-first activity, and there is no native app in v1.
  Source    : KB:raw-idea.md §6.7 "Mobile-first for selling, collecting, counting", §9
  Status    : CONFIRMED

POL-INV-013 — Warehouse role preset / الدور المسبق «المستودع»
  Statement : The system shall provide a Warehouse role preset that grants access to the inventory functions.
  Statement (ar) : يجب على النظام أن يوفّر دورًا مسبق الإعداد باسم «المستودع» يمنح الوصول إلى وظائف المخزون.
  Pattern   : ubiquitous
  Trigger   : Tenant provisioning / role assignment
  Rationale : Three role presets (Owner, Seller/Cashier, Warehouse) let a new tenant start without designing roles.
  Source    : KB:raw-idea.md §3 D8; registry §2.4 D8
  Status    : CONFIRMED

POL-INV-014 — Bundle issue moves component stock / صرف الحزمة يحرّك مخزون مكوّناتها
  Statement : Where the bundles capability flag is on, the system shall issue the stock of a bundle by moving the stock of its component items.
  Statement (ar) : حيثما كان مفتاح القدرة bundles مفعّلًا، يجب على النظام أن يصرف مخزون الحزمة بتحريك مخزون الأصناف المكوِّنة لها.
  Pattern   : optional
  Trigger   : Issue (bundle)
  Rationale : A bundle holds no stock of its own, so its availability always follows its components and no assembly step is needed.
  Source    : Dialogue resolution INV-P06; KB:raw-idea.md §4.1 (`bundles`), §3 D1, §9
  Status    : CONFIRMED

POL-INV-015 — Default branch at start / فرع افتراضي عند البدء
  Statement : When a tenant is provisioned, the system shall create one default branch so that documents can be issued without any branch setup.
  Statement (ar) : عند إنشاء مستأجر جديد، يجب على النظام أن ينشئ فرعًا افتراضيًا واحدًا بحيث يمكن إصدار المستندات دون أي إعداد للفروع.
  Pattern   : event
  Trigger   : Tenant provisioning
  Rationale : The platform starts with one branch and nothing is imposed on the user.
  Source    : Dialogue resolution INV-P07; KB:raw-idea.md §6.8 "one warehouse/branch/currency/price list to start"; DP §5 G8
  Status    : CONFIRMED

POL-INV-016 — Hidden branch selector while one branch / إخفاء محدد الفرع عند وجود فرع واحد
  Statement : While the tenant has exactly one active branch, the system shall hide the branch selector and the branch report filters.
  Statement (ar) : أثناء امتلاك المستأجر فرعًا نشطًا واحدًا فقط، يجب على النظام أن يخفي محدد الفرع ومرشحات الفرع في التقارير.
  Pattern   : state
  Trigger   : View (any screen or report with a branch)
  Rationale : Only what is meaningful is shown, with no dead menus.
  Source    : Dialogue resolution INV-P07; KB:raw-idea.md §6.3, §6.8; DP §5 G8
  Status    : CONFIRMED

POL-INV-017 — Reveal on second branch / الإظهار عند إضافة فرع ثانٍ
  Statement : When a second branch is added, the system shall reveal the branch selector and the branch report filters without data migration or re-setup.
  Statement (ar) : عند إضافة فرع ثانٍ، يجب على النظام أن يُظهر محدد الفرع ومرشحات الفرع في التقارير دون ترحيل للبيانات ودون إعادة إعداد.
  Pattern   : event
  Trigger   : Create (branch)
  Rationale : Multi-branch appears automatically when the business grows.
  Source    : Dialogue resolution INV-P07; KB:raw-idea.md §6.8 "reveals it … automatically"
  Status    : CONFIRMED

POL-INV-018 — Optional block on negative stock / منع الرصيد السالب اختياريًا
  Statement : Where the tenant disallows negative stock, the system shall block an online stock issue that would take a warehouse balance below zero.
  Statement (ar) : حيثما منع المستأجر الرصيد السالب، يجب على النظام أن يحظر أي صرف مخزون متصل بالإنترنت يؤدي إلى هبوط رصيد المستودع دون الصفر.
  Pattern   : optional
  Trigger   : Issue (online stock issue)
  Rationale : A tenant that wants strict balances can switch blocking on; the default allows negative stock so that a new owner is never stopped before opening stock is loaded.
  Source    : Dialogue resolution INV-P08; KB:raw-idea.md §6.5, §6.10; DP §5 G7
  Status    : CONFIRMED

POL-INV-019 — Alert on negative stock / تنبيه عند الرصيد السالب
  Statement : If a stock issue takes a warehouse balance below zero, then the system shall alert the Warehouse role.
  Statement (ar) : إذا أدّى صرف مخزون إلى هبوط رصيد مستودع دون الصفر، فيجب على النظام أن ينبّه دور «المستودع».
  Pattern   : unwanted
  Trigger   : Issue (stock issue)
  Rationale : A negative balance is allowed but never silent.
  Source    : Dialogue resolution INV-P08; KB:raw-idea.md §6.5
  Status    : CONFIRMED

POL-INV-020 — Offline POS sales always accepted / قبول مبيعات نقطة البيع دون اتصال دائمًا
  Statement : If a synchronised offline point-of-sale sale takes a warehouse balance below zero, then the system shall accept the sale.
  Statement (ar) : إذا أدّى بيع من نقطة البيع نُفّذ دون اتصال ثم تمت مزامنته إلى هبوط رصيد مستودع دون الصفر، فيجب على النظام أن يقبل عملية البيع.
  Pattern   : unwanted
  Trigger   : Synchronise (offline sale)
  Rationale : The goods have already left, so rejecting the sale would lose a real transaction.
  Source    : Dialogue resolution INV-P08; KB:raw-idea.md §5 #6 (POS offline-capable)
  Status    : CONFIRMED

POL-INV-021 — Weighted-average stock valuation / تقييم المخزون بالتكلفة المتوسطة المرجّحة
  Statement : The system shall value stock at the perpetual weighted-average cost of each variant across the tenant.
  Statement (ar) : يجب على النظام أن يقيّم المخزون بالتكلفة المتوسطة المرجّحة المستمرة لكل متغيّر صنف على مستوى المستأجر.
  Pattern   : ubiquitous
  Trigger   : Receive / Issue (stock move)
  Rationale : Weighted average is permitted by the accounting standards, is the simplest for a small trader and stays stable when stock moves between warehouses.
  Source    : Dialogue resolution INV-P09; IAS 2 ¶25 (not live-verified)
  Status    : CONFIRMED

POL-INV-022 — Expired lots excluded from sale / استبعاد التشغيلات المنتهية من البيع
  Statement : If a lot has passed its expiry date, then the system shall exclude it from issue for sale.
  Statement (ar) : إذا تجاوزت تشغيلة تاريخ صلاحيتها، فيجب على النظام أن يستبعدها من الصرف للبيع.
  Pattern   : unwanted
  Trigger   : Issue (sale)
  Rationale : Selling expired goods is prohibited; the lot stays in stock until a stock adjustment writes it off.
  Source    : Dialogue resolution INV-P10; KB:raw-idea.md §4.1, §4.2; consumer-protection practice (not live-verified)
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
None — standard values apply.

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Bundle assembly and disassembly (stock of pre-built bundles) / تجميع الحزم وتفكيكها | Assembly orders are light manufacturing, which is excluded. Bundles are exploded into component stock moves | A later version that admits manufacturing scope | KB §3 D1, §9; dialogue INV-P06 |
| Native mobile counting app / تطبيق جرد أصلي للهاتف | Counting runs on responsive web only in v1 | A version that admits native apps | KB §6.7, §9 |
| FIFO cost layers / طبقات التكلفة بالوارد أولًا | v1 values stock at weighted-average cost only | A later version, if a customer requires FIFO | dialogue INV-P09 |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| INV-P06 | Bundle stock | Explode to component moves; no bundle stock. Adds POL-INV-014 | Converged in dialogue (round 2); user confirms at the gate | DP §8 P-06; KB §3 D1, §9 |
| INV-P07 | Branch ownership | INV owns Branch; a default branch is seeded and its selector is hidden while there is one. Adds POL-INV-015, POL-INV-016, POL-INV-017 | Converged in dialogue (round 2); user confirms at the gate | KB §6.8; DP §5 G8 |
| INV-P08 | Negative stock | Tenant setting, default allowed with a Warehouse alert; online issues blocked when the tenant disallows it; synced offline POS sales always accepted. Adds POL-INV-018, POL-INV-019, POL-INV-020 | Converged in dialogue (round 2); user confirms at the gate | KB §5 #6, §6.5, §6.10; DP §5 G7 |
| INV-P09 | Costing method | Perpetual weighted-average cost per Variant across the tenant. Adds POL-INV-021 | Converged in dialogue (round 2); user confirms at the gate | IAS 2 ¶25 (not live-verified) |
| INV-P10 | Expired lots | Excluded from issue for sale; removed only by a stock adjustment. Adds POL-INV-022 | Converged in dialogue (round 2); user confirms at the gate | KB §4.1 |

PLATFORM FINDINGS   (obligations outside INV that an INV artifact depends on; each row stays OPEN until its owner closes it. They are recorded here because the project registry's platform-findings category is outside this module's writable set; `gov.py state` copies them there.)
| # | Finding | Owner of the fix | Evidence | Status |
|---|---|---|---|---|
| 1 | contract-cat names no key for its unit-of-measure lookup. INV stores a code of that lookup in ENT-INV-014.measureUomCode (SRS A6 "Lookups consumed") and validates it through `MdlLookupApi`, so it needs the key. contract-cat v2 does not publish it; CAT's own SRS A6 declares `CAT_UOM`. INV seeds the SettingsApi setting INV_MEASURE_UOM_KEY with `CAT_UOM` meanwhile. CAT's next contract version should publish the key (for example in §0 "Conventions" or with CON-CAT-001 `baseUomCode`), so INV confirms the seed against the contract. | CAT (next contract version) | INV SRS A6 "Lookups consumed", D-04; contract-cat v2 (no key named); srs-cat v2 A6 `CAT_UOM`; ADR-INV-043; gate-analysis INV revise 1 G4 | OPEN |
| 2 | CAT v2 publishes no exact read of a Variant by its code. contract-cat offers getVariant by identifier (CON-CAT-008), lookupBarcode (CON-CAT-010) and searchCatalogue (CON-CAT-016, Items only, contains-match, VIEW-gated). INV's opening stock sheet therefore names a Variant by barcode only in v1. CAT should publish an operation such as `findVariantByCode(code) → {variantPk, itemPk, isActiveFl} · errors: not found`; an INV delta then resolves a sheet row through the barcode first and the code second, and restores the "Variant code or barcode" column. | CAT (next contract version) · INV (delta after CAT publishes) | INV SRS D-13, REQ-INV-073, AC-INV-080, RULE-INV-028; ADR-INV-035; backend plan API-INV-047, XM-INV-013 / 014; gate-analysis INV revise 1 G12 | OPEN |
| 3 | Every business module lives under the application root `com.commerce` (platform summary decision 11 of the PTY run, ADR-PTY-047). INV now declares `com.commerce.inv`. CAT v2 still declares `com.erp.cat`, including its crossmodule bean that INV's INT-C adapters inject; INV references that bean as CAT delivers it until CAT's rename. This is the same finding as business-policies-pty PLATFORM FINDINGS row 1, cited here, not a new one. | CAT (next pass) | business-policies-pty PLATFORM FINDINGS row 1; ADR-PTY-047; INV backend plan CORE, INT-C; gate-analysis INV revise 1 G22 | OPEN (tracked under PTY row 1) |
| 4 | The core states no authority model for in-process calls. INV's `InventoryApi` (CON-INV-006 … 019) delegates to internal engines that check no INV permission, because the calling module authorises its own caller (a POS cashier holds only PERM_INV_STOCK_BALANCES_VIEW). The core should publish how a module's in-process operation learns, or asserts, the authority of the calling module's user (CORE-GAP-in-process-caller-authority). No `InventoryApi` signature changes when it does. | core | ADR-INV-042; INV backend plan SVC-API-INT "Authority", SEC-BE; gate-analysis INV revise 2 G15 | OPEN (tracked with PTY's gap on the same core point) |
| 5 | The core lookup module offers no way to mark a seeded value as one a tenant cannot deactivate. INV's open key INV_ADJUSTMENT_REASON seeds COUNT_DIFFERENCE and OPENING_STOCK, which the system writes itself; INV now skips the tenant-active check for the adjustments it generates, so a deactivated value no longer blocks a count or an opening stock load, but a tenant can still hide the value from its own lists and reports. `mdl` should let the owner module mark seeded values as system values that stay active (for example a flag on the seeded value), and INV would then mark these two. | core (`mdl`) | INV SRS RULE-INV-033, A6; ADR-INV-047; gate-analysis INV revise 2 G3, G15 | OPEN |
══════════════════════════════════════════════════════════════════

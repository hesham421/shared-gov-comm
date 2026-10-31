# UI/UX spec — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Stage : P3.2 Part A (UX design)   Profile : commerce
Inputs : srs-inv (PART A fields and rules, PART B SCR-REQ-INV-001 … 012, access summary), prd-inv, registry-srs
Counts : SCR 12 (SCR-INV-001 … 012) · UXD 11 (UXD-INV-001 … 011) · ADR raised: ADR-INV-033 … ADR-INV-036
══════════════════════════════════════════════════════════════════

## Conventions for every screen

- **Context on load.** Every screen reads the inventory context: active warehouse and branch counts and the
  capability flags `lotExpiry`, `dualQuantity` and `bundles`. Flags are read through the FeatureFlags API
  (REQ-INV-132), never from the activity.
  - While there is exactly one active warehouse, the warehouse selector, the warehouse filter and the warehouse
    column are hidden (REQ-INV-010, REQ-INV-012), and the transfer entry and the transfer action are hidden
    (REQ-INV-011).
  - While there is exactly one active branch, the branch selector, the branch filter and the branch column are
    hidden (REQ-INV-029, REQ-INV-030).
  - With `lotExpiry` off, lot fields, the lot screen and the near-expiry view are hidden (REQ-INV-134). With
    `dualQuantity` off, measure fields are hidden (REQ-INV-135).
  - When a count rises to two, the hidden controls appear with no re-setup (REQ-INV-015, REQ-INV-032).
- **Progressive disclosure.** Notes and other fields that the SRS marks "Advanced" sit behind an "Advanced"
  disclosure, labelled «متقدّم» / "Advanced".
- **Language.** Every label and message is in Arabic and English. Arabic is the default reading direction (RTL).
  A name field shown in English falls back to `nameAr` when `nameEn` is empty.
- **Permissions.** Names follow `PERM_<PAGE_CODE>_<ACTION>`, with `VIEW` as the gateway. Each screen's rows below
  are copied from SRS B4 and the SRS access summary. Nothing is added.
- **States.** Every screen has the same four states:
  - empty: a search with no rows shows «لا توجد نتائج.» / "No results." and the screen's primary action, when the
    user may use it.
  - loading: the result area keeps its frame and shows a busy indicator. No full-page blocking.
  - error: a refusal shows its localized catalogue message. Field refusals appear inline, and the rest in the
    message area. Part B binds each catalogue code.
  - offline: no INV screen is offline-capable. The SRS asks for none (offline selling is SLS's POS). A lost
    connection shows the generic network error with a retry.
- **Composition.** One screen, one job, one submit (A.4). A `drawer` container keeps the record in an entry
  drawer over the search. A `page` container uses routes `/new` and `/:id`, keeps the header inline and keeps
  child lines in a pane beneath it. A line is added or changed in a one-line drawer.

## Screens

## SCR-INV-001 — المستودعات / Warehouses                 traces=REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-015,REQ-INV-029,REQ-INV-030,REQ-INV-031,AC-INV-003,AC-INV-004,AC-INV-005,AC-INV-006,AC-INV-007,AC-INV-008,AC-INV-009,AC-INV-010,AC-INV-011,AC-INV-012,AC-INV-018,AC-INV-034,AC-INV-035,AC-INV-036
UI pattern        : Search + Entry — flat record (SCR-REQ-INV-001)
Sub-views         : Search · Entry under this ONE SCR
Fields shown      : Search filters — code «رمز المستودع» / "Warehouse code"; name «اسم المستودع» / "Warehouse name"; branch «الفرع» / "Branch" (hidden while one active branch); active state «الحالة» / "Active state" (active only / all). Result columns — code; nameAr «اسم المستودع بالعربية» / "Warehouse name (Arabic)"; nameEn «اسم المستودع بالإنجليزية» / "Warehouse name (English)"; branchFk «الفرع» / "Branch" (hidden while one active branch); isDefaultFl «المستودع الافتراضي» / "Default warehouse"; isActiveFl «نشط» / "Active". Entry — code (read-only, empty until first save); nameAr (mandatory); nameEn; branchFk (hidden while one active branch, default branch when empty); isDefaultFl and isActiveFl (read-only, changed only by their actions)
Composition       : container drawer · fields → none · branch selector → inline · submits: one (Save)
Permissions       : PERM_INV_WAREHOUSES_VIEW (Owner, Warehouse) · PERM_INV_WAREHOUSES_CREATE (Owner) · PERM_INV_WAREHOUSES_UPDATE (Owner — save, reactivate, set as default) · PERM_INV_WAREHOUSES_DELETE (Owner — deactivate)
Cross-module data : none
Navigation        : Inventory → Setup → Warehouses (from the inventory home); row → SCR-INV-003 seeded `warehousePk=<the row's warehousePk>`; in from SCR-INV-002 seeded `branchPk=<the branch>`
Actions           : Save (create / update) · Deactivate (confirm) · Reactivate · Set as default (confirm)
States            : empty · loading · error · offline (not required — generic network error)

## SCR-INV-002 — الفروع / Branches                 traces=REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-032,AC-INV-025,AC-INV-026,AC-INV-027,AC-INV-028,AC-INV-029,AC-INV-030,AC-INV-031,AC-INV-032,AC-INV-033,AC-INV-037
UI pattern        : Search + Entry — flat record (SCR-REQ-INV-002)
Sub-views         : Search · Entry under this ONE SCR
Fields shown      : Search filters — code «رمز الفرع» / "Branch code"; name «اسم الفرع» / "Branch name"; active state «الحالة» / "Active state". Result columns — code; nameAr «اسم الفرع بالعربية» / "Branch name (Arabic)"; nameEn «اسم الفرع بالإنجليزية» / "Branch name (English)"; isDefaultFl «الفرع الافتراضي» / "Default branch"; isActiveFl «نشط» / "Active". Entry — code (read-only); nameAr (mandatory); nameEn; isDefaultFl and isActiveFl (read-only)
Composition       : container drawer · fields → none · submits: one (Save)
Permissions       : PERM_INV_BRANCHES_VIEW (Owner, Warehouse) · PERM_INV_BRANCHES_CREATE (Owner) · PERM_INV_BRANCHES_UPDATE (Owner — save, reactivate, set as default) · PERM_INV_BRANCHES_DELETE (Owner — deactivate)
Cross-module data : none
Navigation        : Inventory → Setup → Branches (from the inventory home); row → SCR-INV-001 seeded `branchPk=<the row's branchPk>`
Actions           : Save (create / update) · Deactivate (confirm) · Reactivate · Set as default (confirm)
States            : empty · loading · error · offline (not required)

## SCR-INV-003 — أرصدة المخزون / Stock balances                 traces=REQ-INV-012,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-115,REQ-INV-121,REQ-INV-135,REQ-INV-137,AC-INV-015,AC-INV-040,AC-INV-043,AC-INV-044,AC-INV-045,AC-INV-046,AC-INV-047,AC-INV-129,AC-INV-135,AC-INV-151,AC-INV-153,UXD-INV-001,UXD-INV-002,UXD-INV-003
UI pattern        : Search + read-only result — inquiry list (SCR-REQ-INV-003)
Sub-views         : Search · availability summary under this ONE SCR
Fields shown      : Search filters — Variant «متغيّر الصنف» / "Variant" (code, name or barcode); Item «الصنف» / "Item"; warehouse «المستودع» / "Warehouse" (hidden while one); lot «التشغيلة» / "Lot" (lotExpiry on); non-zero only «غير الصفري فقط» / "Non-zero only". Result columns — Variant code and Variant name «متغيّر الصنف» / "Variant"; Item name «الصنف» / "Item"; warehouseFk «المستودع» / "Warehouse" (hidden while one); lotFk lot number «رقم التشغيلة» / "Lot number", expiry date «تاريخ انتهاء الصلاحية» / "Expiry date" and the expired mark «منتهية الصلاحية» / "Expired" (lotExpiry on); onHandQty «الكمية الفعلية» / "On-hand quantity"; reserved «محجوز» / "Reserved"; available «متاح» / "Available"; onHandMeasureQty «القياس الفعلي» / "On-hand measure" with its unit (dualQuantity on). Availability summary (when one Variant is filtered) — per warehouse and total: on-hand, reserved, available, measure. All read-only; no cost or value column (DEFAULT D-26)
Composition       : container page · availability per warehouse → inline · submits: none (read-only inquiry)
Permissions       : PERM_INV_STOCK_BALANCES_VIEW (Owner, Warehouse, Seller/Cashier) · CREATE / UPDATE / DELETE: not applicable
Cross-module data : Variant filter (pick by code, name or barcode) → UXD-INV-001 (CAT) · Item filter and Item name column → UXD-INV-002 (CAT) · measure unit label → UXD-INV-003 (CAT)
Navigation        : Inventory → Stock balances (from the inventory home); in from SCR-INV-001 with `warehousePk`, from SCR-INV-008 with `lotPk`; row → SCR-INV-004 seeded `variantPk` + `warehousePk` (+ `lotPk` when the row has a lot)
Actions           : none
States            : empty · loading · error · offline (not required)

## SCR-INV-004 — سجل حركات المخزون / Stock move history                 traces=REQ-INV-016,REQ-INV-055,REQ-INV-056,REQ-INV-075,REQ-INV-076,AC-INV-019,AC-INV-062,AC-INV-082,AC-INV-083,UXD-INV-004
UI pattern        : Search + read-only result — journal of moves with running balance (SCR-REQ-INV-004)
Sub-views         : Search · move detail (read-only) under this ONE SCR
Fields shown      : Search filters — Variant «متغيّر الصنف» / "Variant"; warehouse «المستودع» / "Warehouse" (hidden while one); lot «التشغيلة» / "Lot" (lotExpiry on); move type «نوع الحركة» / "Move type" (INV_MOVE_TYPE); document number «رقم المستند» / "Document number"; date from «من تاريخ» / "Date from" and date to «إلى تاريخ» / "Date to". Result columns — docDate «تاريخ الحركة» / "Move date"; moveTypeCode «نوع الحركة» / "Move type"; variantFk «متغيّر الصنف» / "Variant"; warehouseFk «المستودع» / "Warehouse" (hidden while one); lotFk «التشغيلة» / "Lot" (lotExpiry on); quantity «الكمية» / "Quantity"; measureQty «القياس» / "Measure" (dualQuantity on); balanceAfterQty «الرصيد بعد الحركة» / "Balance after move" (only while filtered to one Variant and one warehouse); sourceDocTypeCode «نوع المستند» / "Document type"; sourceDocNo «رقم المستند» / "Document number". Move detail adds offlineFl «بيع دون اتصال» / "Offline sale" and bundleVariantFk «الحزمة» / "Bundle". All read-only (REQ-INV-055)
Composition       : container page · move detail → none (read-only view, no save) · submits: none (read-only inquiry)
Permissions       : PERM_INV_STOCK_MOVES_VIEW (Owner, Warehouse) · CREATE / UPDATE / DELETE: not applicable
Cross-module data : Variant filter → UXD-INV-004 (CAT)
Navigation        : Inventory → Stock moves (from the inventory home); in from SCR-INV-003 with `variantPk` + `warehousePk` (+ `lotPk`), from SCR-INV-005 and SCR-INV-007 with `docNo`, from SCR-INV-012 with `variantPk`
Actions           : open move detail
States            : empty · loading · error · offline (not required)

## SCR-INV-005 — التحويلات بين المستودعات / Transfers                 traces=REQ-INV-011,REQ-INV-014,REQ-INV-062,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,AC-INV-014,AC-INV-017,AC-INV-085,AC-INV-086,AC-INV-087,AC-INV-088,AC-INV-089,AC-INV-090,AC-INV-091,AC-INV-092,AC-INV-093,AC-INV-094,AC-INV-095,UXD-INV-005
UI pattern        : Search + Entry — header + repeating lines (SCR-REQ-INV-005)
Sub-views         : Search · Entry (header + lines) under this ONE SCR
Fields shown      : Search filters — number «رقم التحويل» / "Transfer number"; date from / to; from warehouse «من المستودع» / "From warehouse"; to warehouse «إلى المستودع» / "To warehouse"; document status «حالة المستند» / "Document status". Result columns — docNo, docDate «تاريخ التحويل» / "Transfer date", fromWarehouseFk, toWarehouseFk, statusCode. Header — docNo (read-only), docDate (defaulted to today), statusCode (read-only), fromWarehouseFk (mandatory), toWarehouseFk (mandatory), noteText «ملاحظات» / "Notes" (Advanced), postedAt «تاريخ الترحيل» / "Posted at" (read-only). Lines (ENT-INV-007) — variantFk «متغيّر الصنف» / "Variant" (mandatory, by barcode or pick); quantity «الكمية» / "Quantity" (mandatory); lotFk «التشغيلة» / "Lot" (lotExpiry on, lot-tracked Items); measureQty «القياس» / "Measure" (dualQuantity on, dual-quantity Items); unitCost «تكلفة الوحدة» / "Unit cost" (read-only, filled at posting). Everything is read-only once POSTED or CANCELLED
Composition       : container page · lines → inline (pane beneath the header; a line is added or changed in a one-line drawer that returns it to the form) · Variant picker → inline (one field inside the line drawer) · submits: one (Save)
Permissions       : PERM_INV_TRANSFERS_VIEW · PERM_INV_TRANSFERS_CREATE · PERM_INV_TRANSFERS_UPDATE (save, post) · PERM_INV_TRANSFERS_DELETE (cancel) — Owner, Warehouse
Cross-module data : line Variant (barcode, pick, label, its Item's stock settings and measure unit) → UXD-INV-005 (CAT)
Navigation        : Inventory → Transfers (shown only with two or more active warehouses); routes search, `/new`, `/:id`; posted entry → SCR-INV-004 seeded `docNo=<the transfer's docNo>`
Actions           : Save · Post (DRAFT, saved) · Cancel (DRAFT, confirm)
States            : empty · loading · error · offline (not required)

## SCR-INV-006 — جرد المخزون / Stock counts                 traces=REQ-INV-013,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,REQ-INV-096,AC-INV-096,AC-INV-097,AC-INV-098,AC-INV-099,AC-INV-100,AC-INV-101,AC-INV-102,AC-INV-103,AC-INV-104,AC-INV-105,AC-INV-106,UXD-INV-006
UI pattern        : Search + Entry — header + repeating lines (scan list), mobile-first (SCR-REQ-INV-006)
Sub-views         : Search · Entry (header + scan list) under this ONE SCR
Fields shown      : Search filters — number «رقم الجرد» / "Count number"; date from / to; warehouse (hidden while one); document status. Result columns — docNo, docDate «تاريخ الجرد» / "Count date", warehouseFk (hidden while one), statusCode. Header — docNo (read-only), docDate, statusCode (read-only), warehouseFk «المستودع» / "Warehouse" (mandatory when starting; hidden and defaulted while one), noteText (Advanced, at start), adjustmentFk «التسوية الناتجة» / "Resulting adjustment" (read-only, after posting), postedAt (read-only). Scan bar — barcode «الباركود» / "Barcode" or Variant pick, countedQty «الكمية المعدودة» / "Counted quantity" (mandatory, default 1). Lines (ENT-INV-009) — variantFk «متغيّر الصنف» / "Variant"; lotFk «التشغيلة» / "Lot" (lotExpiry on); countedQty; countedMeasureQty «القياس المعدود» / "Counted measure" (dualQuantity on); systemQty «الكمية في النظام» / "System quantity" and differenceQty «الفرق» / "Difference" (only after posting — DEFAULT D-15)
Composition       : container page · lines → inline (stacked line cards beneath the scan bar; change or remove in a one-line drawer) · Variant picker → inline (fallback to the barcode field) · submits: one per action — each scan or line change commits one line (ADR-INV-034)
Permissions       : PERM_INV_COUNTS_VIEW · PERM_INV_COUNTS_CREATE (start) · PERM_INV_COUNTS_UPDATE (lines, post) · PERM_INV_COUNTS_DELETE (cancel) — Owner, Warehouse
Cross-module data : count line Variant (barcode resolution, pick by hand, label, its Item's stock settings and measure unit) → UXD-INV-006 (CAT)
Navigation        : Inventory → Stock counts; routes search, `/new`, `/:id`; posted entry → SCR-INV-007 entry `/:id` of the count's adjustmentFk
Actions           : Start count · Scan / add line · Change / remove line · Post (confirm) · Cancel (confirm)
Layout            : a single column that fits a 360 CSS-pixel viewport with no horizontal scrolling (REQ-INV-090); the barcode field keeps focus after each scan
States            : empty · loading · error · offline (not required)

## SCR-INV-007 — تسويات المخزون / Stock adjustments                 traces=REQ-INV-010,REQ-INV-013,REQ-INV-067,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-114,REQ-INV-123,AC-INV-013,AC-INV-073,AC-INV-107,AC-INV-108,AC-INV-109,AC-INV-110,AC-INV-111,AC-INV-112,AC-INV-113,AC-INV-114,AC-INV-115,AC-INV-116,AC-INV-128,AC-INV-137,AC-INV-150,UXD-INV-007
UI pattern        : Search + Entry — header + repeating lines with totals (SCR-REQ-INV-007)
Sub-views         : Search · Entry (header + lines) under this ONE SCR
Fields shown      : Search filters — number «رقم التسوية» / "Adjustment number"; date from / to; warehouse (hidden while one); reason «سبب التسوية» / "Adjustment reason" (INV_ADJUSTMENT_REASON); document status. Result columns — docNo, docDate «تاريخ التسوية» / "Adjustment date", warehouseFk (hidden while one), reasonCode, statusCode. Header — docNo (read-only), docDate, statusCode (read-only), reasonCode (mandatory), warehouseFk «المستودع» / "Warehouse" (hidden and defaulted while one), noteText (Advanced), sourceCountFk «الجرد المصدر» / "Source count" (read-only), postedAt (read-only). Lines (ENT-INV-011) — rowNo «رقم السطر» / "Row number" (read-only); variantFk «متغيّر الصنف» / "Variant" (mandatory); quantityChange «التغيّر في الكمية» / "Quantity change" (mandatory, + increase / − decrease); unitCost «تكلفة الوحدة» / "Unit cost" (increase lines, Advanced unless no average exists); lotNo «رقم التشغيلة» / "Lot number" and lotExpiryDate «تاريخ الصلاحية» / "Expiry date" (lotExpiry on); measureQtyChange «التغيّر في القياس» / "Measure change" (dualQuantity on); lineValue «قيمة السطر» / "Line value" (read-only, after posting) with the document total
Composition       : container page · lines → inline (pane beneath the header; a line is added or changed in a one-line drawer that returns it to the form) · Variant picker → inline (one field inside the line drawer) · submits: one (Save)
Permissions       : PERM_INV_ADJUSTMENTS_VIEW · PERM_INV_ADJUSTMENTS_CREATE · PERM_INV_ADJUSTMENTS_UPDATE (save, post) · PERM_INV_ADJUSTMENTS_DELETE (cancel) — Owner, Warehouse
Cross-module data : line Variant (barcode, pick, label, its Item's stock settings and measure unit) → UXD-INV-007 (CAT)
Navigation        : Inventory → Stock adjustments; routes search, `/new`, `/:id`; in from SCR-INV-006 (`/:id` of the resulting adjustment), from SCR-INV-008 (`/new` seeded `reason=EXPIRY_WRITE_OFF` + `lotPk` + `warehousePk`), from SCR-INV-010 (`/:id` of the posted OPENING_STOCK adjustment); posted entry → SCR-INV-004 seeded `docNo=<the adjustment's docNo>`
Actions           : Save · Post (DRAFT, saved) · Cancel (DRAFT, confirm)
States            : empty · loading · error · offline (not required)

## SCR-INV-008 — التشغيلات وقرب انتهاء الصلاحية / Lots and near expiry                 traces=REQ-INV-108,REQ-INV-109,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-134,AC-INV-121,AC-INV-122,AC-INV-129,AC-INV-130,AC-INV-131,AC-INV-150,UXD-INV-008
UI pattern        : Search + Entry — list with a near-expiry view (SCR-REQ-INV-008)
Sub-views         : Search (all lots | near-expiry only) · Entry (expiry correction) under this ONE SCR
Fields shown      : Search filters — Variant «متغيّر الصنف» / "Variant"; lot number «رقم التشغيلة» / "Lot number"; warehouse (hidden while one); expiry date from / to «تاريخ الصلاحية من / إلى» / "Expiry from / to"; near-expiry only «قريبة الانتهاء فقط» / "Near expiry only". Result columns — variantFk «متغيّر الصنف» / "Variant"; code «رقم التشغيلة» / "Lot number"; warehouse (hidden while one); on-hand «الكمية الفعلية» / "On-hand quantity"; expiryDate «تاريخ انتهاء الصلاحية» / "Expiry date"; days left «الأيام المتبقية» / "Days left"; expired mark «منتهية الصلاحية» / "Expired". Entry — code and variantFk (read-only); expiryDate (mandatory, Owner); nameAr «وصف التشغيلة بالعربية» / "Lot description (Arabic)" and nameEn «وصف التشغيلة بالإنجليزية» / "Lot description (English)" (Advanced); firstReceivedAt «تاريخ أول استلام» / "First received" and nearExpiryNotifiedAt «تاريخ تنبيه قرب الانتهاء» / "Near-expiry alert sent" (read-only)
Composition       : container drawer · fields → none · submits: one (Save expiry)
Permissions       : PERM_INV_LOTS_VIEW (Owner, Warehouse) · PERM_INV_LOTS_UPDATE (Owner) · CREATE / DELETE: not applicable
Cross-module data : Variant filter and Variant label of each row → UXD-INV-008 (CAT)
Navigation        : Inventory → Lots (shown only with lotExpiry on); in from the near-expiry notification seeded `nearExpiryOnly=true`; expired row with on-hand → SCR-INV-007 `/new` seeded `reason=EXPIRY_WRITE_OFF` + `lotPk` + `warehousePk`; row → SCR-INV-003 seeded `lotPk` (+ `warehousePk`)
Actions           : Save expiry · Write off (navigation only)
States            : empty · loading · error · offline (not required)

## SCR-INV-009 — إعدادات مخزون الصنف / Item stock settings                 traces=REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-134,REQ-INV-135,AC-INV-138,AC-INV-139,AC-INV-140,AC-INV-141,AC-INV-142,AC-INV-143,AC-INV-144,AC-INV-145,UXD-INV-009,UXD-INV-010
UI pattern        : Search + Entry — flat record (SCR-REQ-INV-009)
Sub-views         : Search · Entry under this ONE SCR
Fields shown      : Search filters — Item «الصنف» / "Item" (code or name); tracked by lot «يُتتبَّع بالتشغيلة» / "Tracked by lot" (lotExpiry on); dual quantity «كمية مزدوجة» / "Dual quantity" (dualQuantity on). Result columns — Item's code «رمز الصنف» / "Item code"; Item's name «اسم الصنف» / "Item name"; lotTrackedFl (lotExpiry on); dualQuantityFl and measureUomCode «وحدة القياس الثانية» / "Measure unit" (dualQuantity on); valueAr «ملخص التتبع بالعربية» / "Tracking summary (Arabic)" and valueEn «ملخص التتبع بالإنجليزية» / "Tracking summary (English)" (read-only). Entry — itemFk «الصنف» / "Item" (mandatory, pick when new, read-only when editing); key «مفتاح الإعداد» / "Setting key" (read-only); lotTrackedFl (lotExpiry on); dualQuantityFl and measureUomCode (dualQuantity on; measure unit mandatory when dual)
Composition       : container drawer · fields → none · Item picker → inline (one field) · submits: one (Save)
Permissions       : PERM_INV_ITEM_STOCK_SETTINGS_VIEW (Owner, Warehouse) · PERM_INV_ITEM_STOCK_SETTINGS_CREATE (Owner) · PERM_INV_ITEM_STOCK_SETTINGS_UPDATE (Owner) · DELETE: not applicable
Cross-module data : Item pick → UXD-INV-009 (CAT) · measure unit options and label → UXD-INV-010 (CAT)
Navigation        : Inventory → Setup → Item stock settings (shown only with lotExpiry or dualQuantity on); no outgoing jump
Actions           : Save
States            : empty · loading · error · offline (not required)

## SCR-INV-010 — المخزون الافتتاحي / Opening stock                 traces=REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,AC-INV-076,AC-INV-077,AC-INV-078,AC-INV-079,AC-INV-080,AC-INV-081
UI pattern        : Entry + bulk load — header + repeating lines with totals (SCR-REQ-INV-010)
Sub-views         : Entry · bulk load (sheet download and read) under this ONE SCR
Fields shown      : Header — warehouseFk «المستودع» / "Warehouse" (hidden and defaulted while one). Lines (ENT-INV-011) — rowNo «رقم السطر» / "Row number" (read-only); Variant code or barcode «رمز المتغيّر أو الباركود» / "Variant code or barcode" (mandatory); quantityChange «الكمية» / "Quantity" (mandatory, > 0); unitCost «تكلفة الوحدة» / "Unit cost" (mandatory); lotNo «رقم التشغيلة» / "Lot number" and lotExpiryDate «تاريخ الصلاحية» / "Expiry date" (lotExpiry on); measureQtyChange «القياس» / "Measure" (dualQuantity on); total quantity and line count (read-only). Row failures list — rowNo and message
Composition       : container page · lines → inline (pane beneath the header; a line is added or changed in a one-line drawer that returns it to the form; a loaded sheet fills the pane) · submits: one (Post)
Permissions       : PERM_INV_OPENING_STOCK_VIEW (Owner — screen, sheet download) · PERM_INV_OPENING_STOCK_CREATE (Owner — post) · UPDATE / DELETE: not applicable
Cross-module data : none (the Variant code or barcode is sent as entered and resolved by INV — ADR-INV-035)
Navigation        : Inventory → Setup → Opening stock (from the setup wizard step 5 and the inventory home); after posting → SCR-INV-007 `/:id` of the posted OPENING_STOCK adjustment
Actions           : Download sheet · Load sheet · Post
States            : empty (no line yet: download / load / add line offered) · loading · error · offline (not required)

## SCR-INV-011 — إعدادات المخزون / Inventory settings                 traces=REQ-INV-065,REQ-INV-066,REQ-INV-118,REQ-INV-119,AC-INV-071,AC-INV-072,AC-INV-132,AC-INV-133
UI pattern        : Entry — flat record (SCR-REQ-INV-011)
Sub-views         : Entry under this ONE SCR
Fields shown      : «السماح بالرصيد السالب» / "Allow negative stock" (flag, default on); «أفق قرب انتهاء الصلاحية (أيام)» / "Near-expiry horizon (days)" (whole number 1–365, default 30; shown only with lotExpiry on)
Composition       : container page · fields → none · submits: one (Save)
Permissions       : PERM_INV_SETTINGS_VIEW (Owner) · PERM_INV_SETTINGS_UPDATE (Owner) · CREATE / DELETE: not applicable
Cross-module data : none
Navigation        : Inventory → Setup → Inventory settings (from the inventory home); no outgoing jump
Actions           : Save
States            : empty (not applicable — defaults always shown) · loading · error · offline (not required)

## SCR-INV-012 — تقييم المخزون / Stock valuation                 traces=REQ-INV-059,REQ-INV-061,REQ-INV-063,AC-INV-065,AC-INV-067,AC-INV-069,UXD-INV-011
UI pattern        : Search + read-only result — inquiry list with a total value (SCR-REQ-INV-012)
Sub-views         : Search · Variant detail (read-only) under this ONE SCR
Fields shown      : Search filters — Variant «متغيّر الصنف» / "Variant" (code, name or barcode); Item «الصنف» / "Item"; non-zero only «غير الصفري فقط» / "Non-zero only". Result columns — Variant code and name; totalOnHandQty «إجمالي الكمية» / "Total quantity"; averageUnitCost «متوسط تكلفة الوحدة» / "Average unit cost"; stockValue «قيمة المخزون» / "Stock value"; grand total of stock value «إجمالي قيمة المخزون» / "Total stock value". Variant detail adds lastMoveAt «آخر حركة» / "Last move". All read-only
Composition       : container page · Variant detail → none (read-only view, no save) · submits: none (read-only inquiry)
Permissions       : PERM_INV_STOCK_VALUATION_VIEW (Owner) · CREATE / UPDATE / DELETE: not applicable
Cross-module data : Variant filter and Item filter → UXD-INV-011 (CAT)
Navigation        : Inventory → Stock valuation (from the inventory home); row → Variant detail; detail → SCR-INV-004 seeded `variantPk`
Actions           : open Variant detail
States            : empty · loading · error · offline (not required)

## Cross-module display dependencies (UXD)

Each UXD names a need of an INV screen: the screen shows data whose source of truth is another module's real
API. All are CAT reads promised by contract-cat. The UXD cites the CAT contract only. CAT's own artifacts give
the CAT operation id and path (ADR-INV-033).

### UXD-INV-001 — Stock balances: Variant filter                 traces=REQ-INV-039,AC-INV-044
Screen SCR-INV-003 · field: Variant filter (pick by code, name or barcode) · owner CAT (ENT-CAT-002 Variant, ENT-CAT-009 Barcode) · contracts CON-CAT-016 (search), CON-CAT-010 (barcode lookup) · yields `variantPk` for the INV search

### UXD-INV-002 — Stock balances: Item filter and Item name column                 traces=REQ-INV-039,AC-INV-044
Screen SCR-INV-003 · field: Item filter and Item name column · owner CAT (ENT-CAT-001 Item) · contracts CON-CAT-016 (search), CON-CAT-008 (Variant → its Item), CON-CAT-007 (read Item) · yields `itemPk` and the Item name

### UXD-INV-003 — Stock balances: measure unit label                 traces=REQ-INV-121,AC-INV-135
Screen SCR-INV-003 · field: unit beside onHandMeasureQty (dualQuantity on) · owner CAT (unit-of-measure lookup, SRS A6 lookups consumed) · labels `measureUomCode` in ar / en

### UXD-INV-004 — Stock move history: Variant filter                 traces=REQ-INV-075,AC-INV-082
Screen SCR-INV-004 · field: Variant filter · owner CAT (ENT-CAT-002, ENT-CAT-009) · contracts CON-CAT-016, CON-CAT-010 · yields `variantPk`

### UXD-INV-005 — Transfers: line Variant                 traces=REQ-INV-078,AC-INV-085,REQ-INV-084,AC-INV-093
Screen SCR-INV-005 · field: line Variant (barcode entry, pick, label of `variantPk`, its Item for the Item stock settings read, measure unit label) · owner CAT (ENT-CAT-002, ENT-CAT-009, ENT-CAT-001) · contracts CON-CAT-010, CON-CAT-016, CON-CAT-008, CON-CAT-007

### UXD-INV-006 — Stock counts: count line Variant                 traces=REQ-INV-088,AC-INV-097,REQ-INV-091,AC-INV-101
Screen SCR-INV-006 · field: count line Variant (barcode resolution before a lot or measure is asked, pick by hand after an unknown barcode, label of `variantPk`, its Item for the Item stock settings read, measure unit label) · owner CAT · contracts CON-CAT-010, CON-CAT-016, CON-CAT-008, CON-CAT-007

### UXD-INV-007 — Stock adjustments: line Variant                 traces=REQ-INV-097,AC-INV-107,REQ-INV-099,AC-INV-110
Screen SCR-INV-007 · field: line Variant (barcode entry, pick, label of `variantPk`, its Item for the Item stock settings read, measure unit label) · owner CAT · contracts CON-CAT-010, CON-CAT-016, CON-CAT-008, CON-CAT-007

### UXD-INV-008 — Lots: Variant filter and Variant label                 traces=REQ-INV-109,AC-INV-122
Screen SCR-INV-008 · field: Variant filter and Variant column of each lot row (`LotRow.variantPk`) · owner CAT · contracts CON-CAT-016, CON-CAT-010, CON-CAT-008

### UXD-INV-009 — Item stock settings: Item pick                 traces=REQ-INV-124,AC-INV-138,REQ-INV-125,AC-INV-140
Screen SCR-INV-009 · field: Item pick on a new record (STANDARD Items; a BUNDLE Item is refused by RULE-INV-045) · owner CAT (ENT-CAT-001) · contracts CON-CAT-016, CON-CAT-007 · yields `itemPk`

### UXD-INV-010 — Item stock settings: measure unit options                 traces=REQ-INV-125,AC-INV-140,AC-INV-141
Screen SCR-INV-009 · field: measureUomCode options and label · owner CAT (unit-of-measure lookup, SRS A6 lookups consumed)

### UXD-INV-011 — Stock valuation: Variant and Item filters                 traces=REQ-INV-063,AC-INV-069
Screen SCR-INV-012 · field: Variant filter and Item filter · owner CAT (ENT-CAT-002, ENT-CAT-009, ENT-CAT-001) · contracts CON-CAT-016, CON-CAT-010 · yields `variantPk` / `itemPk`

## Reconciliation self-check (SRS ↔ draft)

```
RECONCILIATION — INV v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)   → 23 stories in flows, each with REQ + SCR-REQ; 5 stories (US-INV-004, 009, 018, 019, 022) have no SRS screen and no flow
B2 no RULE-* contradicts a flow/spec outcome                           → none found; the count's per-line commit follows REQ-INV-088 / B5 (ADR-INV-034)
B3 every field/permission on a screen exists in the SRS               → extra: none (status and audit-free read-only fields are SRS fields); missing: none
B4 every screen entry of the SRS has exactly one SCR-* block          → SCR-REQ-INV-001 … 012 → SCR-INV-001 … 012, one each
RESULT  reconciled 12 · reworked 0 · ADRs ADR-INV-033, ADR-INV-034, ADR-INV-035, ADR-INV-036
```

| SCR | SCR-REQ | Page code | Owning ENT |
|---|---|---|---|
| SCR-INV-001 | SCR-REQ-INV-001 | INV_WAREHOUSES | ENT-INV-001 |
| SCR-INV-002 | SCR-REQ-INV-002 | INV_BRANCHES | ENT-INV-002 |
| SCR-INV-003 | SCR-REQ-INV-003 | INV_STOCK_BALANCES | ENT-INV-003 |
| SCR-INV-004 | SCR-REQ-INV-004 | INV_STOCK_MOVES | ENT-INV-004 |
| SCR-INV-005 | SCR-REQ-INV-005 | INV_TRANSFERS | ENT-INV-006 (+ ENT-INV-007) |
| SCR-INV-006 | SCR-REQ-INV-006 | INV_COUNTS | ENT-INV-008 (+ ENT-INV-009) |
| SCR-INV-007 | SCR-REQ-INV-007 | INV_ADJUSTMENTS | ENT-INV-010 (+ ENT-INV-011) |
| SCR-INV-008 | SCR-REQ-INV-008 | INV_LOTS | ENT-INV-012 |
| SCR-INV-009 | SCR-REQ-INV-009 | INV_ITEM_STOCK_SETTINGS | ENT-INV-014 |
| SCR-INV-010 | SCR-REQ-INV-010 | INV_OPENING_STOCK | ENT-INV-010 (+ ENT-INV-011) |
| SCR-INV-011 | SCR-REQ-INV-011 | INV_SETTINGS | — (given `cu` settings) |
| SCR-INV-012 | SCR-REQ-INV-012 | INV_STOCK_VALUATION | ENT-INV-013 |

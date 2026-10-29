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

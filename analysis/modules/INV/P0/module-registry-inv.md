## MODULE REGISTRY — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module Code    : INV   (profile.vocabulary.module_prefixes)
Bounded context: inventory — where stock is and how much: stock, moves, reservations, transfers, counts, lots, dual quantity
Layer / Type   : L2 / engine     Execution tier : 2.1
Source         : NEW
Knowledge      : KB:raw-idea.md §1, §4.1, §4.2, §5 #3, §6.2, §6.7, §6.8; DP §3 #3, §5 G1 G3 G8, §7.1; erp-core 1.6.0 manifest (§1.5)
Readiness      : READY
══════════════════════════════════════════════════════════════════

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity (ar/en) | Kind | PRIVATE / SHARED | Source |
|---|---|---|---|
| Warehouse / المستودع | master | SHARED (PUR, SLS, SFR, INS) | KB §6.8; DP §7.1, §5 G8; registry §4 |
| Branch / الفرع | master | SHARED (PUR, SLS, STL, TAX, INS) | KB §6.8 ("one warehouse/branch … to start"); DP §5 G8; DECISION INV-P07 |
| Stock balance / رصيد المخزون | transactional | SHARED (SLS, SFR, INS — read only) | KB §5 #3; DP §6 |
| Stock move / حركة المخزون | transactional | PRIVATE (published as domain events to ACC, SWR, INS) | KB §6.8; DP §4 #3 |
| Reservation / حجز المخزون | transactional | PRIVATE (created by SLS and SFR through an INV crossmodule API) | KB §5 #3 |
| Transfer / تحويل مخزني | transactional | PRIVATE | KB §5 #3, §6.8 |
| Stock count / جرد المخزون | transactional | PRIVATE | KB §5 #3, §6.7 |
| Stock adjustment / تسوية المخزون | transactional | PRIVATE | AUTO (count differences and write-offs need a stock document); KB §5 #3; DECISION INV-P10 |
| Lot / التشغيلة | master | SHARED (PUR receipt, SLS FEFO issue) | KB §4.1 (`lotExpiry`); DP §7.1; DECISION INV-P02 |
| Stock valuation (average cost) / تقييم المخزون (متوسط التكلفة) | transactional | SHARED (SLS margin, ACC posting, INS) | DECISION INV-P09 |

Not registered here:
- Dual quantity / الكمية المزدوجة is an attribute of stock balances, moves and lots, not an entity (registry §4).
- Stock held as a pre-built bundle is not registered (DECISION INV-P06).
- Serial number / الرقم التسلسلي belongs to SWR.
- Inventory settings are given `cu` settings.

LOOKUPS OWNED    (value lists this module masters)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| inv.adjustment-reason | Stock adjustment reason / سبب تسوية المخزون (count difference, damage, expiry write-off and similar) | — (none named by the user) | AUTO; KB §5 #3 |

Rule (profile): all LOV values are loaded at runtime from the core lookup module (mdl). There are no hardcoded enums in APIs or field specs. Activity presets and capability flags are core settings, read through FeatureFlags.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| Unit of measure / وحدة القياس | CAT (owner is CAT whether it is a CAT-held list or a given `mdl` lookup type; settled in the CAT run, DP §8 P-05) |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| Item / صنف | CAT | HARD-FK | Stock belongs to an item of the catalogue |
| Variant / متغيّر الصنف | CAT | HARD-FK | Stock, moves, reservations and lots key on the Variant; the default Variant applies when `variants` is off (DECISION INV-P05) |
| Bundle / حزمة (composition) | CAT | SOFT-READ | Components are exploded into stock moves when a bundle is issued (DECISION INV-P06) |
| Unit of measure conversions | CAT | SOFT-READ | Stock is held in the Variant's base unit of measure |
| Tenant / المستأجر | given `erp-core:modules.tenant` | (given) | Default warehouse and default branch seeded at provisioning |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| CAT | HARD-FK | Item, Variant, Bundle composition, unit-of-measure conversions |
| `erp-core:modules.cu` | (given — no XM edge; the SRS declares the use in its `given-usage` block) | `SettingsApi` through `FeatureFlags`: `lotExpiry`, `dualQuantity`, `bundles`; inventory settings (negative-stock setting, near-expiry horizon, the consumed unit-of-measure key INV_MEASURE_UOM_KEY — ADR-INV-043) |
| `erp-core:modules.sequence` | (given — no XM edge) | `NumberSeriesApi`: transfer, count and adjustment numbers |
| `erp-core:modules.events` | (given — no XM edge) | `OutboxPublisher` only: the events `inv.StockMoveRecorded` (for ACC, SWR, INS) and `inv.InventoryMasterChanged` (for PUR, SLS, SFR, STL, TAX, INS). INV subscribes to no event: offline POS sales and the documents of SLS, PUR and SFR reach INV by calling its in-process operations (SRS D-23, ADR-INV-018) |
| `erp-core:modules.audit` | (given — no XM edge) | `AuditApi`: change history of stock documents |
| `erp-core:modules.mdl` | (given — no XM edge) | `MdlLookupApi`: adjustment reason, unit of measure |
| `erp-core:modules.tenant` | (given — no XM edge) | `TenantProvisioningContributor`: default warehouse and default branch at tenant creation |
| `erp-core:modules.sec` | (given — no XM edge) | `PermissionContributor`: inventory permissions for the Warehouse role preset (D8) |
| `erp-core:modules.notif` | (given — no XM edge) | `NotificationDispatchApi`: near-expiry and negative-stock alerts to the Warehouse role |
| `erp-core:modules.report` | (given — no XM edge) | `ReportProvider`, `PrintApi`: count sheets, transfer prints, near-expiry and stock reports |
| `erp-core:modules.module` | (given — no XM edge) | `ModuleCatalogApi`, `ModuleEntitlementApi`: registration and entitlement of the Inventory business module |
| `erp-core:modules.common` | (given — no XM edge) | base entity, audit columns, optimistic locking, paging, search, idempotency |
ROOT: NO
Consumed by: PUR, SLS, STL, TAX, SWR, ACC, APR, SFR, INS (platform-dependencies).

POLICIES REGISTERED   (POL ids defined in `business-policies-inv.md`; the statements live there only — this index keeps the registry and the policies in agreement)
| Policy | Name (en / ar) | Pattern | Status |
|---|---|---|---|
| POL-INV-001 | One stock across channels / مخزون واحد لجميع القنوات | ubiquitous | CONFIRMED |
| POL-INV-002 | Warehouse on every stock move / المستودع في كل حركة مخزون | ubiquitous | CONFIRMED |
| POL-INV-003 | Default warehouse at start / مستودع افتراضي عند البدء | event | CONFIRMED |
| POL-INV-004 | Hidden warehouse selector while one warehouse / إخفاء محدد المستودع عند وجود مستودع واحد | state | CONFIRMED |
| POL-INV-005 | Reveal on second warehouse / الإظهار عند إضافة مستودع ثانٍ | event | CONFIRMED |
| POL-INV-006 | Lot and expiry on receipt / التشغيلة والصلاحية عند الاستلام | optional | CONFIRMED |
| POL-INV-007 | FEFO issue / الصرف بالأقرب انتهاءً | optional | CONFIRMED |
| POL-INV-008 | Near-expiry reporting / تقرير قرب انتهاء الصلاحية | optional | CONFIRMED |
| POL-INV-009 | Dual quantity stock / مخزون بالكمية المزدوجة | optional | CONFIRMED |
| POL-INV-010 | Flag turn-off blocked when data depends on it / منع إيقاف المفتاح عند وجود بيانات تعتمد عليه | unwanted | CONFIRMED |
| POL-INV-011 | Flags only, never the activity / المفاتيح فقط وليس النشاط | ubiquitous | CONFIRMED |
| POL-INV-012 | Mobile stock counting / الجرد عبر الهاتف المحمول | ubiquitous | CONFIRMED |
| POL-INV-013 | Warehouse role preset / الدور المسبق «المستودع» | ubiquitous | CONFIRMED |
| POL-INV-014 | Bundle issue moves component stock / صرف الحزمة يحرّك مخزون مكوّناتها | optional | CONFIRMED |
| POL-INV-015 | Default branch at start / فرع افتراضي عند البدء | event | CONFIRMED |
| POL-INV-016 | Hidden branch selector while one branch / إخفاء محدد الفرع عند وجود فرع واحد | state | CONFIRMED |
| POL-INV-017 | Reveal on second branch / الإظهار عند إضافة فرع ثانٍ | event | CONFIRMED |
| POL-INV-018 | Optional block on negative stock / منع الرصيد السالب اختياريًا | optional | CONFIRMED |
| POL-INV-019 | Alert on negative stock / تنبيه عند الرصيد السالب | unwanted | CONFIRMED |
| POL-INV-020 | Offline POS sales always accepted / قبول مبيعات نقطة البيع دون اتصال دائمًا | unwanted | CONFIRMED |
| POL-INV-021 | Weighted-average stock valuation / تقييم المخزون بالتكلفة المتوسطة المرجّحة | ubiquitous | CONFIRMED |
| POL-INV-022 | Expired lots excluded from sale / استبعاد التشغيلات المنتهية من البيع | unwanted | CONFIRMED |

AUTO-DECISIONS
AUTO: Serial identity is owned by SWR. INV holds only the quantities of serial-tracked Variants.  FROM: KB §4.1 (`serialTracking` → new module SerialWarranty), §5 (SWR depends on INV)  IF WRONG: move the serial ledger into INV and make SWR consume it.
AUTO: Stock is held in the Variant's base unit of measure. Purchase and sale units are converted through CAT-owned conversions.  FROM: domain best practice; DP §8 P-05  IF WRONG: hold stock per unit of measure with INV-owned conversions.
AUTO: One default warehouse and one default branch are seeded at tenant provisioning through `TenantProvisioningContributor`.  FROM: KB §6.8; manifest `erp-core:modules.tenant`  IF WRONG: create the first warehouse and branch in the setup wizard.
AUTO: Inventory settings (negative-stock setting, near-expiry horizon) are given `cu` settings, not an INV entity.  FROM: profile rule (core settings read through FeatureFlags); DP §5 G6  IF WRONG: register an INV config entity.
AUTO: SLS (orders) and SFR (checkout) create reservations through an INV crossmodule API. A POS sale issues stock directly, without a reservation.  FROM: KB §1 (one stock), §5 (SLS, SFR depend on INV); DP §5 G5  IF WRONG: reserve on POS cart lines as well.
AUTO: Stock moves are published as domain events through the given outbox (`OutboxPublisher`), each carrying its unit cost. ACC, SWR and INS consume them, and INV holds no reverse edge. INV subscribes to no event (SRS D-23, ADR-INV-018).  FROM: KB §5 #3 (Inventory uses events); DP §5 G5  IF WRONG: expose a crossmodule query API in their place.
AUTO: INV never calls the given `gl`. Valuation and adjustment postings reach the ledger through ACC, which owns the posting rules and calls `GlPostingApi`.  FROM: KB §5 #9; DP §5 G5; DECISION INV-P02  IF WRONG: let INV post its own journal entries.
AUTO: Receipt cost is supplied by PUR on the receipt call into INV, so INV holds no edge to PUR.  FROM: KB §5 (PUR depends on INV); DP §5 G5  IF WRONG: INV reads the receipt cost from PUR through a crossmodule API and the edge direction is revisited.
AUTO: Dual quantity is an attribute of stock balances, moves and lots, not a separate entity.  FROM: registry §4; KB §4.1  IF WRONG: register a dual-quantity entity.
AUTO: Stock adjustment is a transactional candidate of INV.  FROM: KB §5 #3 (counts); domain best practice (a count difference posts through an adjustment document)  IF WRONG: post count differences directly from the stock count.
AUTO: Branch is a new term, introduced from the G8 dimension list. It is not yet in the steering vocabulary.  FROM: DP §5 G8; KB §6.8  IF WRONG: rename in the vocabulary at the next domain-profile revision.
AUTO: The module registry indexes every POL-INV id (POLICIES REGISTERED); the policy text stays in business-policies-inv only.  FROM: engine §4 (the module registry IS this stage's registry output); contract C3.7 registry-agree (business-policies ↔ module-registry, kind POL); precedent `module-registry-cat.md` (CAT v2 P0)  IF WRONG: none (required by C3.7)

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| INV-P05 | Stock key (registry OI-07, INV side) | Stock, moves, reservations and lots key on the Variant; the default Variant applies when `variants` is off. A conflicting CAT registry decision wins at the P0.5 consistency check (engine STEP C) | Converged in dialogue (round 2); user confirms at the gate | DP §8 P-04; KB §4.1; DP §5 G2 |
| INV-P06 | Bundle stock (registry OI-09) | CAT owns the composition. INV explodes a bundle into component stock moves at issue (POL-INV-014). A bundle holds no stock of its own, and bundle assembly is excluded | Converged in dialogue (round 2); user confirms at the gate | DP §8 P-06; KB §3 D1, §9 |
| INV-P07 | Branch ownership | INV owns Branch next to Warehouse, because PTY and CAT are past or at their gate. One default branch is seeded and its selector stays hidden while its count is 1 (POL-INV-015, POL-INV-016, POL-INV-017). STL and TAX read it, so each has an edge to INV | Converged in dialogue (round 2); user confirms at the gate | KB §6.8; DP §5 G8 |
| INV-P08 | Negative stock | Tenant setting, default allowed with an alert to the Warehouse role. When the tenant disallows it, online issues below zero are blocked (POL-INV-018, POL-INV-019). Synced offline POS sales are always accepted (POL-INV-020) | Converged in dialogue (round 2); user confirms at the gate | KB §5 #6, §6.5, §6.10; DP §5 G7; domain best practice |
| INV-P09 | Costing method | Perpetual weighted-average cost per Variant across the tenant; transfers carry cost unchanged; FIFO not offered in v1 (POL-INV-021) | Converged in dialogue (round 2); user confirms at the gate | IAS 2 ¶23, ¶25; IFRS for SMEs Section 13 (not live-verified); KB §3 D7 |
| INV-P10 | Expired lots | An expired lot is excluded from issue for sale and from FEFO selection (POL-INV-022). It stays in stock, visible in near-expiry reporting, and leaves stock only through a stock adjustment | Converged in dialogue (round 2); user confirms at the gate | KB §4.1, §4.2; consumer-protection practice (not live-verified) |
══════════════════════════════════════════════════════════════════

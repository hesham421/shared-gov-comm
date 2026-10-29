## MODULE REGISTRY — Party / الأطراف (PTY)
══════════════════════════════════════════════════════════════════
Module Code    : PTY   (profile.vocabulary.module_prefixes)
Bounded context: party — "Who: customers, suppliers, addresses, credit. Credentials belong to given sec" (DP §7.2)
Layer / Type   : L1 / master data     Execution tier : 1.2
Source         : NEW
Knowledge      : KB:raw-idea.md §1, §3 D5, §4.1, §5 #4, §6.1–§6.6; DP §3 #4, §6, §7.1, §7.2; registry §4–§5 (CAND-PTY-001…004, CAND-STL-003), OI-13, OI-16
Readiness      : READY
══════════════════════════════════════════════════════════════════

ENTITIES OWNED   (names only; entity IDs are assigned by P1)
| Entity (ar/en) | Kind | PRIVATE / SHARED | Source |
|---|---|---|---|
| الطرف / Party | master | PRIVATE (the identity record; other modules reference it through its Customer or Supplier role) | decision 4: one identity record per counterparty |
| العميل / Customer | master | SHARED (SLS, STL, SFR, INS) | CAND-PTY-001; DP §7.1; KB §5 #4 |
| المورّد / Supplier | master | SHARED (PUR, STL, INS) | CAND-PTY-002; DP §7.1; KB §5 #4 |
| العنوان / Address | master | SHARED (SLS, PUR, SFR) | CAND-PTY-003; AUTO (PRIVATE → SHARED) |
| شرط الدفع / Payment term | config | SHARED (SLS, PUR, STL) | CAND-PTY-004 (terms part); KB §4.1 "terms"; decision 6 |

Not owned here:
- The CUSTOMER-realm account (credentials, login, invitations) is given `erp-core:modules.sec`, linked by SOFT-READ (decision 5).
- The credit limit is an attribute of Customer, not an entity (decision 6). CAND-PTY-004 resolves to Payment term.
- The customer's open balance and ageing belong to STL. CAND-STL-003 is withdrawn: STL owns balances, not limits (decision 6).
- The tax identity of a party is an attribute of Party, not an entity of TAX.

LOOKUPS OWNED    (value lists this module masters, held in given `mdl`)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| customer-group | Customer group / مجموعة العملاء: classifies customers (for example retail / wholesale) so that price lists and reports can follow it | none named | DP §7.1 (Price list "for a channel or customer group"); decision 8 |
| address-type | Address type / نوع العنوان: the purpose of an address (for example billing / delivery) | none named | AUTO (domain best practice) |
Rule (profile): all LOV values runtime-loaded from the core lookup module (mdl); no hardcoded enums in APIs or field specs; activity presets and capability flags are core settings, read through FeatureFlags

LOOKUPS CONSUMED (from other modules; each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| country / region | given `erp-core:modules.mdl` (AUTO) |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| User (CUSTOMER realm) | given `erp-core:modules.sec` | SOFT-READ | A Customer links to 0..1 CUSTOMER-realm account (decision 5) |
| AppConfiguration (capability flag `b2bCredit`, credit-overrun setting) | given `erp-core:modules.cu` | SOFT-READ (through FeatureFlags / SettingsApi) | Credit features show only while `b2bCredit` is on; the overrun behaviour is a tenant setting (decisions 3, 7) |
| LookupType / LookupValue | given `erp-core:modules.mdl` | SOFT-READ | customer-group, address-type, country / region |
| NumberSeries | given `erp-core:modules.sequence` | SOFT-READ | Customer and Supplier numbers (AUTO) |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| `erp-core:modules.sec` | (given — no XM edge; the SRS declares the use in its `given-usage` block) | SecUserDirectoryApi, SecCustomerInvitationApi, SecCustomerDefaultRoleApi; PermissionContributor (party, credit and credit-override permissions) |
| `erp-core:modules.cu` | (given — no XM edge) | SettingsApi through the FeatureFlags read facade (`b2bCredit`; credit-overrun setting) |
| `erp-core:modules.mdl` | (given — no XM edge) | MdlLookupApi (customer-group, address-type, country / region) |
| `erp-core:modules.sequence` | (given — no XM edge) | NumberSeriesApi (party numbers) |
| `erp-core:modules.events` | (given — no XM edge) | DomainEventPublisher / OutboxPublisher (party created / changed events for SFR and INS) |
| `erp-core:modules.audit` | (given — no XM edge) | AuditApi (credit-limit and payment-term change history) |
| `erp-core:modules.tenant` | (given — no XM edge) | TenantProvisioningContributor (walk-in customer seed, decision 9); TenantExportContributor (party data in tenant export) |
| `erp-core:modules.common` | (given — no XM edge) | base entity, audit columns, optimistic locking, validation, search (global search, POL-PTY-007), paging, owned lookups |
ROOT: YES (no business-module dependency; DAG #4 [KB:raw-idea.md §5])

Consumers (reverse edges, from the platform-dependencies block): PRC (SOFT-READ of the customer-group lookup only, decision 8), PUR (HARD-FK Supplier), SLS (HARD-FK Customer), STL (HARD-FK Customer / Supplier; it fills the credit-exposure extension point, decision 6), SFR (SOFT-READ Customer, Address), INS (SOFT-READ).

EXTENSION POINT DECLARED
| Point | Declared by | Filled by | What it carries |
|---|---|---|---|
| Customer credit exposure / تعرّض العميل الائتماني | PTY | STL (open receivables); other modules may contribute | The customer's open exposure only. PTY compares exposure plus the new amount with the credit limit and returns a verdict; SLS and SFR enforce it (decision 6) |

POLICIES SERVED   (index only; the statements live in business-policies-pty.md)
| Policy | Serves |
|---|---|
| POL-PTY-001 | Party, Customer — one customer base across back-office, POS and storefront |
| POL-PTY-002 | Customer, sec link — guest becomes a customer (decision 5) |
| POL-PTY-003 | Customer credit limit, Payment term — flag `b2bCredit` on |
| POL-PTY-004 | Customer credit limit, Payment term — flag `b2bCredit` off |
| POL-PTY-005 | Flag `b2bCredit` guard (decision 3) |
| POL-PTY-006 | Party, Customer, Supplier — quick registration |
| POL-PTY-007 | Customer — global search |
| POL-PTY-008 | Customer credit limit, credit-exposure extension point (decision 7) |
| POL-PTY-009 | Customer (walk-in) — POS sales without a named customer (decision 9) |

AUTO-DECISIONS
AUTO: Address is SHARED (SLS ship-to / bill-to, PUR supplier address, SFR customer area address book); an address belongs to the Party and is held once  FROM: STEP 4 domain best practice; DP §6 (Storefront → Party)  IF WRONG: keep Address PRIVATE; consumers read it only through Customer / Supplier
AUTO: documents (SLS, PUR) copy the party data they need as a snapshot; TAX and SHP read it from the document, not from PTY  FROM: KB §5 fixed DAG (TAX → 5,6; SHP → 6)  IF WRONG: add TAX → PTY and SHP → PTY SOFT-READ edges (tiers unchanged)
AUTO: Customer and Supplier numbers come from given `sequence`  FROM: manifest `sequence` (number-series, document-numbers); G6  IF WRONG: party numbers are entered by hand
AUTO: party created / changed events are published through given `events` for SFR and INS  FROM: manifest `events`; KB §5 rules (crossmodule only)  IF WRONG: consumers read on demand only
AUTO: credit-limit and payment-term changes are recorded in given `audit`  FROM: manifest `audit` (change-history); domain best practice  IF WRONG: base audit columns only
AUTO: the country / region lists are consumed from given `mdl`  FROM: manifest `mdl` (code-lists, master-data)  IF WRONG: PTY masters them as owned lookups
AUTO: an address-type lookup is owned  FROM: STEP 4 domain best practice  IF WRONG: one untyped address list per party
AUTO: supplier payment terms are flag-independent; customer credit limits and customer payment terms are under `b2bCredit`  FROM: KB §4.1 (`b2bCredit` = "wholesale customers: credit limits, terms"); KB §5 #7 (AP ageing needs due dates)  IF WRONG: put supplier terms under `b2bCredit` too
AUTO: a customer with no credit limit set is not credit-checked  FROM: KB §6.2 (nothing imposed)  IF WRONG: treat a missing limit as zero and block every credit sale
AUTO: the walk-in customer never receives a credit limit or credit terms, and cannot be deleted  FROM: KB §6.8 (defaults); domain best practice (credit needs a named party)  IF WRONG: allow an anonymous credit balance and deletion of the seed record
AUTO: no AR/AP netting between the customer and supplier roles of one Party in v1  FROM: KB §5 #7 (AR and AP ageing are separate); decision 4  IF WRONG: add netting to STL scope
AUTO: one credit limit per customer, in the tenant's single currency  FROM: KB §6.8, §8 #4 (multi-currency is advanced)  IF WRONG: promote credit limit to an entity keyed by currency when multi-currency ships

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 4 | Party model | One Party identity record per counterparty, with Customer and Supplier roles over it. The screens still say "Customer" and "Supplier"; adding the other role is an action in place | Converged in dialogue (round 2); user confirms | DP §7.1 (Customer / Supplier "a party that …"); KB §1 (one customer base), §6.4, §6.6 |
| 5 | Customer identity split (OI-13) and guest conversion | Given `sec` owns credentials and the CUSTOMER realm. PTY owns the commercial Customer, linked 0..1 by SOFT-READ. A guest whose contact matches an existing Customer has the order attached to that Customer and is linked through a `sec` customer invitation, never by an unverified contact | Converged in dialogue (round 2); user confirms | KB §3 D5, §5 #4; manifest `sec` (customer-accounts, customer-invitations) |
| 6 | Credit ownership (OI-16) and the graph | PTY owns the credit limit (an attribute of Customer) and the Payment term entity. STL owns balances and ageing. STL fills a PTY credit-exposure extension point, so no upward edge exists; PTY returns the verdict and SLS / SFR enforce it | Converged in dialogue (round 2); user confirms | KB §4.1, §5 #4, #7, §5 rules; manifest contributor pattern (TenantProvisioningContributor, PermissionContributor, PrintParameterContributor) |
| 9 | Walk-in customer | PTY seeds one walk-in customer (عميل نقدي) per tenant; POS uses it when the cashier names no customer. It carries no credit and is protected from deletion (POL-PTY-009) | Converged in dialogue (round 2); user confirms | KB §6.8 (defaults), §6.10 (sell within 10 min); manifest `tenant` (TenantProvisioningContributor) |
══════════════════════════════════════════════════════════════════

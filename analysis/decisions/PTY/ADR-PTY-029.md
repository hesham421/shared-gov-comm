# ADR-PTY-029 — Bootstrap: number series, setting key, role grants, walk-in provisioning

Status  : ACCEPTED (non-breaking) · Stage : P3.1 · Module : PTY v1 · Date : 2026-10-29

## Context
PTY cannot answer its first calls until certain things exist:
- two number series (Party code, request docNo);
- a settings key for the credit-overrun action;
- the role grants of its five pages;
- the walk-in Customer.

The SRS gives the number formats only by example (P-000120, CO-000031). It names no series codes and no settings key. The db-script writes no rows to the given number-series or settings tables (its D-P2-07). Registering a page and deriving its permissions is not a grant: without grants, every endpoint would answer forbidden to every caller.

## Decision
- **Number series:**
  - `PTY_PARTY` — prefix `P-`, 6 digits, for DBF-PTY-007.
  - `PTY_CREDIT_OVERRIDE` — prefix `CO-`, 6 digits, for DBF-PTY-061.

  Both are registered through NumberSeriesApi by PTY's TenantProvisioningContributor for every provisioned tenant. Tenants provisioned before PTY was enabled get the same registration on PTY's startup migration path. The executor confirms the exact NumberSeriesApi registration operation against the erp-core-usage skill.
- **Setting:** `PTY_CREDIT_OVERRUN_ACTION`, stored through the given SettingsApi. It is not seeded: BLOCK_WITH_OVERRIDE applies until the Owner saves a value (REQ-PTY-087).
- **Pages and grants:** PTY's PermissionContributor declares the five pages, together with default grants to the preset roles exactly as SRS B4 lists them:
  - Owner — every listed action;
  - Seller/Cashier — PTY_CUSTOMER VIEW/CREATE/UPDATE, PTY_CUSTOMER_CREDIT VIEW, PTY_CREDIT_OVERRIDE VIEW/CREATE/DELETE;
  - Warehouse — PTY_SUPPLIER VIEW.

  The three credit pages are bound to the flag `b2bCredit` for menu visibility.
- **Walk-in Customer:** created by WalkInCustomerService.ensure. This runs from the TenantProvisioningContributor at provisioning (REQ-PTY-044), and on first demand for older tenants (REQ-PTY-046).
- **Lookup keys:** seeded by the db-script BLOCK 8 in PTY's Flyway migration. New tenants receive them through the given's mdl provisioning copy.

## Consequences
- The `bootstrap` block of the plan lists every row, together with who produces it.
- Customers and suppliers share one Party series, because the code belongs to the Party (SRS D-01 allows separate series later as an override).

## Traces
DBF-PTY-007, DBF-PTY-061 · REQ-PTY-006, REQ-PTY-044, REQ-PTY-046, REQ-PTY-070, REQ-PTY-087, REQ-PTY-092 · RULE-PTY-007, RULE-PTY-031 · SCR-REQ-PTY-001 … 005 (B4)

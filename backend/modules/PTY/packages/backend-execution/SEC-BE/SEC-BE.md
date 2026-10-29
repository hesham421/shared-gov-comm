<!-- source: PHASE:SEC-BE -->
<!-- traces: REQ-PTY-002, REQ-PTY-030, REQ-PTY-043, REQ-PTY-090, REQ-PTY-094, REQ-PTY-096, REQ-PTY-102 -->
<!-- PHASE:SEC-BE:START traces=REQ-PTY-002,REQ-PTY-030,REQ-PTY-043,REQ-PTY-090,REQ-PTY-094,REQ-PTY-096,REQ-PTY-102 -->
## PHASE 5 — SEC-BE

Security model (profile): permission names are `PERM_<PAGE_CODE>_<ACTION>`, with actions VIEW / CREATE / UPDATE / DELETE. VIEW is the gateway: without it no other permission of the page applies. Permission names are minted only from the SRS access matrix (B4 of each screen).
- `com.commerce.pty.permission.PtyPermissions implements PermissionContributor` declares them as constants (checklist C13).
- **Every service method** carries `@PreAuthorize("hasAuthority(T(com.commerce.pty.permission.PtyPermissions).PERM_…)")` referencing PTY's own constants only, and its controller method carries the same. An in-process call through `PtyPartyApi` is checked by the same annotation, so the calling module's security context must hold the PTY authority (ADR-PTY-039).
- A refusal maps through `LocalizedException → {code, messageAr, messageEn}` to PTY-403-FORBIDDEN (unauthenticated → PTY-401-UNAUTHENTICATED). No service checks a role name.
- No service method is exempt from the annotation. Two endpoints add a check of their own, each with its reason:
  - API-PTY-027 (global search): REQ-PTY-043 answers an empty list rather than a refusal. That answer is given at the entry layer: `CustomerController.globalSearch` and `PtyPartyApi.globalSearchCustomers` test PERM_PTY_CUSTOMER_VIEW through `SecurityContextHelper` and return an empty list without it; with it, they call `CustomerQueryService.globalSearch`, annotated `hasAuthority(T(…PtyPermissions).PERM_PTY_CUSTOMER_VIEW)` (ADR-PTY-039 §3, revised);
  - the Address endpoints carry `hasAnyAuthority` of the two role permissions and require in code the one of a role the Party holds — either one when it holds both (ADR-PTY-039 §4).
- API-PTY-038 (lookup read) carries `hasAnyAuthority` of the five VIEW permissions, because every PTY page reads one of PTY's keys (ADR-PTY-048).
- **Credit values on the Customer read (ADR-PTY-046).** PERM_PTY_CUSTOMER_VIEW includes reading creditLimit, paymentTermDays and currencyCode on API-PTY-002 while `b2bCredit` is on. PERM_PTY_CUSTOMER_CREDIT_VIEW gates the credit work: the credit standing with exposure (API-PTY-016), the credit list (API-PTY-030) and the credit history (API-PTY-031). Editing stays under PERM_PTY_CUSTOMER_CREDIT_UPDATE.
- The invitation subscriber runs without a user, as the given outbox runs it. It is not an endpoint and checks no permission.

**SCR-REQ-PTY-001 Customers — page PTY_CUSTOMER.** VIEW: API-PTY-002, 012, 026, 027 (empty list without it), 029, 033, 035, 038 · CREATE: API-PTY-001, 034 · UPDATE: API-PTY-003, 011, 013, 014, 015 (Address actions, by role held) · DELETE: API-PTY-004, 005. Grants (SRS B4): Owner VIEW, CREATE, UPDATE, DELETE · Seller/Cashier VIEW, CREATE, UPDATE · Warehouse none.
**SCR-REQ-PTY-002 Suppliers — page PTY_SUPPLIER.** VIEW: API-PTY-007, 012, 028, 029, 038 · CREATE: API-PTY-006 · UPDATE: API-PTY-008, 011, 013, 014, 015 · DELETE: API-PTY-009, 010. Grants: Owner all four · Seller/Cashier none · Warehouse VIEW.
**SCR-REQ-PTY-003 Customer credit — page PTY_CUSTOMER_CREDIT** (flag `b2bCredit`). VIEW: API-PTY-016, 030, 031, 038 · UPDATE: API-PTY-017, 018. Grants: Owner VIEW, UPDATE · Seller/Cashier VIEW · Warehouse none.
**SCR-REQ-PTY-004 Credit override requests — page PTY_CREDIT_OVERRIDE** (flag `b2bCredit`). VIEW: API-PTY-020, 032, 038 · CREATE: API-PTY-019, 036 · UPDATE: API-PTY-021, 022 (the credit-override permission of POL-PTY-008) · DELETE: API-PTY-023 (cancel own — RULE-PTY-029). Grants: Owner all four · Seller/Cashier VIEW, CREATE, DELETE · Warehouse none. Visibility: without UPDATE a user sees only its own requests (REQ-PTY-102, API-PTY-020, 032).
**SCR-REQ-PTY-005 Credit control settings — page PTY_CREDIT_SETTINGS** (flag `b2bCredit`). VIEW: API-PTY-024, 037, 038 · UPDATE: API-PTY-025 (REQ-PTY-090). Grants: Owner VIEW, UPDATE · others none.

**Seed data.** One `SEC_PAGES` row per composite screen, declared through PTY's `PermissionContributor` (given row `modules.sec`): page code, name ar / en, parent "Party / الأطراف" menu — PTY_CUSTOMER (العملاء / Customers), PTY_SUPPLIER (الموردون / Suppliers), PTY_CUSTOMER_CREDIT (ائتمان العملاء / Customer credit), PTY_CREDIT_OVERRIDE (طلبات تجاوز حد الائتمان / Credit override requests), PTY_CREDIT_SETTINGS (إعدادات ضبط الائتمان / Credit control settings); the given derives one permission per action; column names are the given's, not PTY's. The contributor also declares the default grants above to the three preset roles (ADR-PTY-029) — registering a page is not a grant. The three credit pages are hidden from menus while `b2bCredit` is off (REQ-PTY-070) through the page's flag binding on the contributor; their endpoints refuse with PTY-422-CREDIT-NOT-ENABLED where RULE-PTY-023 applies.

**In-process callers.** A consuming module calling `PtyPartyApi` runs under its own caller's security context. That context must hold the PTY authority of the method it calls, which is the same `@PreAuthorize` as the REST endpoint (ADR-PTY-039, revising ADR-PTY-025). The Seller/Cashier preset holds what a sale needs: PERM_PTY_CUSTOMER_VIEW (CON-PTY-009, 013, 015) and PERM_PTY_CREDIT_OVERRIDE_CREATE (CON-PTY-016, 018). A caller with no PTY authority is refused until `CORE-GAP-in-process-caller-authority` is answered: the anonymous storefront shopper, a CUSTOMER-realm shopper, or a system job. Tenancy is resolved by the given for every call.

```yaml name=permission-matrix
rows:
  - screen: SCR-REQ-PTY-001 Customers
    entity: ENT-PTY-002
    api: [API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-026, API-PTY-027, API-PTY-029, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-038]
    actions:
      VIEW: {granted: true, permission: PERM_PTY_CUSTOMER_VIEW}
      CREATE: {granted: true, permission: PERM_PTY_CUSTOMER_CREATE}
      UPDATE: {granted: true, permission: PERM_PTY_CUSTOMER_UPDATE}
      DELETE: {granted: true, permission: PERM_PTY_CUSTOMER_DELETE}
  - screen: SCR-REQ-PTY-002 Suppliers
    entity: ENT-PTY-003
    api: [API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-012, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-028, API-PTY-029, API-PTY-038]
    actions:
      VIEW: {granted: true, permission: PERM_PTY_SUPPLIER_VIEW}
      CREATE: {granted: true, permission: PERM_PTY_SUPPLIER_CREATE}
      UPDATE: {granted: true, permission: PERM_PTY_SUPPLIER_UPDATE}
      DELETE: {granted: true, permission: PERM_PTY_SUPPLIER_DELETE}
  - screen: SCR-REQ-PTY-003 Customer credit
    entity: ENT-PTY-002
    api: [API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-030, API-PTY-031, API-PTY-038]
    actions:
      VIEW: {granted: true, permission: PERM_PTY_CUSTOMER_CREDIT_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: true, permission: PERM_PTY_CUSTOMER_CREDIT_UPDATE}
      DELETE: {granted: false}
  - screen: SCR-REQ-PTY-004 Credit override requests
    entity: ENT-PTY-005
    api: [API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-032, API-PTY-036, API-PTY-038]
    actions:
      VIEW: {granted: true, permission: PERM_PTY_CREDIT_OVERRIDE_VIEW}
      CREATE: {granted: true, permission: PERM_PTY_CREDIT_OVERRIDE_CREATE}
      UPDATE: {granted: true, permission: PERM_PTY_CREDIT_OVERRIDE_UPDATE}
      DELETE: {granted: true, permission: PERM_PTY_CREDIT_OVERRIDE_DELETE}
  - screen: SCR-REQ-PTY-005 Credit control settings
    entity: ENT-PTY-002
    api: [API-PTY-024, API-PTY-025, API-PTY-037, API-PTY-038]
    actions:
      VIEW: {granted: true, permission: PERM_PTY_CREDIT_SETTINGS_VIEW}
      CREATE: {granted: false}
      UPDATE: {granted: true, permission: PERM_PTY_CREDIT_SETTINGS_UPDATE}
      DELETE: {granted: false}
```

**Bootstrap data.** The rows that must exist before an endpoint of PTY can succeed, each with who produces it:

```yaml name=bootstrap
rows:
  - {kind: lookup key, name: "CUSTOMER_GROUP", source: "PTY Flyway migration — db-script §4 BLOCK 8a / 8b (idempotent) into the given mdl lookup tables for TENANT_ID = 1, OWNER_MODULE_CODE PTY; values RETAIL, WHOLESALE", note: "read by API-PTY-001, 003 (RULE-PTY-018); new tenants receive it through the given's provisioning copy of mdl seeds; served to the screens by API-PTY-038 (OwnedLookups, ADR-PTY-048)"}
  - {kind: lookup key, name: "ADDRESS_TYPE", source: "PTY Flyway migration — db-script §4 BLOCK 8; values BILLING, DELIVERY", note: "read by API-PTY-011, 013 (RULE-PTY-016), API-PTY-034 (DELIVERY); served to the screens by API-PTY-038 (OwnedLookups, ADR-PTY-048)"}
  - {kind: lookup key, name: "CREDIT_OVERRUN_ACTION", source: "PTY Flyway migration — db-script §4 BLOCK 8; values WARN, BLOCK_WITH_OVERRIDE", note: "read by API-PTY-025; served to the screens by API-PTY-038 (OwnedLookups, ADR-PTY-048)"}
  - {kind: lookup key, name: "CREDIT_CHECK_RESULT", source: "PTY Flyway migration — db-script §4 BLOCK 8; values WITHIN_LIMIT, OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE, NOT_APPLICABLE", note: "labels for API-PTY-035 results; served to the screens by API-PTY-038 (OwnedLookups, ADR-PTY-048)"}
  - {kind: lookup key, name: "CREDIT_OVERRIDE_STATUS", source: "PTY Flyway migration — db-script §4 BLOCK 8; values PENDING, APPROVED, REJECTED, CANCELLED, USED", note: "labels for RULE-PTY-026 {statusAr}/{statusEn}; served to the screens by API-PTY-038 (OwnedLookups, ADR-PTY-048)"}
  - {kind: page, name: "PTY_CUSTOMER", source: "PTY PermissionContributor (com.erp.sec.permission.PermissionContributor) — the given sec writes SEC_PAGES at startup and for every provisioned tenant"}
  - {kind: page, name: "PTY_SUPPLIER", source: "PTY PermissionContributor — given sec SEC_PAGES"}
  - {kind: page, name: "PTY_CUSTOMER_CREDIT", source: "PTY PermissionContributor — given sec SEC_PAGES", note: "menu bound to flag b2bCredit"}
  - {kind: page, name: "PTY_CREDIT_OVERRIDE", source: "PTY PermissionContributor — given sec SEC_PAGES", note: "menu bound to flag b2bCredit"}
  - {kind: page, name: "PTY_CREDIT_SETTINGS", source: "PTY PermissionContributor — given sec SEC_PAGES", note: "menu bound to flag b2bCredit"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_VIEW", source: "derived by the given sec from page PTY_CUSTOMER; default grant declared by PTY PermissionContributor (ADR-PTY-029)", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_CREATE", source: "given sec from PTY_CUSTOMER; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_UPDATE", source: "given sec from PTY_CUSTOMER; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_DELETE", source: "given sec from PTY_CUSTOMER; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_SUPPLIER_VIEW", source: "given sec from PTY_SUPPLIER; grant by PTY PermissionContributor", note: "role: Owner, Warehouse"}
  - {kind: permission, name: "PERM_PTY_SUPPLIER_CREATE", source: "given sec from PTY_SUPPLIER; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_SUPPLIER_UPDATE", source: "given sec from PTY_SUPPLIER; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_SUPPLIER_DELETE", source: "given sec from PTY_SUPPLIER; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_CREDIT_VIEW", source: "given sec from PTY_CUSTOMER_CREDIT; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CUSTOMER_CREDIT_UPDATE", source: "given sec from PTY_CUSTOMER_CREDIT; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_CREDIT_OVERRIDE_VIEW", source: "given sec from PTY_CREDIT_OVERRIDE; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CREDIT_OVERRIDE_CREATE", source: "given sec from PTY_CREDIT_OVERRIDE; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier"}
  - {kind: permission, name: "PERM_PTY_CREDIT_OVERRIDE_UPDATE", source: "given sec from PTY_CREDIT_OVERRIDE; grant by PTY PermissionContributor", note: "role: Owner — the approvers notified by API-PTY-019"}
  - {kind: permission, name: "PERM_PTY_CREDIT_OVERRIDE_DELETE", source: "given sec from PTY_CREDIT_OVERRIDE; grant by PTY PermissionContributor", note: "role: Owner, Seller/Cashier (own requests only, RULE-PTY-029)"}
  - {kind: permission, name: "PERM_PTY_CREDIT_SETTINGS_VIEW", source: "given sec from PTY_CREDIT_SETTINGS; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: permission, name: "PERM_PTY_CREDIT_SETTINGS_UPDATE", source: "given sec from PTY_CREDIT_SETTINGS; grant by PTY PermissionContributor", note: "role: Owner"}
  - {kind: number series, name: "PTY_PARTY", source: "PTY Flyway migration V1001 — db-script §4 BLOCK 8c inserts the CORE_NUMBER_SERIES row for TENANT_ID = 1 (ADR-PTY-036); the given copies it to every new tenant with the counter at 1", note: "prefix P, pattern {PREFIX}-{SEQ:6}, reset NEVER; DBF-PTY-007"}
  - {kind: number series, name: "PTY_CREDIT_OVERRIDE", source: "PTY Flyway migration V1001 — db-script §4 BLOCK 8c, TENANT_ID = 1 (ADR-PTY-036); copied to new tenants by the given", note: "prefix CO, pattern {PREFIX}-{SEQ:6}, reset NEVER; DBF-PTY-061"}
  - {kind: notification template, name: "PTY_CREDIT_OVERRIDE_REQUESTED", source: "PTY Flyway migration V1001 — db-script §4 BLOCK 8d inserts the NOTIF_TEMPLATE row for TENANT_ID = 1 (ADR-PTY-036); copied to new tenants by the given NotifTenantProvisioningContributor", note: "IN_APP, UTILITY, variables {docNo} {name}; dispatched by PtyApproverNotificationListener (API-PTY-019)"}
  - {kind: setting, name: "PTY_BASE_CURRENCY_CODE", source: "set per tenant at setup through /api/v1/common/configurations (given cu); not seeded — XXX is written while it is unset (ADR-PTY-037, CORE-GAP-tenant-base-currency)", note: "read by PtyCurrency for DBF-PTY-078"}
  - {kind: data row, name: "walk-in Customer", source: "PTY TenantProvisioningContributor → WalkInCustomerService.ensure at provisioning (REQ-PTY-044); on first demand by API-PTY-033 for tenants provisioned earlier (REQ-PTY-046)"}
  - {kind: setting, name: "PTY_CREDIT_OVERRUN_ACTION", source: "not seeded — absent until the Owner saves it through API-PTY-025; BLOCK_WITH_OVERRIDE applies meanwhile (REQ-PTY-087)"}
```

The frontend stage references these permission names; it does not redeclare them.
<!-- PHASE:SEC-BE:END -->

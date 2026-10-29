All 22 findings (G1–G22) from the analysis gate are now applied to the PTY artifacts. I edited the files in place, as in the earlier repair passes, rather than returning complete-file blocks; `git diff` shows every change. My own consistency script passes, and every ID count matches its header. I did not run the toolkit's `gov.py` gate, so that is still the real test.

**What the checks confirmed**
- All 136 acceptance criteria and all 113 requirements have at least one test case across the two test plans.
- The backend test plan has 146 test cases and the frontend plan 44, with no duplicate IDs.
- 79 database fields, 40 queries, 37 API operations (valid YAML) and 37 error codes.
- The script flags one difference on API-PTY-001. This is wrong: it is reading the "PTY-002" inside "RULE-PTY-002".

**How each finding was fixed**

| # | Fix |
|---|---|
| G1 | "Open that party" now finds the existing party by email in the Customer search, then the Supplier search (new email filter on API-028). The temporary frontend workaround is gone. TC-123 is updated and TC-179 adds the Supplier-only case. Recorded in ADR-043. |
| G2 / G21 | Every table has TENANT_ID with a foreign key, a leading index and composite uniques. Seeds use tenant 1, and the two number series (P, CO) are seeded. Recorded in ADR-035 and ADR-036; ADR-021 is superseded and ADR-022 and ADR-029 are revised. |
| G3 | Every query includes TENANT_ID. QR-011 and QR-037 are marked `@TenantUnscoped`. |
| G4 | The role-deactivated event now goes out through `OutboxPublisher` as "pty.PartyRoleDeactivated". SRS A9, contract Part 4 and TC-072 match (ADR-038). |
| G5 / G14 | Permission checks moved onto the service methods, and either UPDATE permission is enough. ADR-039 revises ADR-025. CORE-GAP-in-process-caller-authority covers the anonymous shopper. New TCs: 167 and 177. |
| G6 | The approver notification uses the new template PTY_CREDIT_OVERRIDE_REQUESTED (TC-115). |
| G7 | Entities extend `AuditableEntity`, and the domain rules sit in companion classes. |
| G8 | The module manifest `pty.yaml`, a module-boundary test and a CI job are added. |
| G9 | The listeners run after commit, on the erp-core executor inside `TenantContext.runAs`. The invitation-acceptance outbox subscriber replaces the old invitation handler. |
| G10 | The address-ordering decision is recorded (ADR-040) and CORE-GAP-ordinal-within-parent is raised. |
| G11 | CURRENCY_CODE is added to the customer and to the override request (ADR-037, superseding ADR-020). CORE-GAP-tenant-base-currency is raised. |
| G12 | Inactive customers and parties are covered by REQ-110–112 and a RULE-005 extension. Deactivating cancels their open requests (ADR-041). |
| G13 | Only one open request is allowed per sale, enforced by a partial unique index and the error PTY-409-REQUEST-OPEN-FOR-SALE. A request can only be used on the sale it was raised for (ADR-042, RULE-034, REQ-113). |
| G15 | RULE-030 now also covers approve and reject decisions (AC-134, TC-164 and TC-190). |
| G16 | TC-168–176 test concurrent requests. |
| G17 | TC-179–190 cover the frontend behaviour that had no test before. |
| G18 | TC-138's actor is now an Owner, and the open gap F-N4 is removed. |
| G19 | TC-020 now names the lookup values it needs created on the host (VIP, OFFICE). Checks C10.8/C10.9 examined nothing because every listed value was already seeded; N4 explains this. |
| G20 | ADR-034 §9 is removed and points to ADR-043. |
| G22 | `@Audited` is on the customer's credit fields and on the override request. |

New decision files: ADR-PTY-035 to 043 (all ACCEPTED) and the three CORE-GAP files. Next free IDs: ADR-044, DBF-080, QR-041, REQ-114, AC-137, RULE-035 and TC-191.

**Things to check**
- **Assumed columns:** the NOTIF_TEMPLATE seed (BLOCK 8d in the DB script) uses column names I took from the conventions. I could not check them against a real schema.
- **Base currency:** until CORE-GAP-tenant-base-currency is answered, it comes from the setting PTY_BASE_CURRENCY_CODE, with "XXX" if the setting is missing.
- **Anonymous shopper:** until that gap is answered, the strict permission checks apply. No v1 caller is blocked by this, because the storefront module comes in wave 2.
- **Line endings:** `backend-execution-plan-pty.md` changed from CRLF to LF to match the other P1–P4 files, so its diff shows every line as changed.
- **Snapshots:** the `_state/` snapshot files still describe the old design. I left them alone because the pipeline maintains them.
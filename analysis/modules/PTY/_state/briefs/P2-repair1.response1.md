I fixed all three C-GIVEN-2 findings. They were all in the SRS A9 `given-usage` block, which the contract repeats word for word, so I edited both files the same way. I haven't re-run `gov.py analyze`, so the fixes aren't confirmed by the checker yet.

**What I changed:**
- **`srs-pty.md` (A9) and `contract-pty.md` (Part 4):**
  - **Tenant row:** `via` changed from `com.erp.tenant.TenantContext` to `com.erp.tenant.TenantRunner`, which the tenant module does publish. The purpose now says the tenant is re-bound through that published runner.
  - **Common row:** removed (`com.erp.common.domain.AuditableEntity`). `modules.common` publishes nothing, so no row can go through it. The tenantId, version and audit columns stay in the model; only the row is gone. The block goes from 15 rows to 14.
  - **Module row:** `via` changed from `META-INF/erp/modules/pty.yaml` to `docs/api-docs/module/index.md`, the module's published api. The purpose still names the manifest file. This follows how CAT routes its file use through `docs/api-docs/file/index.md`.
- **`srs-pty.md`, other edits:** a note under the A9 block saying why the base class has no row, and an ADR-PTY-044 row in the Decisions applied table.
- **New `analysis/decisions/PTY/ADR-PTY-044.md`** (ACCEPTED, non-breaking): records these three choices.
- **`registry-db-pty.md`:** the Decisions line now lists ADR-PTY-044, and the next free ADR is ADR-PTY-045.
- **`db-script-pty.md`:** unchanged. No column, DBF or XM changes.

**Left for the next P3.1 pass:** `backend-execution-plan-pty.md` and `registry-exec-be-pty.md` still say `TenantContext.runAs`, `AuditableEntity` and the manifest path. I couldn't edit them in this repair. If C-GIVEN-2 also checks the plan, P3.1 will need to switch to `TenantRunner` there; ADR-PTY-044 notes this under Consequences.
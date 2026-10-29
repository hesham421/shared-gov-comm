# ADR-PTY-047 — One application root namespace for every business module: `com.commerce`

Status  : ACCEPTED (non-breaking) · Stage : gate-analysis revise 2 (G12) · Module : PTY v1 (platform-wide rule) · Date : 2026-10-29

## Context
The PTY backend plan put the module under `com.erp.pty` (CORE module layout, manifest `package: com.erp.pty`, `com.erp.pty.crossmodule.PtyPartyApi`, `com.erp.pty.permission.PtyPermissions`). `com.erp.*` is the namespace of the given erp-core: its manifest lists `com.erp.sec`, `com.erp.mdl`, `com.erp.common` and the others. Checklist F8 forbids "a `com.erp.*` package in the application", and the library's own module example uses an application namespace (`package: com.acme.rental.leasing`). CAT v2 shows the same pattern (`com.erp.cat`), so the defect sits at the rule level, not only in PTY.

## Decision
1. **Root namespace.** Every business module of this project lives under the application root `com.commerce`, taken from the project id `commerce`. Module `<code>` is the package `com.commerce.<code lower case>`: `com.commerce.cat`, `com.commerce.pty`, `com.commerce.prc`, and so on. No business module declares a package under `com.erp`.
2. **Recorded once at platform level.** The rule is decision 11 of the platform summary (P0). The domain profile and the factory profile are outside this module's writable set, so a platform finding asks for the rule to be copied there (business-policies-pty.md, PLATFORM FINDINGS row 1).
3. **PTY.** The root package becomes `com.commerce.pty`. This covers the CORE module layout, the manifest entry `package: com.commerce.pty`, `com.commerce.pty.crossmodule.PtyPartyApi`, `com.commerce.pty.permission.PtyPermissions`, `com.commerce.pty.exception.PtyErrorCodes`, `com.commerce.pty.tenant.PtyTenantProvisioningContributor`, `com.commerce.pty.currency.PtyCurrency`, the `@PreAuthorize` expressions, the Javadoc and README of DOC, and the test-plan references. The boundary rule keeps its shape: consumers may import `com.commerce.pty.crossmodule` only.
4. **Given packages are unchanged.** `com.erp.sec…`, `com.erp.common…`, `com.erp.events…` and the others remain the given's, imported as they are.
5. **CAT.** CAT's next pass applies the same rename (`com.erp.cat` → `com.commerce.cat`). That is filed as a platform finding (business-policies-pty.md, PLATFORM FINDINGS row 1), because CAT's artifacts are outside this pass.
6. **A vendor domain.** If the project later adopts a vendor domain (`com.<vendor>.commerce`), one platform-wide rename replaces `com.commerce` before the first module is delivered. No code exists yet.

## Consequences
- No endpoint, table, permission or contract signature changes. CON items name no Java package.
- `PtyModuleBoundaryTest` checks `com.commerce.pty.crossmodule`.

## Traces
erp-core checklist F8, C13, C18, C20 · backend plan PHASE CORE, SEC-BE, DOC · CORE-GAP-in-process-caller-authority · platform summary decision 11 · ADR-PTY-039

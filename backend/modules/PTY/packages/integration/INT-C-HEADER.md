<!-- source: PHASE:INT-C — the phase around its blocks -->
<!-- traces: REQ-PTY-023, REQ-PTY-080, REQ-PTY-084 -->
## PHASE 7 — INT-C

The one place this plan speaks about another module. **PTY v1 has no outbound cross-module edge**: the db-script's XM register is `records: []` (SRS A8 `consumes: []` — Party is DAG #4 and depends on no business module), and this stage mints none — nothing in this plan reads another module's data. There is therefore no `XM-*` block, no `ddl_patch`, no adapter and no integration package.

- Access mechanism (CM-5): `profile.conventions.module_interface: in_process` — one deployable; PTY's dependencies are the given erp-core only (CORE, given usage), reached through the `via` interfaces the SRS names. No HTTP client, no base URL, no network error path exists in this module; no Error Catalog row describes one.
- Direction (CM-10): no PTY dependency points to any business module, so none can point upward.
- Inbound: the modules below PTY reach it through its published contract (CON-PTY-001 … CON-PTY-024):
  - the in-process `PtyPartyApi`, whose PTY authority the caller must hold (ADR-PTY-039);
  - the extension point `CustomerCreditExposureContributor` (CON-PTY-022). Downstream modules implement it, and PTY calls whatever beans exist (REQ-PTY-080, REQ-PTY-084).
  - the outbox event `pty.PartyRoleDeactivated` (CON-PTY-021, REQ-PTY-023, ADR-PTY-038), consumed by idempotent `OutboxSubscriber`s.
- PTY consumes one given event, `sec.CustomerInvitationRedeemed`. It belongs to the given erp-core, not to a business module, so it is no XM edge. Those edges are theirs, written in their own plans and derived by `gov.py graph` into `platform/dependency-graph.json`; this phase carries no state for them.

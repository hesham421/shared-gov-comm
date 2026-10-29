<!-- source: PHASE:DOC -->
<!-- traces: REQ-PTY-001, REQ-PTY-002, REQ-PTY-019, REQ-PTY-032, REQ-PTY-079, REQ-PTY-091, REQ-PTY-103 -->
<!-- PHASE:DOC:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-019,REQ-PTY-032,REQ-PTY-079,REQ-PTY-091,REQ-PTY-103 -->
## PHASE 4 — DOC

- **API document.** `api-spec-pty.yaml` (OpenAPI 3.1.0) is derived from the 38 `API-*` blocks and the Error Catalog of this plan: one operation per block (`x-api-id`, `x-traces`, `operationId` = the block heading), request and response schemas typed from the db-script through the type mapping of CORE, one error response per catalog row the block raises (`x-error-codes`), `x-paginated: true` with `page` / `size` on the five paginated searches, and the bearer-JWT scheme on every operation. The frontend stage binds to it and serves it from a mock; `api-verify` holds the delivered api-docs to it.
- **Delivered api-docs.** Each controller method carries springdoc annotations (`@Operation(operationId, summary)`, `@ApiResponse` per status of its `Errors` line, `@Tag(name = "pty-<aggregate>")`) so the published `/v3/api-docs` matches the document operation for operation; the `x-error-codes` extension is emitted by an `OperationCustomizer` reading a `@PtyErrors({...})` annotation that lists the block's codes.
- **Message bundles (checklist C11).** Every catalog code is a constant of `PtyErrorCodes` and has `messages.properties` (en) / `messages_ar.properties` (ar) keys `pty.error.<CODE>` holding the catalog text verbatim. The confirmations (RULE-PTY-006, RULE-PTY-017), the switch-off answer (RULE-PTY-024) and the invitation-subscriber outcomes (RULE-PTY-013, RULE-PTY-020) use keys `pty.message.<RULE-id>`. The approver notification's text is the seeded template PTY_CREDIT_OVERRIDE_REQUESTED (db-script BLOCK 8d), not a bundle key.
- **Module README** (`backend/modules/PTY/README.md` in the delivery partition) covers:
  - the published in-process surface (`PtyPartyApi`, `CustomerCreditExposureContributor`) and the outbox event `pty.PartyRoleDeactivated`, with its payload and the subscriber idempotence rule;
  - the CON-PTY item each method honours, and the PTY authority each method requires (ADR-PTY-039);
  - the flag `b2bCredit` behaviour, the setting `PTY_BASE_CURRENCY_CODE`, and the bootstrap rows of SEC-BE;
  - the module manifest `META-INF/erp/modules/pty.yaml` (CORE), with `package: com.commerce.pty` and the root-namespace rule of ADR-PTY-047.
- **Javadoc** on `com.commerce.pty.crossmodule.PtyPartyApi` and `com.commerce.pty.crossmodule.CustomerCreditExposureContributor` states, per method, the contract item, the errors (catalog codes) and the 3-second budget of a contributor (REQ-PTY-084).
<!-- PHASE:DOC:END -->

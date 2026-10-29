<!-- source: PHASE:F2 — preamble before the first SUB -->
<!-- traces: REQ-PTY-001, REQ-PTY-004, REQ-PTY-005, REQ-PTY-006, REQ-PTY-007, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-012, REQ-PTY-013, REQ-PTY-014, REQ-PTY-015, REQ-PTY-016, REQ-PTY-017, REQ-PTY-018, REQ-PTY-020, REQ-PTY-021, REQ-PTY-022, REQ-PTY-023, REQ-PTY-024, REQ-PTY-025, REQ-PTY-026, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-039, REQ-PTY-040, REQ-PTY-041, REQ-PTY-047, REQ-PTY-048, REQ-PTY-049, REQ-PTY-064, REQ-PTY-065, REQ-PTY-066, REQ-PTY-067, REQ-PTY-068, REQ-PTY-069, REQ-PTY-070, REQ-PTY-071, REQ-PTY-072, REQ-PTY-073, REQ-PTY-077, REQ-PTY-078, REQ-PTY-080, REQ-PTY-085, REQ-PTY-086, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-090, REQ-PTY-091, REQ-PTY-092, REQ-PTY-093, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-098, REQ-PTY-100, REQ-PTY-101, REQ-PTY-102, REQ-PTY-103, REQ-PTY-104, REQ-PTY-105, REQ-PTY-106, REQ-PTY-107, REQ-PTY-108, REQ-PTY-109, REQ-PTY-110, REQ-PTY-112, REQ-PTY-113, REQ-PTY-114, AC-PTY-007, AC-PTY-010, AC-PTY-011, AC-PTY-013, AC-PTY-018, AC-PTY-021, AC-PTY-029, AC-PTY-031, AC-PTY-036, AC-PTY-055, AC-PTY-056, AC-PTY-057, AC-PTY-077, AC-PTY-078, AC-PTY-080, AC-PTY-082, AC-PTY-087, AC-PTY-088, AC-PTY-096, AC-PTY-097, AC-PTY-098, AC-PTY-101, AC-PTY-102, AC-PTY-106, AC-PTY-107, AC-PTY-109, AC-PTY-111, AC-PTY-114, AC-PTY-115, AC-PTY-116, AC-PTY-117, AC-PTY-122, AC-PTY-123, AC-PTY-132, AC-PTY-134, API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-027, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, API-PTY-038, UXD-PTY-001, SCR-PTY-001, SCR-PTY-002, SCR-PTY-003, SCR-PTY-004, SCR-PTY-005 -->
## F2 — Data Hooks

What each screen reads and mutates. Hook code is the implementer's; this phase fixes the operation each hook calls (by `API-*` id of api-spec-pty.yaml — method, path and schemas are read there, never restated), its cache key, error routing, loading and invalidation.

**State rule.** Page and page size live inside the filter object that forms the cache key, never as separate state; the filter object is mirrored in the route's search parameters, so a deep link reproduces the list. Components use the screen's facade only; the facade uses the declared hooks only (server state: tanstack-query; the only zustand store is the shell's session/flags store read by the INIT hooks).

**Cache policy.** tanstack-query defaults for every PTY read. Two deviations, both ADR-PTY-034: lookups are long-lived (`staleTime` 30 min, `gcTime` 60 min — the engine's lookup rule); the application configuration is refetched on every route mount (`staleTime` 0, `refetchOnMount: 'always'`) so a `b2bCredit` change shows from the next screen load (REQ-PTY-073).

**Loading.** `LOCAL` everywhere (placeholder rows / a busy Save button). No call is declared slow by the SRS, so nothing is `GLOBAL`.

**Standard routing (STD)** — applied by every hook in addition to its own rows; text is always the server's `messageAr` / `messageEn` in the active language:
- `PTY-400-VALIDATION` → inline on the field the request names when it maps to a form field, else a form banner; text: catalogue PTY-400-VALIDATION.
- `PTY-401-UNAUTHENTICATED` → login (the shell's sign-in, returning to the current route).
- `PTY-403-FORBIDDEN` → on a read: the unauthorized page; on a mutation: a form banner (the form is kept); text: catalogue PTY-403-FORBIDDEN.
- `PTY-404-*-NOT-FOUND` → the screen's not-found state (ui-ux-spec States); text: catalogue code.
- `PTY-500-INTERNAL` → generic error with Retry; text: catalogue PTY-500-INTERNAL.

Rule-bound refusals (`codes` of the API binding) are routed per hook below as `CODE → <place>, text: RULE-PTY-<seq> message (SRS)`; the SRS A5 message is the text in ar and en, and the server returns exactly that text.

### Shared hooks (used by several screens)

#### LOOKUP hooks — one per key, shared across screens
`CUSTOMER_GROUP-LOOKUP`, `ADDRESS_TYPE-LOOKUP`, `CREDIT_OVERRUN_ACTION-LOOKUP`, `CREDIT_CHECK_RESULT-LOOKUP`, `CREDIT_OVERRIDE_STATUS-LOOKUP` · kind `lookup` · operation API-PTY-038 `GET /api/v1/pty/lookups/{key}` — PTY serves its own keys through `OwnedLookups` (ADR-PTY-048, amending ADR-PTY-034) · key `["pty","lookups",<KEY>]` · options shape `LookupOption` (code + label ar + label en + sort order) · long-lived cache · errors STD (`PTY-404-LOOKUP-KEY-UNKNOWN` cannot occur for the five fixed keys; if it does, the chooser shows the generic error with Retry). Choosers show the active values API-PTY-038 returns; a row whose stored code is no longer active renders the code itself, with no label.

#### APP-CONFIG-INIT and PERMISSIONS-INIT
Kind `init`. `APP-CONFIG-INIT` reads the given application configuration (`GET /api/public/app-config`, KB technical base — feature flags) and exposes `b2bCredit`; key `["app-config"]`; refetched on every route mount. `PERMISSIONS-INIT` reads the session's permission names from the given `sec` session (key `["auth","permissions"]`). Each screen's SCREEN-INIT composes them.

#### PARTY-ADDRESSES-QUERY — API-PTY-029     traces=API-PTY-029,REQ-PTY-032
Kind read · key `["pty","parties",partyFk,"addresses"]` · enabled once `partyFk` is known (an existing Customer or Supplier) · errors STD (`PTY-404-PARTY-NOT-FOUND` → not-found state) · loading LOCAL. Returns active Addresses only; the pane is built from it.

#### ADDRESSES-SYNC — API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015     traces=API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,REQ-PTY-030,REQ-PTY-031,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038
Kind mutation · called only by a screen's SAVE, after the record call succeeded, with the record's `partyFk` · sends the draft's changes **sequentially** in this order: removed lines → API-PTY-014; changed lines → API-PTY-013; new lines → API-PTY-011; lines marked default that are not already the default → API-PTY-015 with `{"isDefaultFl": true}` (ADR-PTY-031). A new line that is the first of its type is not sent to API-PTY-015 — the server makes it the default (REQ-PTY-034). Not sent at all when no line changed. Stops at the first failure; the remaining lines stay in the draft marked unsent.
Errors: `PTY-422-ADDRESS-LINE-REQUIRED` → inline on line1 of the failing line (its drawer reopens), text: RULE-PTY-015 message (SRS) · `PTY-422-ADDRESS-TYPE-INVALID` → inline on addressTypeCode of the failing line, text: RULE-PTY-016 message (SRS) · `PTY-422-ROLE-INACTIVE` (the Party has no active role — ADR-PTY-041) → form banner, the remaining lines stay in the draft marked unsent, text: RULE-PTY-005 message (SRS) · `PTY-404-ADDRESS-NOT-FOUND` / `PTY-404-PARTY-NOT-FOUND` → form banner + refetch of the addresses, text: catalogue code · STD.
Success: the confirmation texts the responses carry (RULE-PTY-006 on deactivation, RULE-PTY-017 on a new default) are shown once as a toast. Invalidation: `["pty","parties",partyFk,"addresses"]`.

#### EMAIL-HOLDER-QUERY — API-PTY-026, API-PTY-028     traces=API-PTY-026,API-PTY-028,REQ-PTY-010,REQ-PTY-013,REQ-PTY-018
Kind read. It runs only when the user chooses "Open that party" «فتح ذلك الطرف» on a PTY-409-EMAIL-TAKEN refusal (ADR-PTY-043).
1. Read API-PTY-026 with `{email: <entered email>, isActive: true, page: 0, size: 20}`.
2. Keep the row whose `email`, trimmed and lower-cased, equals the entered one. Found → navigate to `/pty/customers/:customerPk`.
3. Otherwise, when the session holds PERM_PTY_SUPPLIER_VIEW, read API-PTY-028 with the same filter and keep the matching row. Found → navigate to `/pty/suppliers/:supplierPk`.
4. Neither found (the user may not see the role the Party holds) → the action is withdrawn; the message, which names the Party code, stays.
Keys `["pty","customers","search",{…}]` and `["pty","suppliers","search",{…}]`, the same cache entries as the search screens. Errors: STD (a failure leaves the refusal shown, with no navigation). Loading: LOCAL, on the action.

#### USER-LABEL-LOOKUP — UXD-PTY-001     traces=UXD-PTY-001,REQ-PTY-094,REQ-PTY-103
Kind lookup · the given `sec` user read (ADR-PTY-034) · key `["sec","users",userId]` · long-lived · one call per distinct identifier · on failure the field reads «—» (never the raw identifier, never an error page).

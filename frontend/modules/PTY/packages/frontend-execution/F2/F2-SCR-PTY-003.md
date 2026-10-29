<!-- source: PHASE:F2 / SUB:F2-SCR-PTY-003 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-056, AC-PTY-077, AC-PTY-080, AC-PTY-087, AC-PTY-088, AC-PTY-096, AC-PTY-122, AC-PTY-123, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-030, API-PTY-031, API-PTY-038, REQ-PTY-048, REQ-PTY-064, REQ-PTY-065, REQ-PTY-066, REQ-PTY-067, REQ-PTY-068, REQ-PTY-069, REQ-PTY-070, REQ-PTY-072, REQ-PTY-073, REQ-PTY-077, REQ-PTY-078, REQ-PTY-080, REQ-PTY-085, REQ-PTY-106, REQ-PTY-108, REQ-PTY-109, REQ-PTY-110, SCR-PTY-003 -->
<!-- SUB:F2-SCR-PTY-003:START traces=REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-106,REQ-PTY-108,REQ-PTY-109,REQ-PTY-110,AC-PTY-056,AC-PTY-077,AC-PTY-080,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-122,AC-PTY-123,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-030,API-PTY-031,API-PTY-038,SCR-PTY-003 -->
### F2 · SCR-PTY-003 — Customer credit

```yaml name=screen-hooks
screen: SCR-PTY-003
hooks:
  - hook: CREDIT-SEARCH-QUERY
    kind: read
    api: [API-PTY-030]
    cache_key: "[pty, customer-credits, search, {code, name, customerGroupCode, carriesCredit, page, size, sort}]"
    errors: "STD; PTY-422-CREDIT-NOT-ENABLED → close to home with a banner, text: RULE-PTY-023 message (SRS), and refetch app-config"
    loading: LOCAL
    invalidation: "—"
  - hook: CREDIT-STANDING-QUERY
    kind: read
    api: [API-PTY-016]
    cache_key: "[pty, customer-credits, customerId]"
    errors: "STD; PTY-404-CUSTOMER-NOT-FOUND → not-found state, text: catalogue PTY-404-CUSTOMER-NOT-FOUND; PTY-422-CREDIT-NOT-ENABLED → close to home with a banner, text: RULE-PTY-023 message (SRS)"
    loading: LOCAL
    invalidation: "—"
  - hook: CREDIT-HISTORY-QUERY
    kind: read
    api: [API-PTY-031]
    cache_key: "[pty, customer-credits, customerId, history, {page, size}]"
    errors: "STD; PTY-404-CUSTOMER-NOT-FOUND → not-found state; PTY-422-CREDIT-NOT-ENABLED → close to home with a banner, text: RULE-PTY-023 message (SRS)"
    loading: LOCAL
    invalidation: "—"
  - hook: CUSTOMER_GROUP-LOOKUP
    kind: lookup
    api: [API-PTY-038]
    cache_key: "[pty, lookups, CUSTOMER_GROUP]"
    loading: LOCAL
  - hook: CREDIT-SCREEN-INIT
    kind: init
    cache_key: "[app-config] + [auth, permissions]"
    loading: LOCAL
  - hook: CREDIT-SAVE
    kind: mutation
    api: [API-PTY-017]
    errors: "STD; PTY-422-CREDIT-LIMIT-NEGATIVE → inline on creditLimit, text: RULE-PTY-022 message (SRS); PTY-422-PAYMENT-TERMS-RANGE → inline on paymentTermDays, text: RULE-PTY-008 message (SRS); PTY-422-WALK-IN-CREDIT → form banner, text: RULE-PTY-011 message (SRS); PTY-422-ROLE-INACTIVE → form banner, the form is kept, text: RULE-PTY-005 message (SRS); PTY-422-CREDIT-NOT-ENABLED → form banner, text: RULE-PTY-023 message (SRS), refetch app-config; PTY-500-AUDIT-NOT-RECORDED → form banner, form kept for retry, text: RULE-PTY-030 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, customer-credits] · [pty, customers, customerId]"
  - hook: CREDIT-CLEAR
    kind: mutation
    api: [API-PTY-018]
    errors: "STD; PTY-422-CREDIT-NOT-ENABLED → banner, text: RULE-PTY-023 message (SRS); PTY-500-AUDIT-NOT-RECORDED → banner, text: RULE-PTY-030 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, customer-credits] · [pty, customers, customerId]"
  - hook: CREDIT-FACADE
    kind: facade
```

#### SCR-PTY-003 · CREDIT-SEARCH-QUERY — API-PTY-030     traces=API-PTY-030,REQ-PTY-069,REQ-PTY-077,AC-PTY-077,AC-PTY-087
Kind read query; key = the whole `CreditSearchFilter`. `carriesCredit` is a tri-state chooser (yes / no / any); the route `?carriesCredit=true` seeds it (REQ-PTY-077).

#### SCR-PTY-003 · CREDIT-STANDING-QUERY — API-PTY-016     traces=API-PTY-016,REQ-PTY-068,REQ-PTY-080,REQ-PTY-085,AC-PTY-096
Kind read query. `exposureAvailable: false` → open exposure and available credit read «غير متاحة / Unavailable»; an empty `creditLimit` reads «لا ائتمان / No credit» (REQ-PTY-068).

#### SCR-PTY-003 · CREDIT-HISTORY-QUERY — API-PTY-031     traces=API-PTY-031,REQ-PTY-108,AC-PTY-122
Kind read query, newest first as the server returns it; page size 10 in the inline pane, the pane's pager changes `page` inside the key.

#### SCR-PTY-003 · CREDIT-SAVE — API-PTY-017     traces=API-PTY-017,REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-106,REQ-PTY-109,AC-PTY-056,AC-PTY-123
Kind mutation, the screen's one Save; one call. On success the history key is refreshed (the audit row is new, REQ-PTY-106).

#### SCR-PTY-003 · CREDIT-CLEAR — API-PTY-018     traces=API-PTY-018,REQ-PTY-078,AC-PTY-088
Kind mutation (command, with confirmation «سيُمسح حد الائتمان ومدة السداد لهذا العميل. متابعة؟» / "The credit limit and payment terms of this customer will be cleared. Continue?").

#### SCR-PTY-003 · CREDIT-SCREEN-INIT and CREDIT-FACADE
INIT: `b2bCredit` (the route is not mounted while off); PERM_PTY_CUSTOMER_CREDIT_VIEW / _UPDATE, PERM_PTY_CREDIT_OVERRIDE_VIEW and PERM_PTY_CREDIT_SETTINGS_VIEW (links); lookup CUSTOMER_GROUP; the standing by id when the route carries `:customerId`. FACADE: filter, selection, history page, `isDirty`; `save()`, `clearCredit()` (rejected while dirty).
<!-- SUB:F2-SCR-PTY-003:END -->

# Add G23 — the module registry's events row contradicts D-23 and F11

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T11:33:08+00:00

The P0 module registry DEPENDENCIES still lists `DomainEventPublisher` and `OutboxSubscriber` for offline POS and SLS/PUR documents. SRS D-23 and the plan chose call-in only, which matches F11. The upstream registry row should be corrected. Severity is MINOR because D-23 records the override.

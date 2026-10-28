# New finding G4 — checklist C3 is not met for the advisory-lock statements (MAJOR)

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T13:36:27+00:00

The plan names four native queries with `@TenantUnscoped` (QR-CAT-006, 008, 009, 037). Three operations also issue native `pg_advisory_xact_lock` statements: the Category tree lock (API-CAT-021, 023, 024, 053), the scale-prefix lock (API-CAT-014, 015, 032, 049) and the lock inside the import.
- None of them is in the QR catalog.
- None carries `@TenantUnscoped`.
- The key as written, `(TENANT_ID, 'CAT_CATEGORY_TREE')`, matches no PostgreSQL signature. The only forms are `(bigint)` and `(int, int)`.

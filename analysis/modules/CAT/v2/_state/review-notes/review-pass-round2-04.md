# G3 stays MAJOR. Hibernate does not batch inserts with IDENTITY keys, so "JDBC batching" either does nothing or means `JdbcTemplate`. `JdbcTemplate` would need checklist C3 (`TENANT_ID` named, `@TenantUnscoped`), and nothing declares it. The 60 s budget assumes batching.

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T14:14:22+00:00



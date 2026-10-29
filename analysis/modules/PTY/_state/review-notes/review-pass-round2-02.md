# Add G12 — checklist F8, `com.erp.*` package in the application

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: PTY        Version: v1
Written: 2026-10-29T08:52:40+00:00

`com.erp.pty` uses the core's namespace; the library's own module example uses `com.acme.rental.leasing`. Under §3.6 a checklist violation is MAJOR. CAT shows the same pattern, so the fix is one platform-wide root-namespace rule, recorded by an ADR and a platform finding.

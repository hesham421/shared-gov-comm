# G3 — no tenant export contributor is planned

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T14:49:28+00:00

Confirmed. `tenant.md` line 34 and `conventions.md` line 36 state the convention: an application with its own tenant tables implements `TenantExportContributor`, and `core-manifest.yaml` line 121 lists it as an extension point. INV adds 14 tenant tables but plans no such contributor, so the platform's tenant export would leave out all INV data. MAJOR, because it breaks a checklist line.

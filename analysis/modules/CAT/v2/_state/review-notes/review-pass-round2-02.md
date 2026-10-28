# Narrow G5 to the Unit e-invoice code seeds

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T10:14:15+00:00

Checklist lines C5 and C9 tell modules to seed MDL lookups and number series at TENANT_ID = 1, so those seeds are correct. The defect left is the CAT-owned CAT_UNIT_EINVOICE_CODE rows, which exist only for tenant 1 while every read is tenant-filtered. It stays MAJOR with an ADR.

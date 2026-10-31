# ADR-INV-028 — Inventory settings and context endpoints
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-065, REQ-INV-066, REQ-INV-118, REQ-INV-119, REQ-INV-010, REQ-INV-029, REQ-INV-132, REQ-INV-134, REQ-INV-135, SRS D-04, API-INV-008, API-INV-048, API-INV-049

## Context
The negative-stock setting and the near-expiry horizon are given cu settings (SRS D-04), not INV columns;
REQ-INV-119 bounds the horizon to whole days 1–365 with a message, but no RULE carries the range. Every
screen must know the active warehouse and branch counts and the capability flags to hide or show controls.

## Decision
- GET / PUT /api/v1/inv/settings read and write INV_ALLOW_NEGATIVE_STOCK and INV_NEAR_EXPIRY_DAYS through
  SettingsApi (tenant override over the platform defaults seeded in db-script BLOCK 8e).
- An out-of-range horizon answers 422 INV-422-HORIZON-RANGE with the REQ-INV-119 / AC-INV-133 text; the
  catalog row names rule REQ-INV-119.
- GET /api/v1/inv/context answers activeWarehouseCount, activeBranchCount, lotExpiryFl, dualQuantityFl,
  bundlesFl; it requires any INV page VIEW permission.

## Consequences
- The settings screen has no INV entity; its permission-matrix row names ENT-INV-004, whose moves the
  negative-stock setting governs.

## Status
ACCEPTED — non-breaking.

# ADR-INV-029 — Item stock settings are saved by an idempotent PUT keyed by Item
Status      : ACCEPTED
Stage       : P3.1 (Backend Execution Plan)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : ENT-INV-014, REQ-INV-124, REQ-INV-125, REQ-INV-129, RULE-INV-045, DBF-INV-210, DBF-INV-211, DBF-INV-212, API-INV-045

## Context
SRS B5 gives one save operation, PUT …/item-stock-settings/by-item/{itemId}, for both creating the record of
an Item and changing it; B4 grants CREATE and UPDATE separately. key, valueAr and valueEn are NOT NULL and
system-filled; the SRS gives only examples of the summary text («تشغيلة وصلاحية» / "Lot and expiry").

## Decision
- The PUT creates the record when none exists (requires PERM_INV_ITEM_STOCK_SETTINGS_CREATE) and updates it
  otherwise (requires PERM_INV_ITEM_STOCK_SETTINGS_UPDATE and the version).
- key = the Item's code (read through XM-INV-015). valueAr / valueEn = a summary built from the flags:
  lot-tracked → «تشغيلة وصلاحية» / "Lot and expiry"; dual-quantity → «كمية مزدوجة» / "Dual quantity"; both →
  the two joined with « · » / " · "; neither → «بدون تتبع» / "No tracking". isActiveFl = true.

## Consequences
- A concurrent first save is caught by UQ_INV_ITEM_STOCK_SETTINGS_ITEM_FK and retried as an update.
- The summary texts are display data, not messages; they can be changed without a rule change.

## Status
ACCEPTED — non-breaking.

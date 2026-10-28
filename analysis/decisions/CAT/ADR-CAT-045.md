# ADR-CAT-045 — Endpoint surface beyond SRS B5 and the in-process contract interface

Status : ACCEPTED (non-breaking) · Stage : P3.1 · Module : CAT v2 · Date : 2026-10-28

## Context
The contract (P1.5) promises eleven read operations (CON-CAT-007 … CON-CAT-017) and one event (CON-CAT-018) to other modules, as in-process promises (`conventions.module_interface: in_process`). SRS B5 gives HTTP endpoints for most of them, but four have none: read a Variant (CON-CAT-008, REQ-CAT-119), read an Item unit (CON-CAT-009), the storefront catalogue read (CON-CAT-014, REQ-CAT-115) and the catalogue search (CON-CAT-016, REQ-CAT-125). SCR-REQ-CAT-003 also names the operation "read" for Variant attributes, with no B5 endpoint. REQ-CAT-127 asks that a caller without VIEW on CAT_ITEMS gets no catalogue-search result, which the contract states as "forbidden — answered as an empty result".

## Decision
1. CAT publishes one in-process interface, `com.erp.cat.crossmodule.CatalogueReadApi`, with one method per contract operation (getItem, getVariant, getItemUnit, lookupBarcode, getCategory, getCategoryTree, listCategoryItems, listStorefrontItems, getVariantImages, searchCatalogue, checkCapabilityTurnOff). Each method delegates to the same service method as the HTTP endpoint of the operation, so both surfaces return the same data.
2. The plan adds five HTTP endpoints so that every contract operation and every screen operation has one: `GET /api/v1/cat/variants/{id}`, `GET /api/v1/cat/item-units/{id}`, `GET /api/v1/cat/variant-attributes/{id}`, `GET /api/v1/cat/storefront-items`, `GET /api/v1/cat/catalogue-search`. All are guarded by the VIEW permission of their screen.
3. The storefront HTTP endpoint is a back-office preview guarded by `PERM_CAT_ITEMS_VIEW`. Shoppers never call CAT; the storefront module reaches the same data through `CatalogueReadApi.listStorefrontItems`.
4. The catalogue search checks `PERM_CAT_ITEMS_VIEW` before searching; a caller without it receives success with an empty list (REQ-CAT-127), not 403.
5. The catalogue-change event (CON-CAT-018) is recorded by every mutating endpoint through the given `OutboxPublisher`.

## Consequences
- The frontend binds to the added endpoints through the API document; consumers bind to `CatalogueReadApi`.
- Adding a contract operation later adds one interface method and, when a screen needs it, one endpoint.

## Traces
CON-CAT-007 … CON-CAT-018 · REQ-CAT-115 · REQ-CAT-119 · REQ-CAT-125 · REQ-CAT-127 · REQ-CAT-048

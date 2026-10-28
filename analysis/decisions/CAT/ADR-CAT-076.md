# ADR-CAT-076 — Image type, size and public storage are enforced by a FILE category; the import's byte read waits on the core

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 / P3.2 / P4 (gate-analysis round 8, G1) · Module : CAT v2 · Date : 2026-10-28
Amends : ADR-CAT-056 §2

## Context
ADR-CAT-056 §2 said CAT reads a file's type, size and availability through `FileDocumentLookupApi`, and for the import also the bytes. erp-core 1.6.0 publishes only `isAvailable`, `publicUrl` and `publicUrls` on that interface (conventions §Files rule 3; `modules/file.md`), and no in-process read of an uploaded document's bytes. So:
- RULE-CAT-038 (JPG / PNG / WEBP, ≤ 5 MB, public image storage) and RULE-CAT-044 (xlsx, ≤ 10 MB) relied on calls that do not exist;
- CAT's own image type and size check is the "image validator" that `modules.file` lists under "Do not rebuild";
- `FILE_CATEGORY` already carries per-tenant limits and `allowPublic`, so the FILE upload endpoint can enforce type, size and visibility for a category CAT registers.

Options:
- (a) enforce the image checks through a FILE category and raise a CORE-GAP for the metadata and byte read;
- (b) keep the checks in CAT through an unpublished call, or read the bytes by a download token over HTTP — both rebuild what the core owns (checklist C7, F9).

## Decision
Option (a):
1. **Image category.** CAT registers the FILE category `CAT_ITEM_IMAGE` with `allowPublic = true`, allowed types PNG, JPEG and WEBP, and a limit of 5 MB, for the PLATFORM tenant and for every new tenant (bootstrap; copied by `CatTenantProvisioningContributor`, ADR-CAT-058). The client uploads every item image to this category through the given FILE upload endpoint. A file of another type, above 5 MB, or not public is refused there, in the FILE module's own envelope, before any CAT call.
2. **What CAT decides on API-CAT-018.** CAT checks only:
   - availability through `FileDocumentLookupApi.isAvailable` and a public URL through `FileDocumentLookupApi.publicUrl`: a reference that is not available in the caller's tenant, or has no public URL (it was stored privately), answers **CAT-404-NOT-FOUND**;
   - its own count: at most 20 active media records per Item, under **RULE-CAT-038**, which keeps its code and its message.
   RULE-CAT-038's statement now holds the count only; the type, size and public storage are the FILE category's part.
3. **Import.** RULE-CAT-044 and the import (REQ-CAT-037 … REQ-CAT-041, REQ-CAT-139) stand as written, but the byte read and the type / size check of API-CAT-049 are a delivery dependency on `CORE-GAP-file-document-metadata-read.md`. CAT opens no storage client and issues itself no download token meanwhile. The import is wired when the core publishes the read; its endpoint, body, error codes and tests do not change.
4. **Given usage.** The `FileDocumentLookupApi` rows of SRS A9, contract §4 and the backend plan's PHASE CORE list only the published methods (`isAvailable`, `publicUrl`, `publicUrls`).
5. **Frontend.** The client no longer checks the image type and size itself; a refusal of the FILE upload is shown on the image's file input in the FILE envelope's language (ADR-CAT-056 already routes it). The client keeps the 20-image count check (RULE-CAT-038).

## Consequences
- SRS: REQ-CAT-110, AC-CAT-112, RULE-CAT-038, A9.
- Contract §4 (given-usage, verbatim from A9).
- Backend plan: PHASE CORE (given usage, core gaps), RULE-CAT-038, API-CAT-018, API-CAT-049, error catalog, bootstrap.
- api-spec: API-CAT-018 and API-CAT-049 descriptions.
- Frontend plan: F2 ITEM-SAVE (CAT-422-IMAGE-NOT-ACCEPTED now the count), F3 image line validator.
- Tests: TC-CAT-056, 146, 166, 218 reworded; the import TCs are marked core-dependent.
- Business policies: PLATFORM FINDINGS row 11.

## Traces
REQ-CAT-105 · REQ-CAT-110 · REQ-CAT-037 · REQ-CAT-040 · REQ-CAT-139 · AC-CAT-112 · RULE-CAT-038 · RULE-CAT-044 · API-CAT-018 · API-CAT-049 · TC-CAT-056 · TC-CAT-146 · TC-CAT-166 · TC-CAT-218 · ADR-CAT-056 · ADR-CAT-058 · CORE-GAP-file-document-metadata-read

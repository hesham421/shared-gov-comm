# CORE-GAP-file-document-metadata-read — Can a module read a FILE document's type, size, visibility and bytes?

Status : OPEN (asked of erp-core 1.6.0) · Raised by : CAT v2 (gate-analysis round 8, G1) · Date : 2026-10-28
Decision that depends on it : ADR-CAT-076

## The gap
erp-core 1.6.0 publishes three methods on `com.erp.file.crossmodule.FileDocumentLookupApi`: `isAvailable`, `publicUrl` and `publicUrls` (conventions §Files rule 3; `modules/file.md`). No published interface returns a document's content type, size or visibility (public or private), and no in-process call reads back the bytes of a document a user uploaded through the FILE endpoints. `FilePrivateStoreApi` stores server-generated files and issues single-use download tokens for a caller; it offers no in-process read.

CAT needs two of these:
1. **Image checks.** RULE-CAT-038 required a JPG / PNG / WEBP file of at most 5 MB held in the public image storage. This part moves to the FILE category CAT registers for item images (ADR-CAT-076), so it no longer depends on this gap. It is recorded here because a module that keeps a reference to an image it did not upload itself cannot check the reference without such a read.
2. **Item import.** API-CAT-049 must read the bytes of the xlsx file the client uploaded to the private storage, and its type and size (RULE-CAT-044), to validate and create the rows (REQ-CAT-037 … REQ-CAT-041, REQ-CAT-139). Without a published read, CAT cannot implement the import without rebuilding a storage access, which checklist C7 and `modules.file` "Do not rebuild" forbid.

## What CAT does meanwhile (ADR-CAT-076)
- Item images: the FILE category `CAT_ITEM_IMAGE` (allowPublic = true, PNG / JPEG / WEBP, limit 5 MB) refuses a wrong file at upload. API-CAT-018 checks only `isAvailable` and `publicUrl` (a reference that is not available, or has no public URL, answers CAT-404-NOT-FOUND) and the 20-record count (RULE-CAT-038).
- Item import: the plan, the API document and the tests of API-CAT-049 stand as written. The byte read and the type / size check of RULE-CAT-044 are a delivery dependency on this gap: the import is wired when the core publishes the read. CAT opens no storage client, issues itself no download token and makes no HTTP call to the FILE endpoints in the meantime.

## Questions to the core
1. Can `FileDocumentLookupApi` (or `FilePrivateStoreApi`) return a document's metadata — content type, size in bytes, visibility (public / private) and category code — for a document of the caller's tenant?
2. Can it give a bounded in-process read of a private document's bytes (for example `openPrivateContent(documentId, maxBytes)` returning a stream), within the caller's tenant and refused for another tenant's document?
3. What is the supported way for a business module to register its FILE categories (code, limits, allowed types, `allowPublic`) for the PLATFORM tenant and for every newly provisioned tenant?

## Impact when answered
- API-CAT-049 is wired on the published read; its endpoint, request body, error codes and tests do not change.
- The category registration of ADR-CAT-076 moves to the published mechanism, with no change to API-CAT-018.

## Traces
REQ-CAT-037 · REQ-CAT-038 · REQ-CAT-039 · REQ-CAT-040 · REQ-CAT-041 · REQ-CAT-105 · REQ-CAT-110 · REQ-CAT-139 · RULE-CAT-038 · RULE-CAT-044 · API-CAT-018 · API-CAT-049 · ADR-CAT-056 · ADR-CAT-076

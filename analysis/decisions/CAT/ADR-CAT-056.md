# ADR-CAT-056 — Uploaded files go through the given FILE endpoints; CAT endpoints take a file reference

Status : ACCEPTED (non-breaking) · Stage : P3.1 (gate-analysis revise, G2, G17) · Module : CAT v2 · Date : 2026-10-28
Amends : ADR-CAT-048 §4 (import shapes)

## Context
`API-CAT-018` (upload image) and `API-CAT-049` (import items) were declared `multipart/form-data` endpoints of CAT. The given library forbids that:
- checklist F4: "a multipart endpoint of the module" is a rejection;
- checklist C7: "Every byte goes through `FilePrivateStoreApi` / `FileImageStoreApi` / the FILE endpoints; the module keeps `FILE_DOCUMENT.ID` columns";
- `modules.file`, "Do not rebuild": "a multipart endpoint of your own".

The multipart image upload also stored the file before the media row was written, so a later refusal or a rollback left an orphan public file, and removing an image never released its file (G17).

## Decision
1. **Two steps for every uploaded file.**
   - Step 1, the client: it uploads the bytes through the given FILE upload endpoint (`erp-core:modules.file`) and receives `FILE_DOCUMENT.ID`. An image goes to the public image store (a FILE category that allows public files); an import file goes to the private store.
   - Step 2, CAT: `API-CAT-018` and `API-CAT-049` take `application/json` bodies that carry `fileDocumentRef` (int64, the `FILE_DOCUMENT.ID`), and no bytes.
2. **CAT reads the file only through `FileDocumentLookupApi`.** It reads the file's type, size and availability, and for the import also the bytes.
   - A `fileDocumentRef` that names no available file of the caller's tenant → `CAT-404-NOT-FOUND`.
   - Wrong type or size → the RULE's own code: `CAT-422-IMAGE-NOT-ACCEPTED` (RULE-CAT-038) or `CAT-422-IMPORT-FILE-REJECTED` (RULE-CAT-044).
3. **No upload-time platform codes in CAT.** CAT has no multipart surface, so `CAT-413-PAYLOAD-TOO-LARGE` and `CAT-415-UNSUPPORTED-MEDIA-TYPE` leave the CAT error catalog and the API document. An oversized or wrong-type upload is answered by the given FILE endpoint in its own envelope, at step 1.
4. **File lifecycle and retention (G17).**
   - CAT creates a media row only after every check of `API-CAT-018` has passed, in the same transaction. A refused or rolled-back request therefore writes no CAT row, and CAT never stores bytes.
   - A file the client uploaded but never attached stays with the given FILE module, under that module's retention of unattached files.
   - Removing an image (`API-CAT-020`) deactivates the media row and keeps `FILE_DOCUMENT_REF`. The file stays available, so earlier documents and the storefront cache can still resolve it (RULE-CAT-048 keeps deactivated records readable). CAT never deletes a FILE document.
   - Purging the files of media inactive for longer than the tenant's retention period is the given FILE module's job, not CAT's. CAT takes no part in that purge.
   - An import file is read once by `API-CAT-049` and is not kept as a CAT reference.
5. `DBF-CAT-122` (`FILE_DOCUMENT_REF`) is unchanged and remains a given soft read, with no constraint and no XM.

## Consequences
- api-spec: `ItemMediaUpload` and `ItemImportUpload` become JSON request schemas carrying `fileDocumentRef`. The 413 and 415 responses are removed, and 404 is added to `API-CAT-049`.
- Frontend: ITEM-SAVE step 8 and IMPORT-SUBMIT first call the given FILE upload and then the CAT endpoint. A rejection from the FILE upload is shown on the file input in the envelope's language.
- Tests: TC-CAT-018, 055, 056, 113, 146, 165 and 166 upload through the FILE endpoint first and send `fileDocumentRef`.
- The error catalog shrinks by 2 platform rows (ADR-CAT-044).

## Traces
REQ-CAT-105 · REQ-CAT-107 · REQ-CAT-108 · REQ-CAT-110 · REQ-CAT-037 · REQ-CAT-040 · API-CAT-018 · API-CAT-049 · API-CAT-020 · DBF-CAT-122

## Amended
§2 is amended by ADR-CAT-076 (gate-analysis round 8): erp-core 1.6.0 publishes only `isAvailable`, `publicUrl` and `publicUrls` on `FileDocumentLookupApi`. Image type, size and public storage are enforced by the FILE category `CAT_ITEM_IMAGE` at upload; API-CAT-018 checks availability and a public URL (else CAT-404-NOT-FOUND) and the 20-record count (RULE-CAT-038). The import's byte read and type / size check wait on CORE-GAP-file-document-metadata-read.

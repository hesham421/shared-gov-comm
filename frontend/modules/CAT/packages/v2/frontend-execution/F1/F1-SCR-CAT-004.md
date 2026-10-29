<!-- source: PHASE:F1 / SUB:F1-SCR-CAT-004 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-048, API-CAT-049, REQ-CAT-036, REQ-CAT-037, REQ-CAT-038, REQ-CAT-039, REQ-CAT-040, REQ-CAT-041, REQ-CAT-139, SCR-CAT-004 -->
<!-- SUB:F1-SCR-CAT-004:START traces=REQ-CAT-139,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,API-CAT-048,API-CAT-049,SCR-CAT-004 -->
### F1 · SCR-CAT-004 — Item import

Bound schemas: the xlsx binary of API-CAT-048 (the response is a `Blob`) · `ItemImportRequest` (JSON carrying `fileDocumentRef`, API-CAT-049) → `ImportResult` with `ImportRowRejection[]`. The file is uploaded first through the given FILE client (private storage — ADR-CAT-056).

UI types:
- `ImportForm = { file: File | null }`.
- `ImportView = { phase: 'idle' | 'importing' | 'done' | 'rejected', result?: ImportResult, error?: LocalizedError }`.
<!-- SUB:F1-SCR-CAT-004:END -->

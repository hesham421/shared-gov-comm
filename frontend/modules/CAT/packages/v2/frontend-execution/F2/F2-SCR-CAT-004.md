<!-- source: PHASE:F2 / SUB:F2-SCR-CAT-004 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-048, API-CAT-049, REQ-CAT-036, REQ-CAT-037, REQ-CAT-038, REQ-CAT-039, REQ-CAT-040, REQ-CAT-041, REQ-CAT-139, SCR-CAT-004 -->
<!-- SUB:F2-SCR-CAT-004:START traces=REQ-CAT-139,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,API-CAT-048,API-CAT-049,SCR-CAT-004 -->
### F2 · SCR-CAT-004 — Item import

```yaml name=screen-hooks
screen: SCR-CAT-004
hooks:
  - {hook: IMPORT-SHEET-DOWNLOAD, kind: read, api: [API-CAT-048], cache_key: "none — a file download, not cached", errors: "CAT-403 → unauthorized · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: IMPORT-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized", loading: NONE}
  - {hook: GIVEN-FILE-UPLOAD, kind: mutation, cache_key: "—", errors: "rejection of the given FILE upload → inline on the file input, the FILE envelope's messageAr / messageEn", loading: LOCAL, invalidation: "—"}
  - {hook: IMPORT-SUBMIT, kind: mutation, api: [API-CAT-049], errors: "see IMPORT-SUBMIT below", loading: LOCAL, invalidation: "[cat, items, search] · [cat, catalogue-search] · [cat, categories, items]"}
  - {hook: IMPORT-FACADE, kind: facade}
```

#### IMPORT-SHEET-DOWNLOAD — API-CAT-048            traces=API-CAT-048,REQ-CAT-036
Read on click. The binary body is saved as a file named after the import sheet.

#### IMPORT-SUBMIT — API-CAT-049            traces=API-CAT-049,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-139
Mutation, the screen's one submit. The chosen file is first uploaded through the given FILE client (GIVEN-FILE-UPLOAD, private storage — ADR-CAT-056), then `API-CAT-049` is sent with `{fileDocumentRef}`. The response `ImportResult` fills the result view. Errors and notices:
- `CAT-422-IMPORT-FILE-REJECTED` → inline on the file input; text: RULE-CAT-044 message
- a rejection of the given FILE upload → inline on the file input, showing the FILE envelope's messageAr / messageEn
- `CAT-404-NOT-FOUND` (the uploaded file is no longer available) → inline on the file input; generic text, and the file is uploaded again on the next Import
- the success code `CAT-200-IMPORT-ROW-REJECTED` → result list; each rejected row shows its `messageAr` / `messageEn` (RULE-CAT-045 message filled per row)
- `CAT-500-CODE-NOT-ISSUED` → banner with Try again; text: RULE-CAT-005 message (the whole import was rolled back — ADR-CAT-048 §3)
- `CAT-500-CHANGE-NOT-SAVED` → banner; text: RULE-CAT-049 message (also the answer when the import passed its 120-second limit and was rolled back whole — REQ-CAT-139, ADR-CAT-071; no Item was created, and the owner imports the file again, split into smaller files)

#### IMPORT-SCREEN-INIT — SCR-CAT-004
Permission read PERM_CAT_ITEM_IMPORT_VIEW / CREATE; no lookups; no entity. When mounted by the setup wizard, the wizard's "next" stays the wizard's control; this screen exposes `onImported(result)`.

#### IMPORT-FACADE — SCR-CAT-004
It owns: the chosen file, the import phase, the last result. Operations: `downloadSheet()`, `import()`, `reset()`.
<!-- SUB:F2-SCR-CAT-004:END -->

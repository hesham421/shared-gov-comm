<!-- source: PHASE:F4 / SUB:F4-SCR-CAT-005 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-007, API-CAT-042, API-CAT-050, API-CAT-056, REQ-CAT-101, REQ-CAT-102, REQ-CAT-103, REQ-CAT-104, REQ-CAT-148, SCR-CAT-005 -->
<!-- SUB:F4-SCR-CAT-005:START traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-148,API-CAT-007,API-CAT-042,API-CAT-050,API-CAT-056,SCR-CAT-005 -->
### F4 · SCR-CAT-005 — Barcode labels

#### BARCODE-LABELS-SCREEN — SCR-CAT-005            traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-104,API-CAT-042,API-CAT-007,API-CAT-050
- **Guard**: `/cat/barcode-labels` needs PERM_CAT_BARCODE_LABELS_VIEW; "Print" needs PERM_CAT_BARCODE_LABELS_CREATE.
- **Facade**: LABEL-FACADE only.
- **Cross-module**: none.
- **Components**: `BarcodeLabelsPage` (lines table with copies inputs and total, "Generate internal barcode" on a flagged line, "Add variants", "Print") and `VariantPickerDrawer` (`?picker=variants&text=`), the second level.
- **Composition**: container page. The label lines are inline in a pane of the page body. `VariantPickerDrawer` is a sibling of the page, opened from the search params. It returns the chosen Variants to the lines and saves nothing.
- **Saves**: ONE (`LABEL-PRINT`). It opens the PDF in a new tab. "Generate internal barcode" on a flagged line (`LABEL-INTERNAL-BARCODE`, API-CAT-056) is a record command on that line, not a save of the form (ADR-CAT-078).
- **Navigation out**: "Open item" on a flagged line → `/cat/items/:itemPk` (with PERM_CAT_ITEMS_VIEW).
<!-- SUB:F4-SCR-CAT-005:END -->

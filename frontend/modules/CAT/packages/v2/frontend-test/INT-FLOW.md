<!-- source: PHASE:TEST-PLAN-FE / SUB:INT-FLOW -->
<!-- context: TEST-PLAN-FE-HEADER.md — phase-level preamble -->
<!-- traces: AC-CAT-005, REQ-CAT-003, REQ-CAT-004, REQ-CAT-005, REQ-CAT-024, SCR-CAT-001 -->
<!-- SUB:INT-FLOW:START traces=AC-CAT-005,REQ-CAT-005,REQ-CAT-024,REQ-CAT-004,REQ-CAT-003 -->
### SUB INT-FLOW

<!-- TC:TC-CAT-176:START traces=AC-CAT-005,REQ-CAT-005,REQ-CAT-024,REQ-CAT-004,REQ-CAT-003,SCR-CAT-001 -->
### TC-CAT-176 — Item lifecycle: create → search → update → publish → deactivate → gone from active results
Derived from : AC-CAT-005  (REQ-CAT-005; the flow passes through REQ-CAT-024, REQ-CAT-003, REQ-CAT-004)
Exercises    : SCR-CAT-001 `/cat/items/new` → `/cat/items` → `/cat/items/:itemPk` (ITEM-SAVE → API-CAT-001, 003; ITEM-SEARCH-QUERY → API-CAT-038; ITEM-STATE-COMMAND → API-CAT-005, 004)
Rule / code  : RULE-CAT-041 (deactivation sets UNPUBLISHED) · RULE-CAT-048 → CAT-200-DEACTIVATED (notice)
Package      : F4-SCR-CAT-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: F0
Host data    : none
Steps        : 1. Create an Item with a valid nameAr only and Save. 2. Search `/cat/items` with its nameAr. 3. Open it, set a nameEn and Save. 4. Publish (confirm). 5. Deactivate (confirm). 6. Search again with the default filters (`isActiveFl=true`). 7. Search with `isActiveFl=false` and open the Item.
Expected     : 2 → the Item is listed. 4 → the badge shows "Published". 5 → a notice: ar «تم الإيقاف. يبقى السجل متاحًا للمستندات السابقة ولا يظهر في الاختيارات الجديدة.» · en "Deactivated. The record stays available to earlier documents and no longer appears in new selections."; the header shows inactive and "Not published". 6 → the Item is not listed. 7 → the Item is listed and opens: it is still readable.
Test data    : nameAr / nameEn placeholders `<valid Arabic name>`, `<English name>`
<!-- TC:TC-CAT-176:END -->
<!-- SUB:INT-FLOW:END -->

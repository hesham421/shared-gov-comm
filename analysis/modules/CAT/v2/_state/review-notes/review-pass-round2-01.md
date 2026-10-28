# Previous-round findings G1–G14 are confirmed

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T08:11:42+00:00

I checked each one against the artifacts. G1–G5 and G13 quote checklist lines C1, C3/F2, C7/F4, C10/F7, C13 and C16/C18/C20 word for word in `.claude/skills/erp-core-usage@1.6.0/templates/checklist.md`. For G8 I recomputed the check digits: label 20·01230·01234 has an EAN-13 check digit of 2, not 7, and 04·0000000042 has 8, not 0. The other literal barcodes in the ACs are valid (6223000000014, 16223000000011, 2000000000008, 6229999999995). AC-CAT-096's 2000123012347 also has a wrong check digit (it should be 6), but that AC still passes because it expects "not found" either way. G6, G7 and G9 hold on the stated rules (RULE-CAT-015 "of an active Item", RULE-CAT-025 counts active Items only, CATEGORY-OPTIONS-QUERY reads `isActiveFl: true`).

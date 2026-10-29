# G4 is widened into a pattern finding (probe P4)

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-29T05:37:06+00:00

The defaults problem has the same cause as a second one. CAT seeds closed, behaviour-driving keys into mdl, where a tenant can edit them: CAT_ITEM_KIND, CAT_PUBLISH_STATE, CAT_BARCODE_TYPE, CAT_SCALE_VALUE_TYPE and CAT_EINVOICE_ITEM_CODE_TYPE. The service also hard-codes some of their codes (PUBLISHED, BUNDLE, GTIN, INTERNAL, SCALE in QR-CAT-007/009/016/047/053). A tenant that deactivates PUBLISHED or GTIN breaks publish or GTIN entry through RULE-CAT-006. The fix belongs in RULE-CAT-006 and needs an ADR.

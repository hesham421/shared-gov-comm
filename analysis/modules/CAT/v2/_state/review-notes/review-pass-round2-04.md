# G3 stays MAJOR

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T13:17:56+00:00

I considered a client-side filter on `VariantAttributeValueView.isActiveFl`, which would fix the picker. It does not fix TC-CAT-090: that test asserts the server-side omission, and as written it fails against QR-CAT-026.

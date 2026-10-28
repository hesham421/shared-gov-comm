# G2 drops to MINOR. The given `PageableBuilder` already caps page size at 200 (reference/modules/common.md). RULE-CAT-014 (≤3 attributes, ≤300 Variants) and RULE-CAT-036 (≤5,000 labels) already bound generation and label requests. What remains is documentation: declare `PageableBuilder` (including for native QR-CAT-006), check the variant limit before building combinations, and add `maxItems` to the arrays.

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T14:14:22+00:00



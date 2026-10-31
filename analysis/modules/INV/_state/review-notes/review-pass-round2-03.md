# new finding G9, an ambiguous lot-less on-hand query

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T14:15:23+00:00

QR-INV-019 says in its intent "held without a lot (LOT_FK IS NULL)", but its logical spec writes `[AND LOT_FK = :l]`. With a null parameter that compares to nothing, so the on-hand without a lot reads as 0. RULE-INV-037's exception (ADR-INV-051) would then refuse every line without a lot. This is MINOR.

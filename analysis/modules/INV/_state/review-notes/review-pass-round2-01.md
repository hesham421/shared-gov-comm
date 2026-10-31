# G1 confirmed (MAJOR). API-INV-032 creates count-difference lines that carry only `lotFk`. RULE-INV-035 ("on post of an increase line") and RULE-INV-036 need `lotNo` and `lotExpiryDate`. Nothing says a count-generated line is exempt, and API-INV-032 does not list INV-422-LOT-EXPIRY-REQUIRED.

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T13:49:07+00:00



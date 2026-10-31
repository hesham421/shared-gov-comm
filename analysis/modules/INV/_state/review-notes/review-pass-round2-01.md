# G1 — a negative stock balance held without a lot can never be corrected

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T14:49:28+00:00

Confirmed. REQ-INV-111 and SRS D-17 let a sale shortfall post as an issue without a lot, which leaves that balance negative (TC-INV-128 shows −3). REQ-INV-111 says "the next count corrects it". But RULE-INV-037 and ADR-INV-051 accept a count line without a lot only up to the stock held without a lot. With that stock at −3, even a counted 0 is refused. RULE-INV-035 also refuses any increase without a lot. So the Owner has no next step. MAJOR, and the fix needs an ADR.

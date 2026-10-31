# Missing platform findings belong in G4 and G12

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T11:33:08+00:00

INV recorded no platform finding. It should have recorded two, both owned by CAT: the unit-of-measure lookup key (G4) and a read by exact Variant code (G12). Both fixes now require the row. Per §7, this weighs on the `complete` score.

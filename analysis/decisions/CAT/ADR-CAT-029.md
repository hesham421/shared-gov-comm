# ADR-CAT-029 — Item media binaries live in the given `file`; CAT owns media ordering, primary image, alt text (ar/en) and variant association
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: 18241e1ca485
traces      : —

## Decision
traces: OI-15, CAND-CAT-006
Accept. The binaries use `FileImageStoreApi`, so G6 holds. Media is PRIVATE to CAT, and SFR reads it through a CAT crossmodule query, not a foreign key.
Sources: KB §5 #1; manifest `file`.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md

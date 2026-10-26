# ADR-CAT-015 — Registry 1.0.0 is the baseline and the POL-CAT stream starts at 001
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: 9d9458f61c69
traces      : POL-CAT-001, POL-CAT-010, POL-CAT-011

## Decision
traces: OI-03, POL-CAT-001, POL-CAT-010
Accept. The generated current state holds no earlier registry or policy file, and this brief forbids opening version folders. If an earlier POL-CAT stream exists, `gov.py analyze` (C3.9) will report it. The policies would then be re-issued after its last number before anything is published. The next free number is POL-CAT-011.
Sources: registry §1; brief inputs.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md

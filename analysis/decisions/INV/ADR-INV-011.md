# ADR-INV-011 — Story priorities follow the launch priority and delivery wave, and are marked as derived
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0.5        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:39:31+00:00
Dialogue-key: 74fa9849799a
traces      : POL-INV-001, US-INV-001, US-INV-026

## Decision
Reasoning : I accept option B and amend it in two ways.
- The engine allows a Priority only when it is "stated or clearly implied". The sources state the module's delivery wave (KB §8, wave 1) and the launch order of activities (KB §4.2). They do not state a priority per story.
- So the PRD records the priorities as derived from those two sources and not as client-stated. The user can adjust any of them at the gate, and none is a rule for P1.
- Option A discards an order the sources do give. Option C makes the field meaningless.
- US-INV-026 takes MEDIUM. It covers two flags, `lotExpiry` (MEDIUM) and `dualQuantity` (LOW), and a story takes the higher of the levels it serves.
- I checked each level against the sources. Flag-independent stories and `bundles` are HIGH, because bundles are ✓ for electronics and household, which are in the launch priority. `lotExpiry` stories are MEDIUM, because grocery comes "next". `dualQuantity` is LOW, because its activities are not in the launch list.
Sources   : KB:raw-idea.md §3 D2, D8; §4.2 (launch priority); §8
traces    : US-INV-001…028, POL-INV-001…022

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0.5-round2.response2.md

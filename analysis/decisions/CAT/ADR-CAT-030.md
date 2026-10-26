# ADR-CAT-030 — The CAT module registry indexes every POL-CAT id, and business-policies-cat stays the only home of policy text
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T14:51:49+00:00
Dialogue-key: 88c01ae49670
traces      : POL-CAT-001, POL-CAT-002, POL-CAT-003, POL-CAT-004, POL-CAT-005, POL-CAT-006, POL-CAT-007, POL-CAT-008, POL-CAT-009

## Decision
Reasoning : Option A is accepted as drafted.
- Contract C3.7 (`registry-agree`, kind POL) needs each POL id present in the registry artifact.
- Engine §4 makes `module-registry-{mod}.md` this stage's registry output, and §3.4 makes business-policies the source of truth for policy statements. Option A satisfies both, and option B would break the three-file output and C3.3.
- Amendment: the index's "Next free policy id: 010" line stays, so a later version adds its rows in the same version that defines them.
traces : POL-CAT-001, POL-CAT-002, POL-CAT-003, POL-CAT-004, POL-CAT-005, POL-CAT-006, POL-CAT-007, POL-CAT-008, POL-CAT-009

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md

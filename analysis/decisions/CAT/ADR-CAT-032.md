# ADR-CAT-032 — Catalogue-content stories with no dedicated policy trace to POL-CAT-001
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0.5        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T14:56:26+00:00
Dialogue-key: 2bcd62fa4231
traces      : POL-CAT-001, POL-CAT-002, POL-CAT-004, POL-CAT-005, POL-CAT-008, US-CAT-002, US-CAT-009, US-CAT-011, US-CAT-012, US-CAT-013, US-CAT-014, US-CAT-015, US-CAT-016, US-CAT-017, US-CAT-018, US-CAT-019, US-CAT-021

## Decision
Accepted with one amendment. The TRACES RULE needs at least one POL, and engine §3 makes "the [role] needs to manage [entity]" a legitimate seed. Option B would leave six of the nine registry entities without a story, so P1 would have nothing to derive REQs from. Each story names the channel it serves, so the trace to the one-catalogue policy is honest.
The amendment concerns sourcing. US-CAT-009, -015 and -018 rest on module-registry AUTO-decisions, which the user has not confirmed. Their Source lines now say so, and the PRD carries a note for the approver. If the approver rejects one, the story is struck at the gate, not edited.
Round 2 also removes two traces that did not hold. US-CAT-019 no longer traces POL-CAT-008, and US-CAT-017 no longer traces POL-CAT-002. US-CAT-012 now traces POL-CAT-004 in place of POL-CAT-005, so it matches US-CAT-011.
traces: POL-CAT-001, US-CAT-002, US-CAT-013, US-CAT-014, US-CAT-015, US-CAT-016, US-CAT-018, US-CAT-021

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0.5-round2.response2.md

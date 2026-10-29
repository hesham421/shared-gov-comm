# ADR-INV-012 — Opening stock is an INV story, stated as a need
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0.5        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:39:31+00:00
Dialogue-key: 4a8adc25af93
traces      : POL-INV-003, POL-INV-018, POL-INV-021, US-INV-012

## Decision
Reasoning : I accept option A.
- The inputs name the moment ("before opening stock is loaded", POL-INV-018 rationale).
- They name bulk loading (KB §6.9 step 5).
- Weighted-average cost (POL-INV-021, IAS 2 ¶25) needs a starting cost basis.
- Leaving it to PUR receipts (option B) would misstate the starting position. Deferring it (option C) would contradict the 30-minute setup metric.
- The story stays at need level. P1 chooses the mechanism (opening document, adjustment or import) and the fields.
- It traces POL-INV-003 (the default warehouse is where the stock lands), POL-INV-018 and POL-INV-021.
Sources   : business-policies POL-INV-018 rationale; KB:raw-idea.md §6.9, §6.10; INV-P09
traces    : US-INV-012, POL-INV-003, POL-INV-018, POL-INV-021

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0.5-round2.response2.md

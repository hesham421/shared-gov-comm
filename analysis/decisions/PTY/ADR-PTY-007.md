# ADR-PTY-007 — Credit overrun is a tenant setting in `cu`, offering warn or block-with-override, with the default block-with-override
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:49:42+00:00
Dialogue-key: ff0e47f2bb86
traces      : POL-PTY-003, POL-PTY-008

## Decision
Accepted (option D). "Nothing imposed" [KB §6.2] is met by the warn alternative. "Every error offers a next action" [KB §6.5] is met by the override. Unbounded credit is the larger financial risk, which justifies the stricter default. The override is a permission. Which role presets hold it (Owner, Seller/Cashier, Warehouse; KB §3 D8) is decided in P1. Recorded as POL-PTY-008, the next free id in the module's sequence.
traces: POL-PTY-008, POL-PTY-003

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0-round2.response2.md

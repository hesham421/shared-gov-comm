# ADR-PTY-011 — Story priority follows the KB §8 delivery waves, and "—" applies wherever no wave is named
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0.5        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:53:58+00:00
Dialogue-key: 8aa2fd03732c
traces      : US-PTY-001, US-PTY-006, US-PTY-009, US-PTY-010, US-PTY-017

## Decision
- Reasoning: I accept option B. KB §3 D10 makes the delivery order binding, so wave membership clearly implies priority. Wave 1 is Catalog, Party, Inventory, Purchasing, Sales & POS, Settlements and Tax. Wave 2 is Storefront and Shipping.
- Amendment: round 1 gave customer groups (US-PTY-006) HIGH, but that is not clearly implied.
- Its consumer is Pricing, which KB §8 does not place in any wave. Wave 1 lists neither Pricing nor price lists.
- Its source is DP §7.1 and platform decision 8, which carry no ordering.
- Under the engine rule "only if stated or clearly implied", it takes "—", the same as US-PTY-017 (an AUTO-only need).
- Final rule:
- A need served by Party's wave-1 consumers (back-office, POS) is HIGH.
- A storefront-only need is MEDIUM.
- A need sourced only from an AUTO decision, or whose consumer no wave names, is "—".
- The result is 13 HIGH, 2 MEDIUM (US-PTY-009, US-PTY-010) and 2 "—" (US-PTY-006, US-PTY-017).
- traces: US-PTY-001…US-PTY-017; KB §3 D10, §8; engine §2

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0.5-round2.response2.md

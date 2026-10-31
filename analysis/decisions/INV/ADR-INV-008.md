# ADR-INV-008 — Negative stock is a tenant setting, default allowed with a Warehouse alert; online issues are blocked when the tenant disallows it; synced offline POS sales are always accepted
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:34:05+00:00
Dialogue-key: e44396d221f4
traces      : —

## Decision
Accept option C, with the default amended from round 1. Round 1 defaulted to "disallowed".

- **Chosen default: negative stock allowed.** G7 (first invoice in 30 minutes or less), KB §6.5 (no dead ends) and
  "nothing imposed" (§6) all argue against blocking an owner who has not yet loaded opening stock. The setup
  wizard's import step covers items, not balances.
- **Alert:** while negative stock is allowed, every crossing below zero alerts the Warehouse role.
- **Owner opt-in:** an owner who wants strict balances switches blocking on (tenant setting).
- **Block scope:** blocking applies to online issues. Synced offline POS sales are always accepted, because the
  goods have already left (KB §5 #6).
- **Reference:** Odoo allows negative stock by default *(not live-verified)*.
- **Binds:** POL-INV-018, POL-INV-019 and POL-INV-020.

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0-round2.response2.md (decision "Negative stock is a tenant setting …"; filled in at gate-analysis revise 1, G21)

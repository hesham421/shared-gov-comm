# ADR-CAT-068 — Three repair operations stay allowed on an inactive Item

Status : ACCEPTED (non-breaking) · Stage : P1 / P3.1 / P3.2 / P4 (gate-analysis round 3, G1 – G3, G8) · Module : CAT v2 · Date : 2026-10-28 · Amends : ADR-CAT-063

## Context
ADR-CAT-063 added RULE-CAT-053: an inactive Item refuses every child write. ADR-CAT-059 made reactivation (REQ-CAT-130) re-establish the ACTIVE-state invariants of SRS A7. Together the two rules left three states that an inactive Item could never leave:
1. **No active Variant.** Reactivation is refused with RULE-CAT-050, whose message says "Generate a variant for it". Generation (API-CAT-006) was refused by RULE-CAT-053, and a Variant has no reactivation.
2. **Inactive Category.** Reactivation is refused with RULE-CAT-026, whose message says "Choose an active category". `PUT` with a new categoryFk (API-CAT-003) was refused by RULE-CAT-053, and a Category had no reactivation either (G10, ADR-CAT-069).
3. **Bundle with an invalid component.** Reactivation is refused with RULE-CAT-019. Replacing the components (API-CAT-017) was refused by RULE-CAT-053.

In all three, the refusal named a next action that the system then refused. That breaks the platform rule that no error is a dead end (KB §6.5).

## Decision
1. **Three repairs are exempt from RULE-CAT-053.** On an inactive Item, these stay allowed, each still under its own rules:
   - **Variant generation** (API-CAT-006) — RULE-CAT-008, 009, 012, 013, 014, 020, 052. With 0 active Variants, RULE-CAT-052 requires no attribute and RULE-CAT-012 assigns nothing; the new Variants are active.
   - **An update whose only change is categoryFk** (API-CAT-003) — RULE-CAT-026 and the version check. Every other field of the body must equal the stored value; any other change is refused with `CAT-422-PARENT-INACTIVE`.
   - **Replacing the Bundle components** (API-CAT-017) — RULE-CAT-008, 009, 018, 019 and the version check.
2. **The Item stays inactive.** None of the three reactivates the Item. The owner reactivates it afterwards (API-CAT-005), and the invariants are checked then, as before.
3. **Everything else is unchanged.** Item units, images, barcodes, the Variant e-invoice override and every other header field stay refused under an inactive Item. Child deactivations stay allowed.
4. **Flag-off case.** Generation still needs `variants` on and the Bundle repair still needs `bundles` on (RULE-CAT-008). While a flag is off, the other next action of the message applies: RULE-CAT-050 also offers "create a new item", and the flag can be turned on in settings. This is not a dead end: both actions are available to the owner.
5. **Invariant 4 (name taken).** It needs no exemption: the other active Item can be renamed or deactivated.
6. **Frontend (G2).** On an inactive Item's Entry, the Category field, "Generate variants" (while `variants` is on) and the Bundle components pane stay available; every other input stays read-only. Each reactivation refusal banner of ITEM-STATE-COMMAND links to the affordance that repairs it.
7. **Tests (G3, G8).** TC-CAT-185, 186 and 187 now perform their repair and then a reactivation that succeeds. TC-CAT-212 exercises RULE-CAT-053 on every endpoint that applies it, checks that child deactivations and the three repairs succeed, and TC-CAT-210 checks the three affordances on screen.

## Consequences
- RULE-CAT-053 now applies to API-CAT-003 (other than a categoryFk-only change), 008, 010, 012, 014, 015, 018 and 019. API-CAT-006 and 017 no longer answer `CAT-422-PARENT-INACTIVE`, and the api-spec no longer lists it on them.
- New AC-CAT-154, 155, 156 under REQ-CAT-135. REQ-CAT-135, RULE-CAT-053, SRS A7 and the `CAT-422-PARENT-INACTIVE` trigger are amended.
- No schema or endpoint changes.

## Traces
REQ-CAT-130 · REQ-CAT-135 · RULE-CAT-019 · RULE-CAT-026 · RULE-CAT-050 · RULE-CAT-053 · API-CAT-003 · API-CAT-005 · API-CAT-006 · API-CAT-017 · AC-CAT-154 · AC-CAT-155 · AC-CAT-156 · TC-CAT-185 · TC-CAT-186 · TC-CAT-187 · TC-CAT-210 · TC-CAT-212

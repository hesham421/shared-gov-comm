Both C10.11 findings are fixed: `F3-SCR-INV-012` and `F4-SCR-INV-011` each now have a test case naming them on its `Package` line. I haven't rerun `gov.py analyze`; I only checked that the frontend plan has 69 START/END marker pairs and the three package lines are right.

The cause was in my own ADR-INV-037. It listed both units as packages nothing could test, which was wrong for both. I recorded the fix in a new ADR, ADR-INV-039 (ACCEPTED, non-breaking), rather than rewriting 037.

**`frontend-test-plan-inv.md`**
- **TC-INV-238** now names `F3-SCR-INV-012` instead of `F4-SCR-INV-012`. This is what ADR-INV-037's own rule 3 calls for, since the valuation Variant and Item filters are search-form pickers that F3 binds. The case itself is unchanged.
- **New TC-INV-240** is derived from AC-INV-069 (REQ-INV-063) and names `F4-SCR-INV-012`. It checks that the valuation screen shows total quantity 20, average cost 23.0000, value 460.00, the grand total and the detail drawer. It replaces what `F4-SCR-INV-012` lost when TC-INV-238 moved.
- **New TC-INV-241** is derived from AC-INV-071 (REQ-INV-065) and names `F4-SCR-INV-011`. The Owner switches "Allow negative stock" off and saves. The case checks that the request sends `false` with the horizon unchanged, and that the horizon field stays hidden while lotExpiry is off. The refusal of a later sale stays on the backend case TC-INV-017.
- I updated the rest of the file to match:
  - the header, now owning TC-INV-173 … 241 and listing ADR-INV-039;
  - the `traces=` of the TEST-PLAN-FE phase and the UI-FLOWS SUB, which gain AC-069, AC-071, REQ-063 and REQ-065;
  - the AC, REQ, SCR and Package index tables;
  - COVERAGE: 55 ACs and 50 REQs on this track, 13 units with no case, and 69 cases for 55 ACs + 11 UXD (ratio 1.05).

**`backend-test-plan-inv.md`**
- I changed only cross-references: the header range now ends at 241, and the AC-069 and AC-071 rows list the new frontend cases.

**ADR-INV-039** is at `analysis\decisions\INV\ADR-INV-039.md`, next to the two test plans. It records the move of TC-INV-238 and the two new AC-derived cases. It also states that ADR-INV-037 point 4 no longer applies to these two units.
I fixed the C10.11 finding by moving one existing test case onto `F4-SCR-PTY-005`. I only edited `analysis/modules/PTY/P4/frontend-test-plan-pty.md`. I did not re-run `gov.py analyze`, so the fix is not yet confirmed by the checker.

**The fix:** TC-PTY-134 (from AC-PTY-101) now names `F4-SCR-PTY-005` on its `Package` line instead of `SEC-FE`. The Seller/Cashier in that case has no permission at all on the settings page. So what the test actually checks is the route guard and the menu entry, and those two things are defined in the F4 block for that screen (`F4-SCR-PTY-005`). `SEC-FE` still has an acceptance case, TC-PTY-142, so no other unit is left without tests. I added no new test case and didn't renumber any id or raise any question. I wrote no ADR because the package follows from what the frontend plan already says.

**What I changed:**
1. **F-N2 (derivation notes):** route guards and menu entries now count as F4. `SEC-FE` keeps the hidden buttons and fields inside a screen the user can open. I added a sentence saying TC-PTY-134 is `F4-SCR-PTY-005`'s acceptance, and removed `F4-SCR-PTY-005` from the list of units with no test case.
2. **TC-PTY-134:** `Package      : SEC-FE` became `Package      : F4-SCR-PTY-005`.
3. **AC → TC index:** the AC-PTY-101 row now shows package `F4-SCR-PTY-005`.
4. **Package → TC table:**
   - new row `F4-SCR-PTY-005 | TC-PTY-134 | 1`
   - `SEC-FE` reduced to `TC-PTY-142 | 1`
   - `F4-SCR-PTY-005` removed from the "none" row, which now lists `F1-SCR-PTY-001 … 005` and `ALIGN-FE` only
5. **COVERAGE:** plan units with at least one test case went from 15/22 to 16/22, with only `F1-SCR-PTY-001 … 005` and `ALIGN-FE` left without one.

The finding didn't flag the F1 units, so I left them as they are.
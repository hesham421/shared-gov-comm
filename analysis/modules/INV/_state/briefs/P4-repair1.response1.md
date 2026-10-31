I fixed the one C10.7 finding (msg-bound), but I couldn't re-run `gov.py analyze` here, so whether it clears is unconfirmed. The cause was that the frontend plan routed INV-403 with its message but never used the `text: <source row>` form, so TC-INV-212 asserted a text the plan didn't bind.

What I changed:
- **`analysis/modules/INV/P3_2/frontend-execution-plan-inv.md`**: I changed two lines that route INV-403 to the same form as the INV-422-HORIZON-RANGE line, which already passes. They now read `text: catalogue INV-403 «ليست لديك صلاحية لهذا الإجراء.» / "You do not have permission for this action." (AC-INV-154)`.
  - the INV-403 entry in the generic error routing list (around line 196);
  - the forbidden-responses line in the SEC-FE section. This is the package TC-INV-212 belongs to.

  The words are unchanged: they are copied exactly from the SRS (AC-INV-154) and from the INV-403 row of the backend error catalog.
- **`analysis/modules/INV/P4/frontend-test-plan-inv.md`**: TC-INV-212's Expected line now names its source, `text: catalogue INV-403`, bound by the SEC-FE row, with the same ar and en text.

I changed no ids and wrote no ADR, because there was no choice to make: the catalog already gives the text. The match rule is my guess: I couldn't open the checker, because reading outside `governance-shared` was blocked. So I copied the form of the horizon line rather than working from the rule itself.
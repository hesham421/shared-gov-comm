<!-- source: PHASE:INT-UXD -->
<!-- traces: AC-PTY-117, REQ-PTY-094, REQ-PTY-103, SCR-PTY-004, UXD-PTY-001 -->
<!-- PHASE:INT-UXD:START traces=UXD-PTY-001,REQ-PTY-094,REQ-PTY-103,AC-PTY-117,SCR-PTY-004 -->
## INT-UXD — foreign-owned field rendered by this module

One UXD (UXD-PTY-001 — `decidedBy`, a user of the given `erp-core:modules.sec`, rendered on SCR-PTY-004 through USER-LABEL-LOOKUP); 2 TCs (≤ 8 — no SUB), grouped under the owner `erp-core:modules.sec` (given).

<!-- TC:TC-PTY-152:START traces=UXD-PTY-001,REQ-PTY-094,REQ-PTY-103,AC-PTY-117,SCR-PTY-004 -->
### TC-PTY-152 — Decider rendered by name on the request Detail
Derived from : UXD-PTY-001  (REQ-PTY-094, REQ-PTY-103, AC-PTY-117)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : — 
Package      : F4-SCR-PTY-004
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW; request CO-000031 APPROVED with decidedBy = the identifier `<deciderId>` of the deciding Owner; the given sec user read answers `<deciderId>` with display name `<decider name>` (owner present)
Host data    : none
Steps        : 1. open the Detail of CO-000031
Expected     : "Decided by" «صاحب القرار» shows `<decider name>` — never the raw identifier; one sec user read is made for `<deciderId>`
Test data    : CO-000031 (AC-PTY-117); `<deciderId>`, `<decider name>` placeholders (the owner's data)
<!-- TC:TC-PTY-152:END -->

<!-- TC:TC-PTY-153:START traces=UXD-PTY-001,REQ-PTY-094,REQ-PTY-103,AC-PTY-117,SCR-PTY-004 -->
### TC-PTY-153 — Decider read failing shows the declared empty value
Derived from : UXD-PTY-001  (REQ-PTY-094, REQ-PTY-103, AC-PTY-117)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : — 
Package      : F4-SCR-PTY-004
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: as TC-PTY-152, but the given sec user read for `<deciderId>` fails (owner read unavailable)
Host data    : none
Steps        : 1. open the Detail of CO-000031
Expected     : "Decided by" «صاحب القرار» reads «—»; the raw identifier is not shown; no error page — every other field of the Detail renders as in TC-PTY-143
Test data    : CO-000031 (AC-PTY-117); `<deciderId>` placeholder
<!-- TC:TC-PTY-153:END -->
<!-- PHASE:INT-UXD:END -->

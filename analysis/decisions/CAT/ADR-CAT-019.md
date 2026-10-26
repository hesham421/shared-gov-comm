# ADR-CAT-019 — Given `sec` owns credentials and the CUSTOMER realm; PTY owns the commercial Customer and links to the user by SOFT-READ
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: 4d9f89b2cc04
traces      : —

## Decision
traces: OI-13
Accept the ownership split. I drop one overclaim: round 1 said `customer-invitations` covers the guest → account step. The manifest lists capability words, not behaviour, so P0 fixes ownership only. SFR and PTY choose the mechanism (invitation or signup) in their SRS, against `SecCustomerInvitationApi` and the signup capability. The link to the user goes through `SecUserDirectoryApi`.
Sources: KB §3 D5, §5 #4; manifest `sec`; G9.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md

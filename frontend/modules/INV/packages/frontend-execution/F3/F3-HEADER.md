<!-- source: PHASE:F3 — preamble before the first SUB -->
<!-- traces: REQ-INV-139, REQ-INV-140, SCR-INV-001, SCR-INV-002, SCR-INV-003, SCR-INV-004, SCR-INV-005, SCR-INV-006, SCR-INV-007, SCR-INV-008, SCR-INV-009, SCR-INV-010, SCR-INV-011, SCR-INV-012, UXD-INV-001, UXD-INV-002, UXD-INV-004, UXD-INV-005, UXD-INV-006, UXD-INV-007, UXD-INV-008, UXD-INV-009, UXD-INV-010, UXD-INV-011, REQ-INV-003, REQ-INV-004, REQ-INV-009, REQ-INV-010, REQ-INV-013, REQ-INV-014, REQ-INV-017, REQ-INV-022, REQ-INV-023, REQ-INV-028, REQ-INV-029, REQ-INV-031, REQ-INV-039, REQ-INV-063, REQ-INV-065, REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-074, REQ-INV-075, REQ-INV-078, REQ-INV-079, REQ-INV-082, REQ-INV-084, REQ-INV-086, REQ-INV-087, REQ-INV-088, REQ-INV-089, REQ-INV-090, REQ-INV-091, REQ-INV-095, REQ-INV-097, REQ-INV-098, REQ-INV-101, REQ-INV-102, REQ-INV-104, REQ-INV-105, REQ-INV-108, REQ-INV-109, REQ-INV-116, REQ-INV-118, REQ-INV-119, REQ-INV-122, REQ-INV-123, REQ-INV-124, REQ-INV-125, REQ-INV-128, REQ-INV-129, AC-INV-004, AC-INV-026, AC-INV-078, AC-INV-081, AC-INV-090, AC-INV-093, AC-INV-098, AC-INV-109, AC-INV-111, AC-INV-113, AC-INV-133, AC-INV-141, API-INV-001, API-INV-003, API-INV-004, API-INV-009, API-INV-011, API-INV-012, API-INV-016, API-INV-018, API-INV-020, API-INV-022, API-INV-023, API-INV-026, API-INV-028, API-INV-029, API-INV-030, API-INV-034, API-INV-036, API-INV-037, API-INV-040, API-INV-042, API-INV-043, API-INV-045, API-INV-047, API-INV-049, API-INV-050 -->
## F3 — Forms & Validators

**RF3.** Forms use `react-hook-form` with a `zod` resolver. Each schema mirrors the request schema of
`api-spec-inv.yaml`: its required fields, `maxLength`, `minimum` and `exclusiveMinimum`. Where the SRS RULE can be
checked in the browser, the schema adds it with the RULE's own message, in ar and en. The server stays the
authority: every server code reaches the same field through the F2 routing. Message keys are the RULE ids, and
the texts are the SRS A5 messages. Decimal fields accept at most 3 places (quantities and measures) or 4 places
(costs), per DEFAULT D-06 and RULE-INV-017. Validation runs on submit and then on change. Search forms validate
only date ranges, where "from" must not be after "to"; the message is «تاريخ البداية بعد تاريخ النهاية.» /
"The start date is after the end date.".

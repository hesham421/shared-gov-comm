I ran the YAML check I couldn't run before, using `C:\repos\commerce\.venv\Scripts\python.exe` (the background search turned it up). Everything I edited still parses:

- **API spec:** API-INV-047 now answers 201, 400, 401, 403, 404, 409, 422 and 500, and `idempotencyKey` is required. The in-process-only list has 5 codes; INV-409-IDEMPOTENCY-KEY-REUSED is no longer among them.
- **Backend plan blocks:** the error catalog has 59 rows, the permission matrix 12, bootstrap 52 (the new notification template included), and totals and self-check both parse.
- **Frontend plan blocks:** api-surface and all 12 screen-hooks blocks parse.
- **SRS blocks:** lookups, module-dependencies and given-usage parse.

This only confirms the YAML is well-formed. I still haven't run `gov.py analyze`, so the orchestrator's analyze pass remains the real check.
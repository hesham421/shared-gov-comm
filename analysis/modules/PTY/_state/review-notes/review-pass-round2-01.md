# G1 confirmed as MAJOR. `conventions.md` §Error codes rule 3 says `GlobalExceptionHandler` owns `VALIDATION_ERROR`, `ACCESS_DENIED`, `NOT_FOUND`, `CONCURRENT_MODIFICATION` and `DATA_INTEGRITY_VIOLATION`, and the module may not register a second advice or re-map a `LocalizedException`. The PTY-prefixed platform rows for 400, 401, 403 and 500 cannot be emitted as written. The fix also has to cover optimistic-lock failures, because the plan has `@Version` but no catalog row for them. The PTY-404 rows stay: a service throws them as `LocalizedException(Status.NOT_FOUND, PtyErrorCodes.X)`.

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: PTY        Version: v1
Written: 2026-10-29T10:20:34+00:00



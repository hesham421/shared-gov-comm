<!-- source: PHASE:F3 — preamble before the first SUB -->
<!-- traces: REQ-PTY-001, REQ-PTY-004, REQ-PTY-005, REQ-PTY-006, REQ-PTY-007, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-012, REQ-PTY-013, REQ-PTY-014, REQ-PTY-015, REQ-PTY-016, REQ-PTY-017, REQ-PTY-018, REQ-PTY-020, REQ-PTY-021, REQ-PTY-022, REQ-PTY-023, REQ-PTY-024, REQ-PTY-025, REQ-PTY-026, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-039, REQ-PTY-040, REQ-PTY-041, REQ-PTY-047, REQ-PTY-048, REQ-PTY-049, REQ-PTY-064, REQ-PTY-065, REQ-PTY-066, REQ-PTY-067, REQ-PTY-068, REQ-PTY-069, REQ-PTY-070, REQ-PTY-071, REQ-PTY-072, REQ-PTY-073, REQ-PTY-077, REQ-PTY-078, REQ-PTY-080, REQ-PTY-085, REQ-PTY-086, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-090, REQ-PTY-091, REQ-PTY-092, REQ-PTY-093, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-098, REQ-PTY-100, REQ-PTY-101, REQ-PTY-102, REQ-PTY-103, REQ-PTY-104, REQ-PTY-105, REQ-PTY-106, REQ-PTY-107, REQ-PTY-108, REQ-PTY-109, AC-PTY-007, AC-PTY-010, AC-PTY-011, AC-PTY-013, AC-PTY-021, AC-PTY-029, AC-PTY-031, AC-PTY-055, AC-PTY-056, AC-PTY-057, AC-PTY-077, AC-PTY-078, AC-PTY-082, AC-PTY-087, AC-PTY-088, AC-PTY-096, AC-PTY-097, AC-PTY-098, AC-PTY-101, AC-PTY-102, AC-PTY-106, AC-PTY-107, AC-PTY-109, AC-PTY-111, AC-PTY-114, AC-PTY-115, AC-PTY-116, AC-PTY-117, AC-PTY-122, AC-PTY-123, API-PTY-001, API-PTY-002, API-PTY-003, API-PTY-004, API-PTY-005, API-PTY-006, API-PTY-007, API-PTY-008, API-PTY-009, API-PTY-010, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015, API-PTY-016, API-PTY-017, API-PTY-018, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-024, API-PTY-025, API-PTY-026, API-PTY-027, API-PTY-028, API-PTY-029, API-PTY-030, API-PTY-031, API-PTY-032, UXD-PTY-001, SCR-PTY-001, SCR-PTY-002, SCR-PTY-003, SCR-PTY-004, SCR-PTY-005 -->
## F3 — Forms & Validators

Forms use react-hook-form with a zod resolver. The client validators repeat the SRS rules only to answer early; the server stays the authority and its refusals are routed as F2 declares. Every client message is the SRS A5 message of the rule, in ar and en, so the client and the server say the same words. Maximum lengths are the document's `maxLength` values (read in api-spec-pty.yaml, not restated). On submit, empty text inputs become `null`; system fields (`code`, `docNo`, audit fields, `isActiveFl`, `isWalkInFl`) are never in a form and never sent.

Messages used below (SRS A5, verbatim):

| Rule | ar | en |
|---|---|---|
| RULE-PTY-001 | أدخل اسم الطرف بالعربية أو بالإنجليزية. | Enter the party name in Arabic or English. |
| RULE-PTY-003 | صيغة البريد الإلكتروني غير صحيحة، مثال: name@example.com | The email format is not valid, for example: name@example.com |
| RULE-PTY-004 | رقم الجوال يتكون من 7 إلى 15 رقماً، ويمكن أن يبدأ بعلامة +. | The mobile number has 7 to 15 digits and can start with +. |
| RULE-PTY-008 | مدة السداد يجب أن تكون بين 0 و365 يوماً. | Payment terms must be between 0 and 365 days. |
| RULE-PTY-011 | لا يُمنح العميل النقدي حد ائتمان أو مدة سداد. سجّل العميل باسمه لمنحه الائتمان. | The walk-in customer cannot have a credit limit or payment terms. Register the customer by name to grant credit. |
| RULE-PTY-015 | أدخل السطر الأول من العنوان. | Enter the first address line. |
| RULE-PTY-016 | اختر نوع عنوان من القائمة. | Choose an address type from the list. |
| RULE-PTY-018 | اختر مجموعة عملاء من القائمة. | Choose a customer group from the list. |
| RULE-PTY-022 | حد الائتمان يجب أن يكون صفراً أو أكثر. | The credit limit must be zero or more. |
| RULE-PTY-027 | أدخل سبب الرفض. | Enter the reason for the rejection. |
| RULE-PTY-032 | مبلغ البيع يجب أن يكون أكبر من صفر. | The sale amount must be greater than zero. |
| (form) required | هذا الحقل مطلوب. | This field is required. |
| (form) max length | الحد الأقصى {n} حرفاً. | At most {n} characters. |

Shared validators (`src/modules/pty/forms/validators.ts`):
- `partyNames` — `nameAr`, `nameEn` optional, trimmed; refinement "at least one non-empty" → RULE-PTY-001 on the name group (one mandatory mark for the group — AC-PTY-007, AC-PTY-021).
- `email` — optional; when non-empty: exactly one `@`, non-empty local part, domain part containing at least one `.` → RULE-PTY-003.
- `mobile` — optional; when non-empty, after removing spaces and dashes: `^\+?\d{7,15}$` → RULE-PTY-004.
- `paymentTermDays` — optional whole number 0 … 365 → RULE-PTY-008.
- `addressLine` — `addressTypeCode` required, one of the active ADDRESS_TYPE codes → RULE-PTY-016; `line1` required, trimmed → RULE-PTY-015; other address fields optional with their maximum lengths.

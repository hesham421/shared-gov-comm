<!-- source: PHASE:DATA-DOM / SUB:DATA-DOM-LOOKUP -->
<!-- context: DATA-DOM-HEADER.md — phase-level preamble -->
<!-- traces: DBF-CAT-035, DBF-CAT-036, DBF-CAT-037, DBF-CAT-038, DBF-CAT-039, DBF-CAT-040, DBF-CAT-041, DBF-CAT-042, DBF-CAT-043, DBF-CAT-044, DBF-CAT-045, DBF-CAT-046, DBF-CAT-047, DBF-CAT-048, DBF-CAT-049, DBF-CAT-050, DBF-CAT-051, DBF-CAT-052, DBF-CAT-053, DBF-CAT-054, DBF-CAT-055, DBF-CAT-056, DBF-CAT-057, DBF-CAT-058, DBF-CAT-059, DBF-CAT-136, DBF-CAT-137, DBF-CAT-138, DBF-CAT-139, DBF-CAT-140, DBF-CAT-141, DBF-CAT-142, DBF-CAT-143, DBF-CAT-144, DBF-CAT-145, DBF-CAT-146, DBF-CAT-147, DBF-CAT-148, DBF-CAT-149, DBF-CAT-150, DBF-CAT-151, DBF-CAT-152, DBF-CAT-153, DBF-CAT-154, DBF-CAT-155, DBF-CAT-156, DBF-CAT-157, DBF-CAT-158, DBF-CAT-159, DBF-CAT-160, DBF-CAT-161, REQ-CAT-094, REQ-CAT-122 -->
<!-- SUB:DATA-DOM-LOOKUP:START traces=DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-038,DBF-CAT-039,DBF-CAT-040,DBF-CAT-041,DBF-CAT-042,DBF-CAT-043,DBF-CAT-044,DBF-CAT-045,DBF-CAT-046,DBF-CAT-047,DBF-CAT-048,DBF-CAT-049,DBF-CAT-050,DBF-CAT-051,DBF-CAT-052,DBF-CAT-053,DBF-CAT-054,DBF-CAT-055,DBF-CAT-056,DBF-CAT-057,DBF-CAT-058,DBF-CAT-059,DBF-CAT-136,DBF-CAT-137,DBF-CAT-138,DBF-CAT-139,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-144,DBF-CAT-145,DBF-CAT-146,DBF-CAT-147,DBF-CAT-148,DBF-CAT-149,DBF-CAT-150,DBF-CAT-151,DBF-CAT-152,DBF-CAT-153,DBF-CAT-154,DBF-CAT-155,DBF-CAT-156,DBF-CAT-157,DBF-CAT-158,DBF-CAT-159,DBF-CAT-160,DBF-CAT-161,REQ-CAT-094,REQ-CAT-122 -->
### SUB DATA-DOM-LOOKUP

### ENT-CAT-003 — Variant attribute / خاصية المتغيّر      kind: lookup
BINDINGS       table CAT_VAR_ATTR · PK VARIANT_ATTRIBUTE_PK (DBF-CAT-035) · PK generation `identity` → `VARIANT_ATTRIBUTE_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_VAR_ATTR
BUSINESS CODE  code · CODE (DBF-CAT-036) · owner-entered, 1–30 Latin letters, digits or hyphens (CHK_CAT_VAR_ATTR_CODE) · uniqueness UQ_CAT_VAR_ATTR_CODE (TENANT_ID, CODE) · fixed after creation
DEFAULT FIELDS lookup → code, nameAr, nameEn, sortOrder, isActiveFl (+ the given's tenantId, version, audit — D-P2-02)
#### FIELDS ENT-CAT-003 — CAT_VAR_ATTR
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-035 | variantAttributePk | VARIANT_ATTRIBUTE_PK | BIGINT | NOT NULL | yes | PK_CAT_VAR_ATTR, UQ_CAT_VAR_ATTR_PK | المعرّف / Identifier |
| DBF-CAT-036 | code | CODE | VARCHAR(30) | NOT NULL | after create | UQ_CAT_VAR_ATTR_CODE, CHK_CAT_VAR_ATTR_CODE | الرمز / Code |
| DBF-CAT-037 | nameAr | NAME_AR | VARCHAR(200) | NOT NULL | no | UQ_CAT_VAR_ATTR_NAME_AR, CHK_CAT_VAR_ATTR_NAME_AR | الاسم بالعربية / Arabic name |
| DBF-CAT-038 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | — | الاسم بالإنجليزية / English name |
| DBF-CAT-039 | sortOrder | SORT_ORDER | INTEGER | NOT NULL | no | — | الترتيب / Sort order |
| DBF-CAT-040 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-041 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-042 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-043 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-044 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-045 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-046 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: code, nameAr, nameEn, sortOrder (default next) · update-request: nameAr, nameEn, sortOrder, version · response VariantAttributeView with its values
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-001, 002, 008 — full text under ENT-CAT-001)
- RULE-CAT-016 — Attribute and value codes and names unique · scope ENT-CAT-003, 004 · trigger on create · on update · on reactivation (API-CAT-054, 055) · statement: The system shall require a code of 1 to 30 Latin letters, digits or hyphens, unique per tenant for a Variant attribute and per attribute for a value, and a nameAr unique among the active records of the same scope; a record being reactivated keeps its code and must not carry the nameAr of another active record of its scope. · DB UQ_CAT_VAR_ATTR_CODE, UQ_CAT_VAR_ATTR_NAME_AR, UQ_CAT_VAR_ATTR_VALUE_CODE, UQ_CAT_VAR_ATTR_VALUE_NAME_AR, CHK_CAT_VAR_ATTR_CODE, CHK_CAT_VAR_ATTR_VALUE_CODE · layer service (QR-CAT-023 / QR-CAT-028) + constraint translation · message CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
- RULE-CAT-017 — Attribute or value in use stays active · scope ENT-CAT-003, 004 · trigger on deactivation · statement: The system shall prevent deactivating a Variant attribute or value that an active assignment of an active Variant carries. · DB — · layer service (QR-CAT-021 under the row lock) · message CAT-409-ATTRIBUTE-VALUE-IN-USE ({count})
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-040) only — deactivate via API-CAT-028 (RULE-CAT-017), reactivate via API-CAT-054 (RULE-CAT-016 — ADR-CAT-069); its values keep their own state; an inactive attribute takes no update (API-CAT-027 answers CAT-400-REQUEST-INVALID) until it is reactivated
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         VariantAttributeDomain.create(request) · VariantAttributeDomain.from(stored, request) — RULE-CAT-001, 002, 016 (code format); uniqueness and RULE-CAT-017 in the service
AUDIT          @Audited(entityType = "CAT_VAR_ATTR")
REPOSITORY OPS QR-CAT-022, QR-CAT-023, QR-CAT-024, QR-CAT-025, QR-CAT-026

### ENT-CAT-004 — Variant attribute value / قيمة خاصية المتغيّر      kind: lookup
BINDINGS       table CAT_VAR_ATTR_VALUE · PK VARIANT_ATTRIBUTE_VALUE_PK (DBF-CAT-047) · PK generation `identity` → `VARIANT_ATTRIBUTE_VALUE_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_VAR_ATTR_VALUE
BUSINESS CODE  code · CODE (DBF-CAT-048) · owner-entered (CHK_CAT_VAR_ATTR_VALUE_CODE) · uniqueness UQ_CAT_VAR_ATTR_VALUE_CODE (TENANT_ID, VARIANT_ATTRIBUTE_FK, CODE) · fixed after creation
DEFAULT FIELDS lookup → code, nameAr, nameEn, sortOrder, isActiveFl (+ tenantId, version, audit)
#### FIELDS ENT-CAT-004 — CAT_VAR_ATTR_VALUE
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-047 | variantAttributeValuePk | VARIANT_ATTRIBUTE_VALUE_PK | BIGINT | NOT NULL | yes | PK_CAT_VAR_ATTR_VALUE, UQ_CAT_VAR_ATTR_VALUE_PK_ATTR | المعرّف / Identifier |
| DBF-CAT-048 | code | CODE | VARCHAR(30) | NOT NULL | after create | UQ_CAT_VAR_ATTR_VALUE_CODE, CHK_CAT_VAR_ATTR_VALUE_CODE | الرمز / Code |
| DBF-CAT-049 | nameAr | NAME_AR | VARCHAR(200) | NOT NULL | no | UQ_CAT_VAR_ATTR_VALUE_NAME_AR, CHK_CAT_VAR_ATTR_VALUE_NAME_AR | الاسم بالعربية / Arabic name |
| DBF-CAT-050 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | — | الاسم بالإنجليزية / English name |
| DBF-CAT-051 | sortOrder | SORT_ORDER | INTEGER | NOT NULL | no | — | الترتيب / Sort order |
| DBF-CAT-052 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-053 | variantAttributeFk | VARIANT_ATTRIBUTE_FK | BIGINT | NOT NULL | yes (path) | FK_CAT_VAR_ATTR_VALUE_CAT_VAR_ATTR, UQ_CAT_VAR_ATTR_VALUE_PK_ATTR | الخاصية / Attribute |
| DBF-CAT-054 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-055 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-056 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-057 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-058 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-059 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: code, nameAr, nameEn, sortOrder (default next within the attribute); variantAttributeFk from the path · update-request: nameAr, nameEn, sortOrder, version · response VariantAttributeValueView
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-001, 002, 008 — under ENT-CAT-001; RULE-CAT-016, 017 — under ENT-CAT-003)
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-052) only — deactivate via API-CAT-031 (RULE-CAT-017), reactivate via API-CAT-055 under an active attribute (RULE-CAT-016 — ADR-CAT-069); an inactive value takes no update (API-CAT-030 answers CAT-400-REQUEST-INVALID) until it is reactivated
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         VariantAttributeValueDomain.create(request, attribute) · VariantAttributeValueDomain.from(stored, request) — RULE-CAT-001, 002, 016 (code format); uniqueness and RULE-CAT-017 in the service
AUDIT          @Audited(entityType = "CAT_VAR_ATTR_VALUE")
REPOSITORY OPS QR-CAT-027, QR-CAT-028, QR-CAT-029, QR-CAT-030, QR-CAT-031

### ENT-CAT-011 — Scale label layout / إعداد ملصق الميزان      kind: config
BINDINGS       table CAT_SCALE_LABEL_LAYOUT · PK SCALE_LABEL_LAYOUT_PK (DBF-CAT-136) · PK generation `identity` → `SCALE_LABEL_LAYOUT_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_SCALE_LABEL_LAYOUT
BUSINESS CODE  key · KEY (DBF-CAT-137) · prefix 02 or 20–29 (CHK_CAT_SCALE_LABEL_LAYOUT_KEY) · uniqueness UQ_CAT_SCALE_LABEL_LAYOUT_KEY among active rows · fixed after creation
DEFAULT FIELDS config → key, valueAr, valueEn, isActiveFl (+ tenantId, version, audit)
#### FIELDS ENT-CAT-011 — CAT_SCALE_LABEL_LAYOUT
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-136 | scaleLabelLayoutPk | SCALE_LABEL_LAYOUT_PK | BIGINT | NOT NULL | yes | PK_CAT_SCALE_LABEL_LAYOUT | المعرّف / Identifier |
| DBF-CAT-137 | key | KEY | VARCHAR(2) | NOT NULL | after create | CHK_CAT_SCALE_LABEL_LAYOUT_KEY, UQ_CAT_SCALE_LABEL_LAYOUT_KEY | البادئة / Prefix |
| DBF-CAT-138 | valueAr | VALUE_AR | VARCHAR(200) | NULL | no | — | الوصف بالعربية / Arabic description |
| DBF-CAT-139 | valueEn | VALUE_EN | VARCHAR(200) | NULL | no | — | الوصف بالإنجليزية / English description |
| DBF-CAT-140 | scaleCodeLength | SCALE_CODE_LENGTH | INTEGER | NOT NULL | after create | CHK_CAT_SCALE_LABEL_LAYOUT_SCALE_CODE_LENGTH | طول رمز الميزان / Scale code length |
| DBF-CAT-141 | embeddedValueTypeCode | EMBEDDED_VALUE_TYPE_CODE | VARCHAR(50) | NOT NULL | no | — | نوع القيمة المضمّنة / Embedded value type |
| DBF-CAT-142 | valueDecimals | VALUE_DECIMALS | INTEGER | NOT NULL | no | CHK_CAT_SCALE_LABEL_LAYOUT_VALUE_DECIMALS | الأرقام العشرية / Decimals |
| DBF-CAT-143 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-144 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-145 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-146 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-147 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-148 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-149 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: key, scaleCodeLength (default 5), embeddedValueTypeCode (default WEIGHT), valueDecimals (default 3), valueAr (default «ملصق ميزان {key}»), valueEn (default "Scale label {key}") · update-request: everything except key and scaleCodeLength, + version (both fixed after creation — ADR-CAT-064) · response ScaleLabelLayoutView
LOOKUP FIELDS  embeddedValueTypeCode · EMBEDDED_VALUE_TYPE_CODE (DBF-CAT-141) · CAT_SCALE_VALUE_TYPE
DOMAIN RULES   (RULE-CAT-006 — under ENT-CAT-001; RULE-CAT-032, 035 — under ENT-CAT-009)
- RULE-CAT-034 — Valid scale label layout · scope ENT-CAT-011 · trigger on create · on update · statement: The system shall require a key of 02 or 20 to 29 that no other active layout uses, a scaleCodeLength of 4 to 6 and valueDecimals of 0 to 3. The key and the scaleCodeLength are set on create only; an update carries neither (ADR-CAT-064). · DB CHK_CAT_SCALE_LABEL_LAYOUT_KEY, CHK_CAT_SCALE_LABEL_LAYOUT_SCALE_CODE_LENGTH, CHK_CAT_SCALE_LABEL_LAYOUT_VALUE_DECIMALS, UQ_CAT_SCALE_LABEL_LAYOUT_KEY + domain (ScaleLabelLayoutDomain: key format, ranges, defaults) + service (key unused by another active layout) · layer domain + service · message CAT-422-SCALE-LAYOUT-INVALID
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-143) only — deactivate via API-CAT-034; an inactive layout takes no update (API-CAT-033 answers CAT-400-REQUEST-INVALID) and is not reactivated: a new layout is created under the same key, which the partial unique index UQ_CAT_SCALE_LABEL_LAYOUT_KEY (active rows only) allows; an inactive layout stops decoding (REQ-CAT-094), while the SCALE barcodes validated against it stay stored and active and decode again only while an active layout of the same scaleCodeLength exists (ADR-CAT-064). A change of embeddedValueTypeCode or valueDecimals (API-CAT-033) changes only how the embedded value of a later scanned label is read
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         ScaleLabelLayoutDomain.create(request) · ScaleLabelLayoutDomain.from(stored, request) — RULE-CAT-034 (key format, length 4–6, decimals 0–3, defaults 5 / WEIGHT / 3; `from` keeps the stored key and scaleCodeLength); key uniqueness and RULE-CAT-032 in the service
AUDIT          @Audited(entityType = "CAT_SCALE_LABEL_LAYOUT")
REPOSITORY OPS QR-CAT-059, QR-CAT-060, QR-CAT-061, QR-CAT-062, QR-CAT-063, QR-CAT-064, QR-CAT-074

### ENT-CAT-012 — Unit e-invoice code / رمز الوحدة في الفاتورة الإلكترونية      kind: config
BINDINGS       table CAT_UNIT_EINVOICE_CODE · PK UNIT_EINVOICE_CODE_PK (DBF-CAT-150) · PK generation `identity` → `UNIT_EINVOICE_CODE_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_UNIT_EINVOICE_CODE
BUSINESS CODE  key · KEY (DBF-CAT-151) · a CAT_UOM code · uniqueness UQ_CAT_UNIT_EINVOICE_CODE_KEY among active rows · fixed after creation
DEFAULT FIELDS config → key, valueAr, valueEn (copied from the CAT_UOM labels at save — D-P2-09), isActiveFl (+ tenantId, version, audit)
#### FIELDS ENT-CAT-012 — CAT_UNIT_EINVOICE_CODE
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-150 | unitEinvoiceCodePk | UNIT_EINVOICE_CODE_PK | BIGINT | NOT NULL | yes | PK_CAT_UNIT_EINVOICE_CODE | المعرّف / Identifier |
| DBF-CAT-151 | key | KEY | VARCHAR(50) | NOT NULL | after create | UQ_CAT_UNIT_EINVOICE_CODE_KEY | وحدة القياس / Unit of measure |
| DBF-CAT-152 | valueAr | VALUE_AR | VARCHAR(200) | NULL | yes (derived) | — | الاسم بالعربية / Arabic name |
| DBF-CAT-153 | valueEn | VALUE_EN | VARCHAR(200) | NULL | yes (derived) | — | الاسم بالإنجليزية / English name |
| DBF-CAT-154 | einvoiceUnitCode | EINVOICE_UNIT_CODE | VARCHAR(50) | NOT NULL | no | — | وحدة الفاتورة الإلكترونية / E-invoice unit |
| DBF-CAT-155 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-156 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-157 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-158 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-159 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-160 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-161 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: key, einvoiceUnitCode · update-request: einvoiceUnitCode, version · response UnitEinvoiceCodeView
LOOKUP FIELDS  key · KEY (DBF-CAT-151) · CAT_UOM · einvoiceUnitCode · EINVOICE_UNIT_CODE (DBF-CAT-154) · CAT_EINVOICE_UNIT
DOMAIN RULES   (RULE-CAT-006 — under ENT-CAT-001)
- RULE-CAT-043 — One e-invoice unit per unit of measure · scope ENT-CAT-012 · trigger on create · statement: The system shall prevent creating a Unit e-invoice code for a key that already has an active one. · DB UQ_CAT_UNIT_EINVOICE_CODE_KEY + service (QR-CAT-066) · layer service · message CAT-409-UNIT-EINVOICE-DUPLICATE
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-155) only — deactivate via API-CAT-037; reads then return an empty e-invoice unit (REQ-CAT-122); an inactive row takes no update (API-CAT-036 answers CAT-400-REQUEST-INVALID) and is not reactivated: a new record is created under the same key (API-CAT-035), which the partial unique index UQ_CAT_UNIT_EINVOICE_CODE_KEY (active rows only) allows
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         UnitEinvoiceCodeDomain.create(request, uomLabels) · UnitEinvoiceCodeDomain.from(stored, request) — valueAr / valueEn copied from the CAT_UOM labels (D-P2-09); RULE-CAT-006 and RULE-CAT-043 in the service
AUDIT          @Audited(entityType = "CAT_UNIT_EINVOICE_CODE")
REPOSITORY OPS QR-CAT-065, QR-CAT-066, QR-CAT-067, QR-CAT-068, QR-CAT-069, QR-CAT-070
<!-- SUB:DATA-DOM-LOOKUP:END -->

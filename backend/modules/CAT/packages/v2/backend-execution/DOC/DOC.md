<!-- source: PHASE:DOC -->
<!-- traces: REQ-CAT-001, REQ-CAT-002, REQ-CAT-003, REQ-CAT-021, REQ-CAT-029, REQ-CAT-039, REQ-CAT-058, REQ-CAT-095, REQ-CAT-097, REQ-CAT-101, REQ-CAT-119, REQ-CAT-125 -->
<!-- PHASE:DOC:START traces=REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-029,REQ-CAT-039,REQ-CAT-058,REQ-CAT-095,REQ-CAT-097,REQ-CAT-101,REQ-CAT-119,REQ-CAT-125,REQ-CAT-021 -->
## PHASE DOC — API documentation and DTO catalogue

- The endpoints publish OpenAPI documentation at the given's api-docs location; `api-verify` holds it to `api-spec-cat.yaml`, which this stage derives from the API blocks and the Error Catalog (§ API document). Each operation carries its `API-CAT-*` id as `x-api-id`, its traces as `x-traces`, and every catalog code it can raise under `x-error-codes` of the response keyed by the row's status.
- Error body (every non-2xx response): `LocalizedException → {code, messageAr, messageEn}`. Success body: `ApiResponse<T>` with `data`, and — when a RULE's notice is shown on success — `code`, `messageAr`, `messageEn` (ADR-CAT-048). Paged bodies: `Page<T>` with `content`, `page`, `size`, `totalElements`, `totalPages`.
- Message bundles (checklist C11): one entry per Error Catalog code in `messages.properties` (en) and `messages_ar.properties` (ar), copied from the catalog — the 53 RULE rows and the 7 PLATFORM-STD rows alike, so no `CAT-` code falls back to the core bundle; placeholders `{flag}`, `{count}`, `{code}`, `{prefix}`, `{length}`, `{row}`, `{column}`, `{reason}` are filled by the service.

### Response DTOs (request bodies are written inline on each `Request` line)
Grammar: `- DTO <Name>: <field> <type>; …` with types int64, int32, string, boolean, decimal, date-time, array<…> and DTO names. Every stored field maps to the DBF of the same property (manifest); derived fields are the manifest's "— (derived)" rows.

- DTO ItemSummary: itemPk int64; code string; nameAr string; nameEn string; displayNameEn string; categoryFk int64; categoryCode string; categoryNameAr string; categoryNameEn string; brandCode string; itemKindCode string; publishStateCode string; isActiveFl boolean
- DTO ItemDetail: itemPk int64; code string; nameAr string; nameEn string; displayNameEn string; itemKindCode string; baseUomCode string; categoryFk int64; categoryCode string; categoryNameAr string; categoryNameEn string; brandCode string; taxClassCode string; einvoiceCodeTypeCode string; einvoiceCode string; descriptionAr string; descriptionEn string; publishStateCode string; isActiveFl boolean; version int64; createdBy string; createdAt date-time; updatedBy string; updatedAt date-time; variants array<VariantSummary>; itemUnits array<ItemUnitView>; bundleComponents array<BundleComponentView>; media array<MediaView>
- DTO VariantSummary: variantPk int64; code string; nameAr string; nameEn string; isDefaultFl boolean; isActiveFl boolean; einvoiceCodeTypeCode string; einvoiceCode string; effectiveEinvoiceCodeTypeCode string; effectiveEinvoiceCode string; version int64; attributeValues array<AttributeValueView>; barcodes array<BarcodeView>
- DTO VariantDetail: variantPk int64; itemFk int64; code string; nameAr string; nameEn string; isDefaultFl boolean; isActiveFl boolean; baseUomCode string; taxClassCode string; einvoiceCodeTypeCode string; einvoiceCode string; effectiveEinvoiceCodeTypeCode string; effectiveEinvoiceCode string; einvoiceUnitCode string; version int64; attributeValues array<AttributeValueView>; barcodes array<BarcodeView>
- DTO VariantGenerationResult: itemPk int64; variants array<VariantSummary>
- DTO AttributeValueView: variantAttributeFk int64; variantAttributeCode string; variantAttributeValueFk int64; valueCode string; valueNameAr string; valueNameEn string
- DTO BarcodeView: barcodePk int64; variantFk int64; barcodeValue string; barcodeTypeCode string; uomCode string; isActiveFl boolean; createdAt date-time
- DTO ItemUnitView: itemUnitPk int64; itemFk int64; uomCode string; conversionFactor decimal; isPurchaseFl boolean; isSalesFl boolean; isActiveFl boolean; einvoiceUnitCode string; version int64
- DTO BundleComponentView: bundleComponentPk int64; bundleItemFk int64; componentVariantFk int64; componentVariantCode string; componentBaseUomCode string; quantity decimal; sortOrder int32; isActiveFl boolean
- DTO MediaView: itemMediaPk int64; itemFk int64; variantFk int64; fileDocumentRef int64; nameAr string; nameEn string; sortOrder int32; isPrimaryFl boolean; isActiveFl boolean; version int64
- DTO CategoryView: categoryPk int64; code string; nameAr string; nameEn string; parentCategoryFk int64; sortOrder int32; isActiveFl boolean; version int64
- DTO CategoryNode: categoryPk int64; code string; nameAr string; nameEn string; parentCategoryFk int64; sortOrder int32; isActiveFl boolean; children array<CategoryNode>
- DTO VariantAttributeView: variantAttributePk int64; code string; nameAr string; nameEn string; sortOrder int32; isActiveFl boolean; version int64; values array<VariantAttributeValueView>
- DTO VariantAttributeValueView: variantAttributeValuePk int64; variantAttributeFk int64; code string; nameAr string; nameEn string; sortOrder int32; isActiveFl boolean; version int64
- DTO VariantSearchRow: variantPk int64; code string; itemPk int64; itemNameAr string; nameAr string; nameEn string; firstBarcodeValue string
- DTO BarcodeLookupResult: variantPk int64; variantCode string; itemPk int64; itemCode string; itemNameAr string; uomCode string; embeddedValue decimal; embeddedValueTypeCode string
- DTO CatalogueSettings: scaleLabelLayouts array<ScaleLabelLayoutView>; unitEinvoiceCodes array<UnitEinvoiceCodeView>
- DTO ScaleLabelLayoutView: scaleLabelLayoutPk int64; key string; valueAr string; valueEn string; scaleCodeLength int32; embeddedValueTypeCode string; valueDecimals int32; isActiveFl boolean; version int64
- DTO UnitEinvoiceCodeView: unitEinvoiceCodePk int64; key string; valueAr string; valueEn string; einvoiceUnitCode string; isActiveFl boolean; version int64
- DTO CatalogueSearchHit: itemPk int64; code string; nameAr string; nameEn string
- DTO TurnOffCheckResult: flag string; allowed boolean; dependentCount int32; dependentItems array<DependentItem>; messageAr string; messageEn string
- DTO LookupOption: code string; labelAr string; labelEn string; sortOrder int32
- DTO DependentItem: itemPk int64; code string; nameAr string
- DTO ImportResult: rowsRead int32; itemsCreated int32; rowsRejected int32; rejectedRows array<ImportRowRejection>
- DTO ImportRowRejection: rowNumber int32; column string; code string; messageAr string; messageEn string
<!-- PHASE:DOC:END -->

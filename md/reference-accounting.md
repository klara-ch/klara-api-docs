# Accounting

24 operations of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### GET /core/v1/accounting/accounts

List KLARA master chart-of-account rows.

Returns the canonical debit/credit accounts maintained by KLARA as the seed of any company's bookkeeping. The data is global — the same chart of accounts is returned for every tenant, so there are no tenant or company path parameters. When the optional `legal-form` query parameter is supplied, only accounts that carry a translation for that legal form are returned, and the `name` field is resolved from the legal-form-specific translation column. The `Accept-Language` header selects which translation is used; if absent or unsupported the platform default applies. The endpoint is a pure read — idempotent, no side effects. Any authenticated bearer token is accepted; the downstream service declares no role or permission requirement on this endpoint.

- operationId: `listAccountsV1`
- Required permission: none, this endpoint is how a token is obtained

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `legal-form` | query | no | string | Restricts the result to accounts that carry a translation for the given Swiss legal form, and resolves `name` from that legal-form-specific column. Allowed values: `LIMITED_LIABILITY`, `CORPORATION`, `OR`, `INDIVIDUALLY_OWNED_COMPANY`, `ASSOCIATION`, `SIMPLE_PARTNERSHIP`, `GENERAL_PARTNERSHIP`, `LIMITED_PARTNERSHIP`, `COOPERATIVE_COMPANY`, `FOUNDATION`. |
| `Accept-Language` | header | no | string | IETF language tag used to resolve the localized `name` field of each account. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Master accounts, sorted by `code` ascending. Empty array when the chart of accounts has not been seeded yet. |
| `401` | none declared | No Authorization header found or invalid token |
| `404` | ErrorMessage | The supplied `legal-form` value does not match any known legal form. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/accounts/account-displaying

List the account-displaying picker rows for the authenticated company.

Returns a flat list of `AccountDisplaying` rows for every account in the caller's company chart of accounts. Each row represents one selectable combination that a bookkeeper can pick when entering a manual journal line. Tenant and company are derived from the bearer token — there are no tenant or company path parameters.How rows are produced: For each master account the downstream service checks whether the account carries any linked sub-account definitions (e.g. a bank account linked to specific bank-accounts, a VAT account linked to VAT-rate items). If the account has no sub-account definitions, exactly one bare row is emitted with `combinedCode` equal to the bare account code (e.g. `"3000"`), `linkType` equal to `null`, and `specificationItem` equal to `null`. If the account has one or more sub-account link specifications, one row is emitted per individual sub-account entry, numbered sequentially across all link types with a 0-based counter suffix: `"1020-0"`, `"1020-1"`, …combinedCode: `"<accountCode>-<i>"` when a sub-account exists (e.g. `"1020-0"`), or the bare account code string (e.g. `"3000"`) when `linkType` is `null`. The suffix index is a flat 0-based counter per account, spanning all link types in declaration order.combinedName: `"<accountName> (<subAccountDisplay>)"` when a sub-account exists (e.g. `"Bank (UBS)"`), or the bare account name (e.g. `"Bürobedarf"`) when `linkType` is `null`.parentAccount: the full master account this row belongs to. Always populated, including for bare rows.linkType: the link-type category name (e.g. `"BANK"`, `"CUSTOMER"`, `"VAT_RATE"`). `null` when the account has no sub-account definitions.linkDisplay: the human-readable label for the link type in the current locale (e.g. `"Bank account"`). Falls back to the German label when no translation exists for the requested language. `null` when `linkType` is `null`.linkOptional: `true` when the sub-account selection is optional for the user (link keys are separated by `;` in the account definition); `false` when selecting a sub-account is mandatory (link keys separated by `,`); `null` when the account has no sub-account definitions.specificationItem: the resolved sub-account entry for this row. Its `link` field is the URI that must be round-tripped back to the booking endpoint when the user selects this row. Its `display` field is the human-readable label shown to the bookkeeper (e.g. `"UBS"`, `"8.1 %"`). `null` when `linkType` is `null`.Row order is stable: accounts are sorted by code ascending (same order as `GET /core/v1/accounting/accounts`); sub-account rows within each account preserve the link-type declaration order and then the entry order returned by the underlying service.The endpoint is a pure read — idempotent, no side effects. The caller must hold the `ACCOUNTING` permission on the company in scope.

- operationId: `getAccountDisplayingV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `legal-form` | query | no | string | When supplied, restricts the result to accounts that carry a translation for the given Swiss legal form and resolves the `parentAccount.name` field from the legal-form-specific translation column. When omitted, all accounts are returned and `account.name` is resolved from the generic language column. Allowed values: `LIMITED_LIABILITY`, `CORPORATION`, `OR`, `INDIVIDUALLY_OWNED_COMPANY`, `ASSOCIATION`, `SIMPLE_PARTNERSHIP`, `GENERAL_PARTNERSHIP`, `LIMITED_PARTNERSHIP`, `COOPERATIVE_COMPANY`, `FOUNDATION`. |
| `Accept-Language` | header | no | string | IETF language tag used to localize the `parentAccount.name` field of each row and the `linkDisplay` label. Falls back to German when no translation exists for the requested language. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Flat list of account-displaying rows, sorted by account code ascending. Each row contains `combinedCode`, `combinedName`, `parentAccount`, and the link fields populated for sub-account rows or `null` for bare rows. Returns an empty array when no accounts match the supplied `legal-form`. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | ErrorMessage | The company associated with the caller's session could not be resolved by the downstream accounting service, or the supplied `legal-form` value does not match any known legal form. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/accounts/by-code/{accountCode}

Resolve a KLARA master account by its chart-of-account code.

Loads a single master account row from KLARA's global chart of accounts, identified by its numeric `accountCode`. The data is global — the same chart of accounts is shared across every tenant, so there are no tenant or company path parameters. When the optional `legal-form` query parameter is supplied, the `name` field is resolved from the legal-form-specific translation column instead of the generic one. The `Accept-Language` header selects which translation is used; if absent or unsupported the platform default applies. The endpoint is a pure read — idempotent, no side effects. Any authenticated bearer token is accepted; the downstream service declares no role or permission requirement on this endpoint.

- operationId: `getAccountByCodeV1`
- Required permission: none, this endpoint is how a token is obtained

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `accountCode` | path | yes | string | Numeric chart-of-account code as printed on the KLARA master chart of accounts (e.g. `1000` for cash, `6000` for material expense). |
| `legal-form` | query | no | string | When supplied, resolves `name` from the legal-form-specific translation column. Allowed values: `LIMITED_LIABILITY`, `CORPORATION`, `OR`, `INDIVIDUALLY_OWNED_COMPANY`, `ASSOCIATION`, `SIMPLE_PARTNERSHIP`, `GENERAL_PARTNERSHIP`, `LIMITED_PARTNERSHIP`, `COOPERATIVE_COMPANY`, `FOUNDATION`. |
| `Accept-Language` | header | no | string | IETF language tag used to resolve the localized `name` field of the account. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiAccount | The master account matching `accountCode`. |
| `401` | none declared | No Authorization header found or invalid token |
| `404` | ErrorMessage | No master account exists for the supplied `accountCode`, or the supplied `legal-form` value does not match any known legal form. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/booking-types

List KLARA master accounting booking types.

Returns the canonical catalog of accounting booking types maintained by KLARA — the stable set of bookkeeping operations (general-ledger postings, payable / receivable invoices, payments, credit notes, prepayments, amortizations, delimitations) that every KLARA company can post against. The data is global — the same catalog is returned for every tenant, so there are no tenant or company path parameters. The `Accept-Language` header selects which translation of the human-readable `description` is returned; if absent or unsupported the German translation is used as a fallback. The endpoint is a pure read — idempotent, no side effects. Any authenticated bearer token is accepted; the downstream service declares no role or permission requirement on this endpoint.

- operationId: `listBookingTypesV1`
- Required permission: none, this endpoint is how a token is obtained

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `Accept-Language` | header | no | string | IETF language tag used to resolve the localized `description` of each booking type. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. Falls back to the German translation when no translation exists for the requested language. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Master booking types, ordered by `code` ascending. Empty array when the catalog has not been seeded yet. |
| `401` | none declared | No Authorization header found or invalid token |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/business-case-templates

List KLARA master accounting business-case templates.

Returns the global catalog of business-case templates — the manual-journal categories that drive the booking form (operating expenses, salary payments, asset purchases, …). The catalog is global KLARA reference data: the same list is returned for every tenant, so there are no tenant or company path parameters. The `Accept-Language` header selects which translation of the human-readable `display` label is returned; if absent or unsupported the German translation is used as a fallback and the full per-language `i18n` / `keywordI18ns` maps are returned for client-side rendering. Optional `orderColumns` / `offset` / `limit` query parameters are forwarded verbatim to the downstream catalog for ordering and pagination. The endpoint is a pure read — idempotent, no side effects. Requires a bearer token whose principal carries the `ACCOUNTING_GET_BUSINESS_CASE_TEMPLATE` permission.

- operationId: `listBusinessCaseTemplatesV1`
- Required permission: `ACCOUNTING_GET_BUSINESS_CASE_TEMPLATE`
- Roles that carry it: Administrator, Buchhalter, External user, Mitarbeiter und Lohn

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `limit` | query | no | integer | Maximum number of templates to return. When omitted, the downstream service returns the full catalog. |
| `offset` | query | no | integer | Zero-based index of the first template to return. When omitted, the response starts at the first row. |
| `orderColumns` | query | no | array | Names of columns to order the result by, ascending. Repeat the query parameter for multi-column ordering (e.g. `?orderColumns=group&orderColumns=code`). |
| `Accept-Language` | header | no | string | IETF language tag used to resolve the localized `display` label of each template. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. Falls back to the German translation when no translation exists for the requested language. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Business-case templates matching the supplied ordering and pagination. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/business-case-templates/{businessCaseTemplateId}

Read a single KLARA master accounting business-case template by id.

Returns one business-case template from the global KLARA catalog — a manual-journal category usable when entering a booking (for example operating expenses, salary payments, asset purchases). The catalog is global KLARA reference data, so this endpoint has no tenant or company path parameters; only the catalogue id selects the template. The `Accept-Language` header selects which translation of the human-readable `display` label is returned; if absent or unsupported the German translation is used as a fallback and the full per-language `i18n` / `keywordI18ns` maps are returned for client-side rendering. The endpoint is a pure read — idempotent, no side effects. Returns `404` when no template with the requested id exists in the catalog. Requires a bearer token whose principal carries the `ACCOUNTING_GET_BUSINESS_CASE_TEMPLATE` permission.

- operationId: `getBusinessCaseTemplateByIdV1`
- Required permission: `ACCOUNTING_GET_BUSINESS_CASE_TEMPLATE`
- Roles that carry it: Administrator, Buchhalter, External user, Mitarbeiter und Lohn

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `businessCaseTemplateId` | path | yes | integer (int64) | Numeric primary-key id of the business-case template in the KLARA master catalog. Obtain it from a prior call to `GET /core/v1/accounting/business-case-templates`. |
| `Accept-Language` | header | no | string | IETF language tag used to resolve the localized `display` label of the template. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. Falls back to the German translation when no translation exists for the requested language. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiBusinessCaseTemplate | Business-case template matching the supplied id. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | none declared | No business-case template carries the requested id. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/business-cases

Hydrate a business case from a template id for the authenticated company.

Loads the fully hydrated business-case aggregate the booking form needs: the template id, the company-VAT and fiscal-year context resolved from the optional date filters, the pre-translated display label, and the recursive snippet / field tree. When `dateForFilteringFiscalYear` is supplied it drives the fiscal-year resolution; otherwise `dateForFilteringCompanyVat` is used; otherwise the downstream service resolves the fiscal year that covers today. Tenant and company are derived from the bearer-token session — they are never accepted from the URL. The endpoint is a pure read — idempotent, no side effects. The caller must hold the `ACCOUNTING` permission on the company in scope.

- operationId: `hydrateBusinessCaseFromTemplateV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `businessCaseTemplateId` | query | yes | integer (int64) | Numeric id of the business-case template to hydrate. Obtain it from `GET /core/v1/accounting/companies/current/business-case-templates`. |
| `dateForFilteringCompanyVat` | query | no | string (date) | ISO date (`yyyy-MM-dd`) used to resolve the company-VAT regime applicable to the business case. Used as the fallback for `dateForFilteringFiscalYear` when that is omitted. |
| `dateForFilteringFiscalYear` | query | no | string (date) | ISO date (`yyyy-MM-dd`) used to resolve the fiscal year that hydrates the business case. When omitted, falls back to `dateForFilteringCompanyVat`; when both are omitted the downstream service resolves the fiscal year that covers today. |
| `Accept-Language` | header | no | string | IETF language tag used to translate the snippet labels and field captions. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiBusinessCase | Hydrated business-case aggregate for the requested template. |
| `400` | ErrorMessage | One of `dateForFilteringFiscalYear` / `dateForFilteringCompanyVat` is not a valid ISO date (`yyyy-MM-dd`), or the supplied `businessCaseTemplateId` is not valid for the company's legal form or VAT regime. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | ErrorMessage | No business-case template matches the supplied `businessCaseTemplateId` in the caller's company scope. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/business-cases/v2

Hydrate a business case from a template id, scoped to a single snippet.

Snippet-scoped variant of `GET /core/v1/accounting/business-cases`: hydrates only the template root plus the snippet identified by `snippet-id`, so the booking UI can stream the form one snippet at a time instead of loading the whole tree up-front. When `dateForFilteringFiscalYear` is supplied it drives the fiscal-year resolution; otherwise `dateForFilteringCompanyVat` is used; otherwise the downstream service resolves the fiscal year that covers today. When `group-insurance-by-insurer` is `true` (the default), social-insurance entries that share the same display + value pair are collapsed, keeping the entry with the smallest numeric id. Tenant and company are derived from the bearer-token session — they are never accepted from the URL. The endpoint is a pure read — idempotent, no side effects. The caller must hold the `ACCOUNTING` permission on the company in scope.

- operationId: `hydrateBusinessCaseFromTemplateForSnippetV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `businessCaseTemplateId` | query | yes | integer (int64) | Numeric id of the business-case template to hydrate. Obtain it from `GET /core/v1/accounting/companies/current/business-case-templates`. |
| `dateForFilteringCompanyVat` | query | no | string (date) | ISO date (`yyyy-MM-dd`) used to resolve the company-VAT regime applicable to the business case. Used as the fallback for `dateForFilteringFiscalYear` when that is omitted. |
| `dateForFilteringFiscalYear` | query | no | string (date) | ISO date (`yyyy-MM-dd`) used to resolve the fiscal year that hydrates the business case. When omitted, falls back to `dateForFilteringCompanyVat`; when both are omitted the downstream service resolves the fiscal year that covers today. |
| `group-insurance-by-insurer` | query | no | boolean | When `true`, collapses social-insurance entries that share the same display + value pair, keeping the entry with the smallest numeric id. Defaults to `true`. Allowed values: `true`, `false`. |
| `snippet-id` | query | no | integer (int64) | Numeric id of the snippet currently activated by the user; v2 hydrates only this snippet plus the template root. When omitted, only the template root is hydrated. |
| `snippet-value` | query | no | string | Currently ignored on the downstream side; reserved for a future optimisation that would load external entries only for the activated value (for example, payment dates of a single employee). Safe to omit. |
| `Accept-Language` | header | no | string | IETF language tag used to translate the snippet labels and field captions. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiBusinessCase | Hydrated business-case aggregate for the requested template and snippet. |
| `400` | ErrorMessage | One of `dateForFilteringFiscalYear` / `dateForFilteringCompanyVat` is not a valid ISO date (`yyyy-MM-dd`), or the supplied `businessCaseTemplateId` is not valid for the company's legal form or VAT regime. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | ErrorMessage | No business-case template matches the supplied `businessCaseTemplateId` in the caller's company scope. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/business-cases/{businessCaseId}

Read a persisted business-case instance by its id for the authenticated company.

Loads the fully hydrated business-case aggregate that was previously persisted for the authenticated company: the template id, the company-VAT and fiscal-year context that were in force when the case was booked, the pre-translated display label, the recursive snippet / field tree with the persisted scripted values overlaid, and the comma-separated list of booking numbers already generated for it. Tenant and company are derived from the bearer-token session — they are never accepted from the URL; the supplied `businessCaseId` is matched against the caller's company so that an id owned by another company surfaces as `404`. The endpoint is a pure read — idempotent, no side effects. The caller must hold the `ACCOUNTING` permission on the company in scope.

- operationId: `getBusinessCaseByIdV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `businessCaseId` | path | yes | integer (int64) | Numeric primary-key id of the persisted business-case instance to hydrate. Obtain it from a prior booking response (for example `POST /core/v1/accounting/bookings`) or from a journal listing. |
| `Accept-Language` | header | no | string | IETF language tag used to translate the snippet labels and field captions. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiBusinessCase | Hydrated business-case aggregate for the requested id. |
| `400` | ErrorMessage | The supplied `businessCaseId` is not a valid numeric id. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | ErrorMessage | No business case matches the supplied `businessCaseId` in the caller's company scope. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/companies/current

Get the accounting configuration of the authenticated company.

Returns the dunning waiting periods configured for the authenticated caller's company together with the canonical compensation-side company URI. Tenant and company are derived from the bearer token — there are no path or query parameters to override them. When the company has no accounting configuration row persisted yet, the downstream service synthesises a default record (`id` = `null`, `waitingDunningTime` = `5`, `dunningWaitingTimeLevelOne` / `Two` / `Three` = `10`) so the response shape stays the same. The endpoint is a pure read — idempotent, no side effects. The caller must hold the `ACCOUNTING` permission on the company in scope.

- operationId: `getCurrentAccountingCompanyV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiAccountingCompany | Accounting configuration of the caller's company. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | ErrorMessage | The company associated with the caller's session could not be resolved by the downstream accounting service. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/companies/current/business-case-templates

List business-case templates available to the authenticated company, grouped by financial-year period.

Returns the manual-journal categories ("business-case templates") the caller's company is allowed to book against, grouped by the financial-year period in which they are valid. Each period carries its `periodFrom` / `periodTo`, the company's `legalForm` and active VAT regime, and the templates that pass the legal-form / VAT-regime filters for that period. Templates are sorted by `code`; the `display` field is pre-translated using the `Accept-Language` header and the full per-language map is also returned in `i18n` / `keywordI18ns`. Tenant and company are derived from the bearer token — there are no path or query parameters to override them. The endpoint is a pure read — idempotent, no side effects. The caller must hold the `ACCOUNTING` permission on the company in scope.

- operationId: `listCurrentCompanyBusinessCaseTemplatesV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `Accept-Language` | header | no | string | IETF language tag used to translate the `display` field of each template. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Business-case templates grouped by financial-year period. May be an empty array when the company has no open fiscal year yet. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | ErrorMessage | The company associated with the caller's session could not be resolved by the downstream accounting service. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/companies/current/financial-years

List active financial-year periods of the authenticated company.

Returns the company's currently active fiscal-year periods (status `OPEN` or `CLOSING`), one entry per legal-form / VAT-regime combination valid in the period. When every period shares the same `legalForm` and VAT regime the downstream service collapses them into a single aggregated period with `periodFrom` set to the earliest start date and `periodTo` set to the latest end date; otherwise the individual periods are returned. The `businessCaseTemplates` field is intentionally left empty on this endpoint — use `GET /core/v1/accounting/companies/current/business-case-templates` to retrieve the templates valid per period. Tenant and company are derived from the bearer token — there are no path or query parameters to override them. The endpoint is a pure read — idempotent, no side effects. The caller must hold the `ACCOUNTING` permission on the company in scope.

- operationId: `listCurrentCompanyFinancialYearsV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `Accept-Language` | header | no | string | IETF language tag forwarded to the downstream accounting service. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Active financial-year periods of the caller's company. May be an empty array when the company has no open fiscal year yet. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | ErrorMessage | The company associated with the caller's session could not be resolved by the downstream accounting service. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/currencies/exchange-rate

Get the CHF exchange rate for a currency on a given date.

Returns the Swiss National Bank reference rate that converts one unit of `from-currency-code` into CHF on `exchange-date`. When `from-currency-code` equals `CHF` (case-insensitive) the rate is always `1`. The exchange-rate table is global SNB data — the same response is returned for every tenant, so there are no tenant or company path parameters. If no rate is stored for the requested day, the downstream service transparently fetches the SNB feed for that date and persists the result; if the feed has no entry either, the most recent rate strictly before `exchange-date` is returned instead. The endpoint is a pure read for the caller — idempotent. Requires a bearer token whose principal carries the `ACCOUNTING` permission.

- operationId: `getExchangeRateV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `exchange-date` | query | yes | string (date) | Reference date for the exchange rate, expressed as an ISO-8601 calendar date (`yyyy-MM-dd`). |
| `from-currency-code` | query | yes | string | ISO-4217 alpha-3 currency code of the source amount. Case-insensitive; `CHF` short-circuits to a rate of `1`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Exchange rate as a JSON number; the value `1` when `from-currency-code` is CHF. |
| `400` | ErrorMessage | `exchange-date` is not a valid ISO-8601 calendar date, or no rate could be resolved for the supplied `from-currency-code`. |
| `401` | none declared | No Authorization header found or invalid token |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/master-vats

List the master VAT catalog used by KLARA Accounting.

Returns the global master VAT catalog — the table of supported VAT rates with their validity ranges and translated labels. The downstream handler is a dispatcher driven by the query string: when `code` is non-blank only rows whose `vatCode` matches one of the semicolon-separated codes are returned (and when combined with `current-period=true`, only the ones valid today); otherwise when `default-vat=true` only the company-default rows are returned; otherwise the full catalog is returned, ordered by rate ascending, optionally paginated via `offset` / `limit`. Pagination is honoured only on the full-catalog branch — it is silently ignored when `code` or `default-vat` is set. The endpoint is a pure read and idempotent. Requires a bearer token whose principal carries the `ACCOUNTING_GET_MASTER_VAT` permission.

- operationId: `listMasterVatsV1`
- Required permission: `ACCOUNTING_GET_MASTER_VAT`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `code` | query | no | string | Semicolon-separated list of `vatCode`s to filter on. When set, switches the response to the by-code branch and ignores `default-vat`. |
| `current-period` | query | no | boolean | When combined with `code`, restricts the result to master VAT rows whose validity range covers today. Defaults to `false`. |
| `default-vat` | query | no | boolean | When `true`, restricts the result to the company-default master VAT rows. Defaults to `false`. Ignored when `code` is set. |
| `includeHiddenVatCase` | query | no | boolean | When `true`, the response also includes hidden VAT-case ids that are not directly mapped to a master VAT row. Defaults to `false`. |
| `limit` | query | no | integer | Maximum number of results to return. Only honoured on the full-catalog branch. |
| `offset` | query | no | integer | Zero-based index of the first result. Only honoured on the full-catalog branch (when neither `code` nor `default-vat` is set). |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Array of master VAT rows matching the filters. |
| `401` | none declared | No Authorization header found or invalid token |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/master-vats/current

List the master VAT rows valid for today.

Returns the subset of the master VAT catalog whose validity range (`validFrom` / `validTo`) covers the server's current day. Useful for booking and invoicing UIs that need only the VAT rates that may legally be applied right now. The endpoint is a pure read and idempotent; the downstream handler caches the filtered list in-process. Requires a bearer token whose principal carries the `ACCOUNTING_GET_MASTER_VAT` permission.

- operationId: `getCurrentMasterVatsV1`
- Required permission: `ACCOUNTING_GET_MASTER_VAT`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Array of master VAT rows valid for the current day. |
| `401` | none declared | No Authorization header found or invalid token |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/master-vats/{masterVatId}

Resolve a master VAT catalog row by its numeric id.

Loads a single master VAT row from KLARA's global master VAT catalog, identified by its numeric primary-key `masterVatId`. The catalog is global KLARA reference data — the same row is returned for every tenant, so there are no tenant or company path parameters. The endpoint is a pure read and idempotent. Requires a bearer token whose principal carries the `ACCOUNTING_GET_MASTER_VAT` permission.

- operationId: `getMasterVatByIdV1`
- Required permission: `ACCOUNTING_GET_MASTER_VAT`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `masterVatId` | path | yes | integer (int64) | Numeric primary-key id of the master VAT row to resolve. Obtain it from `GET /core/v1/accounting/master-vats` or `GET /core/v1/accounting/master-vats/current`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiMasterVat | The master VAT row matching `masterVatId`, including its rate, VAT code and validity range. |
| `401` | none declared | No Authorization header found or invalid token |
| `404` | ErrorMessage | No master VAT row exists for the supplied `masterVatId`. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/vat-profile

Get the authenticated company's VAT reporting profile.

Returns the company's VAT regime (effective vs net-tax-rate), billing method and flat net-tax rates, so an integration knows when the Saldosteuersatz flat-rate layer applies.

- operationId: `getVatProfileV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `date` | query | no | string | Date (ISO yyyy-MM-dd) to resolve the company VAT config for; defaults to today. Use the date you intend to book on — the company VAT must be valid for it. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiVatProfile | VAT profile. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/vat-suggestions

Suggest ready-to-use VAT case / rate / account bundles for a booking line.

Turns a plain-language context — a purchase vs a sale — into ranked, copy-paste-ready VAT bundles. Each bundle carries the verbatim `vatCaseLink` and `vatRateLink` to drop into a booking line's `links`, plus a suggested `vatAccountCode`. Supply `amount` to also receive a net/VAT preview. The bundle marked `recommended` is the server's best guess. Rebuilding the Manual-booking VAT UI from this response (read this if you are generating a UI). This endpoint returns a flat array, but the Manual-booking screen renders it as three linked dropdowns plus two toggles and an auto-computed amount. Map each control as follows:GUI controlDriven byHow to build itVAT on/off toggleclient-side state (not in this response)OFF → the line has no VAT: don't call this endpoint, add no VAT links/fields. ON → call this endpoint and show the dropdowns below.VAT case dropdown (e.g. "taxable supply")distinct `vatCaseCode` (label it with `label`)Group the array by `vatCaseCode`; one entry per distinct case.VAT rate dropdown (e.g. "8.1%")`vatRateDisplay`Cascades from the chosen case: the rows sharing the selected `vatCaseCode` are the rate options.VAT account dropdown (e.g. "1170 | Input tax VAT")`vatAccountCodes`List all `vatAccountCodes`; preselect `vatAccountCode`.including / excluding togglethe `amountKind` query param`including` = `GROSS_INCLUSIVE`; `excluding` = `NET_EXCLUSIVE`. Flipping it means re-calling this endpoint to refresh the preview.Debit / Credit amount preview`previewNet` / `previewVat`Null until you pass `amount`. Re-call with the new `amount`/`amountKind` to refresh.End-to-end recipe (call → render → book):User turns the VAT toggle ON. Call this endpoint with `direction` (PURCHASE/SALE), the line's `accountCode`, the user's `amount`, the `amountKind` from the including/excluding toggle, and the `date` you will book on.Render the dropdowns per the table above; preselect the row where `recommended = true`.When the user picks a row, send it to `POST /core/v1/bookings?autoCalculateVat=true`: on the VAT-bearing line set `links = "<vatCaseLink>,<vatRateLink>"` (both verbatim), `vatAccountCode = <vatAccountCode>`, your `amount`, and `isExcludeVatAmount` matching the `amountKind` you queried — then omit the VAT counterpart line; the server generates it (matching `previewNet`/`previewVat`). Pass the same `date` you will book on. Coverage: the response mirrors the booking GUI's two dropdowns. The VAT-case list is direction-agnostic (every configured case is offered regardless of `direction`), and the rate list includes every configured version of each rate code the case references — current and historical — so a line dated before a rate change still finds its rate.

- operationId: `listVatSuggestionsV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `accountCode` | query | yes | integer (int32) | Main account being booked against (e.g. 1020); drives VAT-account resolution. Required. |
| `amount` | query | no | number | Amount to preview the net/VAT split for (optional). |
| `amountKind` | query | no | string | GROSS_INCLUSIVE (default) or NET_EXCLUSIVE. |
| `date` | query | no | string | Date (ISO yyyy-MM-dd) to resolve the company VAT config for; defaults to today. |
| `direction` | query | yes | string | PURCHASE (you are buying) or SALE (you are selling). Required. It does not restrict which VAT cases are returned (the full case list is always offered); it only selects the fallback VAT account — purchases vs sales — and the recommendation wording. |
| `vatCase` | query | no | string | VAT case code to restrict suggestions to a single case; returns all its rate/account bundles (optional). Any valid case code is accepted regardless of direction — e.g. a sales case such as TAXABLE_SUPPLY can be requested with direction=PURCHASE. |
| `Accept-Language` | header | no | string | Preferred language for the bundle labels. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Ranked VAT suggestions. |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/vat-types

List KLARA master VAT-type catalogue rows.

Returns every VAT-type row maintained by KLARA — the canonical VAT codes that can participate in a booking together with their posting formulas. The data is global — the same catalogue is returned for every tenant, so there are no tenant or company path parameters. The `Accept-Language` header selects which translation is used for the `description` and `vatTypeShortName` fields; if absent or unsupported, German is used as the fallback. The endpoint is a pure read — idempotent, no side effects. Any authenticated bearer token is accepted; the downstream service declares no role or permission requirement on this endpoint.

- operationId: `listVatTypesV1`
- Required permission: none, this endpoint is how a token is obtained

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `Accept-Language` | header | no | string | IETF language tag used to resolve the localized `description` and `vatTypeShortName` fields. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. When the header is missing or the language is unknown, the downstream service falls back to German. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Master VAT-type rows. Empty array when the catalogue has not been seeded yet. |
| `401` | none declared | No Authorization header found or invalid token |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/accounting/vat-types/{vatTypeId}

Resolve a KLARA master VAT-type row by its numeric id.

Loads a single VAT-type row from KLARA's global master VAT-type catalogue, identified by its numeric primary-key `vatTypeId`. The data is global — the same catalogue is shared across every tenant, so there are no tenant or company path parameters. The `Accept-Language` header selects which translation is used for the `description` and `vatTypeShortName` fields; if absent or unsupported, German is used as the fallback. The endpoint is a pure read — idempotent, no side effects. Any authenticated bearer token is accepted; the downstream service declares no role or permission requirement on this endpoint.

- operationId: `getVatTypeByIdV1`
- Required permission: none, this endpoint is how a token is obtained

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `vatTypeId` | path | yes | integer (int64) | Numeric primary-key id of the master VAT-type row to resolve. Obtain it from `GET /core/v1/accounting/vat-types`. |
| `Accept-Language` | header | no | string | IETF language tag used to resolve the localized `description` and `vatTypeShortName` fields. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. When the header is missing or the language is unknown, the downstream service falls back to German. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiVatType | The master VAT-type row matching `vatTypeId`, including its localized description, short name and posting formulas. |
| `401` | none declared | No Authorization header found or invalid token |
| `404` | ErrorMessage | No master VAT-type row exists for the supplied `vatTypeId`. |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/v1/bank-reconciliation/open-positions

List the bank-reconciliation open positions of the authenticated company.

Returns the list of open booking-detail positions (AR / AP invoices, payments, prepayments, credit notes, general-ledger entries) of the caller's company that match the supplied filters, enriched with partner-name display, business-case message id, computed open amount and a stable booking-detail URI. Used by the bank-reconciliation UI to populate the open-position picker for a selected bank or credit-card transaction. The `action` parameter selects the multi-condition combinator: `search` (default) is OR-of-conditions, `filter` is AND-of-conditions. Tenant and company are derived from the bearer-token session — they are never accepted from the URL. The endpoint is a pure read — idempotent, no side effects. The caller must hold the `ACCOUNTING_GET_OPEN_POSITION` permission on the company in scope.

- operationId: `listBankReconciliationOpenPositionsV1`
- Required permission: `ACCOUNTING_GET_OPEN_POSITION`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `action` | query | no | string | Multi-condition combinator. `search` (default) = OR-of-conditions; `filter` = AND-of-conditions. |
| `booking-type-codes` | query | no | string | Comma-separated list of booking-type codes to restrict to. Allowed values: `GENERAL_LEDGER`, `AP_INVOICE`, `AP_PAYMENT`, `AR_INVOICE`, `AR_PAYMENT`, `AP_PREPAYMENT`, `AP_PREPAYUSE`, `AR_PREPAYMENT`, `AR_PREPAYUSE`, `AR_CREDITNOTE`, `AR_CREDITUSE`, `AP_CREDITNOTE`, `AP_CREDITUSE`, `AMORTIZATION`, `SELLOFF_AMORTIZATION`, `DELIMIT`. |
| `crdr-type` | query | no | PublicApiCrDrType | Restrict to credit (`CR`) or debit (`DR`) postings. |
| `credit-card-not-reconciled` | query | no | boolean | When `true`, restricts to credit-card transactions still pending reconciliation. |
| `general-search` | query | no | string | Free-text search across description, partner name, document id and reference fields. Combined with the other filters using the mode selected by `action`. |
| `invoice-date` | query | no | string (date) | Exact-match invoice (document) date filter (ISO date `yyyy-MM-dd`). |
| `partner` | query | no | string | KLARA partner URI to filter by. Format: `/api/…/{tenantId}/companies/{companyId}/partners/{partnerId}`. Construct from the partner id returned by the public partner endpoints. |
| `payment-date` | query | no | string (date) | Exact-match payment date filter (ISO date `yyyy-MM-dd`). |
| `payment-date-from` | query | no | string (date) | Inclusive lower bound of the payment-date range filter (ISO date `yyyy-MM-dd`). |
| `payment-date-to` | query | no | string (date) | Inclusive upper bound of the payment-date range filter (ISO date `yyyy-MM-dd`). |
| `position-status` | query | no | string | Comma-separated list of open-position lifecycle statuses to restrict to. Allowed values: `OPEN`, `PARTLY_PAID`, `PAID`, `PARTLY_CLEARED`, `CLEARED`. |
| `tags` | query | no | string | Comma-separated list of tag names to filter by (free-form). |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | List of open positions matching the filters. Empty array when nothing matches. |
| `400` | ErrorMessage | One of the date filters is not a valid ISO date (`yyyy-MM-dd`), or one of the enum filters (`position-status`, `booking-type-codes`, `crdr-type`) carries a value that is not in the documented value set, or a free-text filter exceeds 256 characters. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### POST /core/v1/bookings

Create a new manual booking for the authenticated company.

Records a manual journal entry against the authenticated caller's company and returns the persisted booking with its server-assigned identifiers. The booking must contain at least two debit/credit lines whose totals balance — the downstream service rejects unbalanced postings. By default the `bookingDate` must fall inside an open fiscal year; set `confirmDontMindClosingFiscalYear` to `true` to accept a booking that targets a closing fiscal year. Set `ignore-unsubscripted-dates` to `true` to bypass the subscription-period guard that normally rejects bookings outside the active KLARA Accounting subscription window. Tenant and company are derived from the bearer token — the request body's `id` and `companyId` are ignored. The caller must hold the `ACCOUNTING` permission on the targeted company.

- operationId: `createBookingV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `autoCalculateVat` | query | no | boolean | When `true`, the server recalculates VAT like the Manual booking GUI: each VAT-bearing line sends a single gross/net `amount` + a `vat_rate` link + `vatAccountCode` + `isExcludeVatAmount` and omits its VAT counterpart line; the server derives the net/VAT split and generates the VAT line so the entry balances. When `false` (default), the caller must pre-split and pre-compute every line (current behaviour). Obtain valid VAT values from `GET /core/latest/vat-cases` and `GET /core/v1/accounting/master-vats`. |
| `confirmDontMindClosingFiscalYear` | query | no | boolean | When `true`, accept the booking even if its `bookingDate` falls in a closing fiscal year. Defaults to `false`. |
| `ignore-unsubscripted-dates` | query | no | boolean | When `true`, accept the booking even if its date falls outside the active KLARA Accounting subscription period. Defaults to `false`. |
| `Accept-Language` | header | no | string | Preferred language for any localized error messages produced downstream. Examples: de-CH, fr-CH, it-CH, en. Defaults to the tenant's configured language when omitted. |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | integer (int64) | no | Internal id of the booking. Assigned by the server on creation; omit when sending a new booking. |
| `companyId` | integer (int64) | no | Internal id of the company this booking belongs to. Filled by the server from the caller's session; ignored on input. |
| `documentDate` | string (date-time) | yes | Date printed on the underlying document, e.g. supplier invoice date (yyyy-MM-ddT00:00:00Z). |
| `bookingDate` | string (date-time) | yes | Effective accounting date of the booking; must fall inside an open fiscal year unless confirmDontMindClosingFiscalYear is set (yyyy-MM-ddT00:00:00Z). |
| `documentIds` | array | no | Ids of supporting documents (uploaded files / e-mails) attached to this booking. |
| `businessCase` | string | no | Free-text business case label shown on the journal. |
| `snippets` | array | no | Snippet identifiers applied to this booking, copied from the booking template. |
| `bookingStatus` | string | no | Status of the booking. Allowed values: DRAFT, BOOKED, CANCELLED. |
| `internalComment` | string | no | Internal comment, visible only to accounting users. |
| `businessCaseId` | string | no | Reference to the business-case document (workflow id) that produced this booking, when applicable. |
| `relatedBookingHeaderLinks` | array | no | URIs of bookings that are related to this one (e.g. payment ↔ invoice, original ↔ delimitation). |
| `bookingDetails` | array | yes | Debit and credit lines of the booking. Must contain at least two lines whose debit and credit totals balance. |
| `invoiceNumber` | string | no | Invoice number printed on the document. |
| `orderManagementInvoiceLink` | string | no | KLARA order-management invoice URI when the booking was generated from a KLARA invoice. |
| `bookingTemplateId` | integer (int64) | no | Id of the booking template the user picked when creating this entry. |
| `bookingTitle` | string | no | Short title of the booking shown in lists. |
| `totalAmount` | number | no | Sum of the absolute amounts of the booking lines, in the company main currency. |
| `bookingTypeCode` | string | no | Business meaning of the booking. Allowed values include GENERAL_LEDGER, AR_INVOICE, AP_INVOICE, AR_PAYMENT, AP_PAYMENT, AR_CREDIT_NOTE, AP_CREDIT_NOTE. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiBookingHeader | Booking created. |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### PUT /core/v1/bookings/{id}/documents

Replace the document attachments and document date of an existing booking.

Replaces the set of supporting-document identifiers and the document date stored on an existing accounting booking. The operation is a full replacement — pass an empty `documentIds` array to clear all existing attachments, or omit `documentDate` / set it to `null` to clear it. By default the operation fails when the booking's existing `bookingDate` falls outside the active KLARA Accounting subscription window; set `ignore-unsubscripted-dates` to `true` to bypass this guard. Tenant and company are derived from the bearer token — the booking `{id}` must belong to the caller's company. The caller must hold the `ACCOUNTING` permission on that company. The endpoint is idempotent: re-sending the same body produces the same state. Returns HTTP 200 with no body on success.

- operationId: `updateBookingDocumentsV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `id` | path | yes | integer (int64) | Internal id of the booking whose documents should be replaced. Obtain from the `id` field returned by `POST /core/v1/bookings`. |
| `ignore-unsubscripted-dates` | query | no | boolean | When `true`, accept the update even if the booking's `bookingDate` falls outside the active KLARA Accounting subscription period. Defaults to `false`. |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `documentDate` | string (date) | no | Date printed on the underlying paper document (e.g. supplier invoice date). When omitted or null, the booking's existing documentDate is cleared. |
| `documentIds` | array | no | Identifiers of supporting documents attached to the booking. Replaces the current set on the booking; pass an empty array to clear. Obtain each id by first uploading a file with POST /core/latest/companies/{company-id}/documents and then copying the returned documentId into this array. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | none declared | Document attachments and date applied to the booking (or accepted as a no-op when the booking does not exist on the downstream side). |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### POST /core/v1/vat-clearing-reports/{year}/{quarter}

Get or recompute the VAT clearing report of a company for a given period.

Returns the VAT clearing report of the caller's company for the requested calendar `year` and reporting `quarter` (quarter, semester or yearly). When `recalculate=true` the report is recomputed from the current bookings and the stored `CALCULATED` snapshot is rewritten before being returned; this is why the operation uses `POST` rather than `GET`. When `recalculate=false` the latest `CALCULATED` snapshot is returned as-is, or — if only a `SEALED` snapshot exists — that one. The caller must hold the `ACCOUNTING` permission on the targeted company.

- operationId: `getVatClearingReportByQuarterV1`
- Required permission: `ACCOUNTING`
- Roles that carry it: Administrator, Buchhalter, External user

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `quarter` | path | yes | string | Reporting period within the year. Allowed values: Q1, Q2, Q3, Q4, S1, S2, YEAR_NET_TAX_RATE, YEAR_EFFECTIVE. |
| `year` | path | yes | integer | Calendar year of the reporting period. |
| `recalculate` | query | no | boolean | If true, recompute the report from current bookings and overwrite the stored CALCULATED snapshot before returning. Defaults to false. |
| `Accept-Language` | header | no | string | Preferred language for localized labels in the response. Examples: de-CH, de, fr-CH, it-CH, en. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | VatClearingReport | The VAT clearing report. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | none declared | Resource not found |
| `429` | none declared | API rate limit exceeded |
| `500` | none declared | Something went wrong on our side while processing the request. Please kindly contact our support. |

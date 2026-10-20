# Accounting

29 operations of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | The supplied `legal-form` value does not match any known legal form. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | The company associated with the caller's session could not be resolved by the downstream accounting service, or the supplied `legal-form` value does not match any known legal form. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | No master account exists for the supplied `accountCode`, or the supplied `legal-form` value does not match any known legal form. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/accounting/business-case-templates

List KLARA master accounting business-case templates.

Returns the global catalog of business-case templates — the manual-journal categories that drive the booking form (operating expenses, salary payments, asset purchases, …). The catalog is global KLARA reference data: the same list is returned for every tenant, so there are no tenant or company path parameters. The `Accept-Language` header selects which translation of the human-readable `display` label is returned; if absent or unsupported the German translation is used as a fallback and the full per-language `i18n` / `keywordI18ns` maps are returned for client-side rendering. Optional `sort-field` (comma-separated column names) / `offset` / `limit` query parameters are forwarded to the downstream catalog for ordering and pagination. The endpoint is a pure read — idempotent, no side effects. Requires a bearer token whose principal carries the `ACCOUNTING_GET_BUSINESS_CASE_TEMPLATE` permission.

- operationId: `listBusinessCaseTemplatesV1`
- Required permission: `ACCOUNTING_GET_BUSINESS_CASE_TEMPLATE`
- Roles that carry it: Administrator, Buchhalter, External user, Mitarbeiter und Lohn

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `limit` | query | no | integer | Maximum number of templates to return. When omitted, the downstream service returns the full catalog. |
| `offset` | query | no | integer | Zero-based index of the first template to return. When omitted, the response starts at the first row. |
| `orderColumns` | query | no | array | Renamed to `sort-field`. This name is accepted until 2027-09-01; when both are sent, `sort-field` wins. After that date, a request sending `orderColumns` without `sort-field` is rejected with 400. Deprecated — use `sort-field` instead, which takes the columns as one comma-separated value rather than as a repeated parameter. Names of columns to order the result by, ascending. Repeat this query parameter for multi-column ordering. |
| `sort-field` | query | no | string | Comma-separated list of columns to order the result by, ascending (e.g. `?sort-field=group,code`). This catalog orders ascending only, so it publishes no `sort-direction`. |
| `Accept-Language` | header | no | string | IETF language tag used to resolve the localized `display` label of each template. Examples: `de-CH`, `fr-CH`, `it-CH`, `en`. Falls back to the German translation when no translation exists for the requested language. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Business-case templates matching the supplied ordering and pagination. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | No business-case template carries the requested id. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `400` | ErrorMessage | One of `dateForFilteringFiscalYear` / `dateForFilteringCompanyVat` is not a valid ISO date (`yyyy-MM-dd`), or the supplied `businessCaseTemplateId` is not valid for the company's legal form or VAT regime. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | No business-case template matches the supplied `businessCaseTemplateId` in the caller's company scope. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `400` | ErrorMessage | One of `dateForFilteringFiscalYear` / `dateForFilteringCompanyVat` is not a valid ISO date (`yyyy-MM-dd`), or the supplied `businessCaseTemplateId` is not valid for the company's legal form or VAT regime. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | No business-case template matches the supplied `businessCaseTemplateId` in the caller's company scope. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `400` | ErrorMessage | The supplied `businessCaseId` is not a valid numeric id. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | No business case matches the supplied `businessCaseId` in the caller's company scope. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | The company associated with the caller's session could not be resolved by the downstream accounting service. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | The company associated with the caller's session could not be resolved by the downstream accounting service. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | The company associated with the caller's session could not be resolved by the downstream accounting service. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `400` | ErrorMessage | `exchange-date` is not a valid ISO-8601 calendar date, or no rate could be resolved for the supplied `from-currency-code`. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | No master VAT row exists for the supplied `masterVatId`. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/accounting/tags

List the tags used on bookings.

Returns the tags that occur on the booking lines of the caller's company, the same tags KLARA suggests when a tag is entered in the journal, a booking template or a business case. All booking lines count, whatever the booking status and fiscal year; tags that exist only on a booking template or a business case are not included. Each tag is returned once; a booking line can carry several tags, which are listed separately. Spellings are kept as entered, so `Marketing` and `marketing` are two entries. A single tag never contains a comma, because the comma separates the tags of a booking line. With `include-combinations=true` the list also contains each combination of tags as stored on a booking line, joined with a comma and a space (for example `Marketing, Zurich`), which is what the KLARA entry screens suggest; a name containing a comma is such a combination. Use the names as `tags` of a booking line in `createBookingV1`, or as `tags` filter in `listBankReconciliationOpenPositionsV1`, so new bookings reuse the existing spelling; join several tags with a comma without a space (`Marketing,Zurich`). Unlike the generic description of `search-key`, this operation splits the search text into words and keeps the tags that contain every word, case-insensitive (`zur mark` finds `Marketing, Zurich`). Characters are compared as written: `Zurich` does not find `Zürich`. Uses the standard collection parameters: `search-key`, `offset` (default 0), `limit` (default 50, maximum 100), `sort-field` (only and default `NAME`) and `sort-direction` (default DESC, Z to A; pass `ASC` for alphabetical order). Names are compared ignoring case and by character code, so letters with umlauts or accents sort after `z`. The number of tags matching the filters, ignoring `offset` and `limit`, is returned in the `X-Total-Count` header; to read everything, repeat the call with `offset` increased by `limit` until `X-Total-Count` is reached. To count the tags only, call with `limit=1` and read `X-Total-Count`. This is a read-only, idempotent operation without side effects. Tenant and company are derived from the bearer token and are never accepted from the URL or the request. The caller must hold the `ACCOUNTING` permission on the company; otherwise the endpoint returns `403`.

- operationId: `listBookingTagsV1`

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `include-combinations` | query | no | boolean | Also return each combination of tags as stored on one booking line, joined with a comma and a space, as the KLARA entry screens suggest them. `true` or `false` (default), case-insensitive. |
| `limit` | query | no | integer | Page size. Defaults to `50`; hard cap `100`. Requests above the cap are rejected with `400`. |
| `offset` | query | no | integer | 0-based pagination offset — the number of rows to skip before the returned page. Defaults to `0`. Combine with `limit` to walk the full collection. |
| `search-key` | query | no | string | Free-text search. The fields searched are documented per endpoint. Matches are case-insensitive substring matches. Maximum length 256 characters. |
| `sort-direction` | query | no | string | Sort direction. Allowed values: `ASC`, `DESC`. Defaults to `DESC`. |
| `sort-field` | query | no | string | Field to order the result by. The accepted values are listed per endpoint; an unknown value is rejected with `400`. Each endpoint documents the default it applies, chosen so that paging with `offset` / `limit` is stable. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | One page of tags, ordered by name in `sort-direction` (default DESC, Z to A). Empty array when the company has no tagged booking lines or nothing matches `search-key`. |
| `400` | ErrorMessage | `sort-field` is not `NAME`, or a parameter is outside its bounds (`include-combinations` not true or false, `search-key` longer than 256 characters, `offset` negative, `limit` outside 1-100, `sort-direction` not ASC or DESC). Error codes for this response (the `code` field of the response body):`request.sort_field_invalid` — sort-field was not one of the values the operation documents. The message lists the accepted values.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | No master VAT-type row exists for the supplied `vatTypeId`. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `general-search` | query | no | string | Renamed to `search-key`. This name is accepted until 2027-09-01; when both are sent, `search-key` wins. After that date, a request sending `general-search` without `search-key` is rejected with 400. Deprecated — use `search-key` instead. Free-text search across description, partner name, document id and reference fields. |
| `invoice-date` | query | no | string (date) | Exact-match invoice (document) date filter (ISO date `yyyy-MM-dd`). |
| `partner` | query | no | string | KLARA partner URI to filter by. Format: `/api/…/{tenantId}/companies/{companyId}/partners/{partnerId}`. Construct from the partner id returned by the public partner endpoints. |
| `payment-date` | query | no | string (date) | Exact-match payment date filter (ISO date `yyyy-MM-dd`). |
| `payment-date-from` | query | no | string (date) | Inclusive lower bound of the payment-date range filter (ISO date `yyyy-MM-dd`). |
| `payment-date-to` | query | no | string (date) | Inclusive upper bound of the payment-date range filter (ISO date `yyyy-MM-dd`). |
| `position-status` | query | no | string | Comma-separated list of open-position lifecycle statuses to restrict to. Allowed values: `OPEN`, `PARTLY_PAID`, `PAID`, `PARTLY_CLEARED`, `CLEARED`. |
| `search-key` | query | no | string | Free-text search across description, partner name, document id and reference fields. Combined with the other filters using the mode selected by `action`. |
| `tags` | query | no | string | Comma-separated list of tag names to filter by (free-form). |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | List of open positions matching the filters. Empty array when nothing matches. |
| `400` | ErrorMessage | One of the date filters is not a valid ISO date (`yyyy-MM-dd`), or one of the enum filters (`position-status`, `booking-type-codes`, `crdr-type`) carries a value that is not in the documented value set, or a free-text filter exceeds 256 characters. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/bank-transactions

List bank transactions that are not reconciled yet.

Returns the bank transactions of the caller's company that are not reconciled yet, as listed in KLARA under Accounting > Bank reconciliation. Only transactions of the company's bank accounts are returned, each with the `bankAccountId` from `listCompanyBankAccounts`; transactions of a bank account that no longer exists are left out, as in KLARA. To find the accounts of one bank, read `swissBank.name` in `listCompanyBankAccounts`. Credit card transactions are not included. A transaction counts as reconciled as soon as it is booked, also when it only partly settles an open item; reconciled transactions are never returned. An empty result for a payment reference therefore means either that no such payment arrived or that it is already reconciled; for the payment status of an invoice use `listBankReconciliationOpenPositionsV1` with `position-status`. Amounts are the full amounts of the bank entry and always positive; `direction` tells whether money was received (`CREDIT`) or paid out (`DEBIT`). `amount` is set for entries in CHF. For entries in any other currency, including WIR (`CHW`), `amount` is empty and the amount is in `foreignAmount` with the currency in `foreignCurrency`; no CHF equivalent is provided. There is no server-side total: to sum amounts, read all pages sorted by `ID` ascending, and keep in mind that transactions reconciled in the meantime drop out of the list. Use `paymentReference`, `remittanceText` and `counterpartyName` to find the matching open item, for example with `listBankReconciliationOpenPositionsV1` (use `action=filter` there so that all conditions apply; money received matches receivables, money paid out matches payables). Reconciling a transaction through the public API is not available yet; the `id` identifies the transaction for that future operation. Filter with `bank-account-id`, `booking-date-from` and `booking-date-to` (booking date, inclusive, `yyyy-MM-dd`) and `direction`; unlike the generic description of `search-key`, it matches counterparty name, remittance text, payment reference or additional information (contains, case-insensitive, spaces are not ignored). Sort with `sort-field` `BOOKING_DATE` (default), `VALUE_DATE`, `AMOUNT` or `ID`; ties are ordered by `ID`, empty values come last. Uses the standard collection parameters: `search-key`, `offset` (default 0), `limit` (default 50, maximum 100), `sort-field` and `sort-direction` (default DESC, newest first). The number of transactions matching the filters, ignoring `offset` and `limit`, is returned in the `X-Total-Count` header; to read everything, repeat the call with `offset` increased by `limit` until `X-Total-Count` is reached. This is a read-only, idempotent operation without side effects. Tenant and company are derived from the bearer token and are never accepted from the URL or the request. The caller must hold the `ACCOUNTING` permission on the company, and `FINANCE_BANK_ACCOUNT` to read the bank accounts (every role with `ACCOUNTING` holds it); otherwise the endpoint returns `403`.

- operationId: `listBankTransactionsV1`

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `bank-account-id` | query | no | integer (int64) | Only transactions of this company bank account, as returned by `listCompanyBankAccounts`. An id that is not a bank account of the company returns an empty list. |
| `booking-date-from` | query | no | string (date) | Only transactions booked on or after this date, ISO `yyyy-MM-dd`. |
| `booking-date-to` | query | no | string (date) | Only transactions booked on or before this date, ISO `yyyy-MM-dd`; not before `booking-date-from`. |
| `direction` | query | no | string | `CREDIT` for money received, `DEBIT` for money paid out; both when omitted. Case-insensitive. |
| `limit` | query | no | integer | Page size. Defaults to `50`; hard cap `100`. Requests above the cap are rejected with `400`. |
| `offset` | query | no | integer | 0-based pagination offset — the number of rows to skip before the returned page. Defaults to `0`. Combine with `limit` to walk the full collection. |
| `search-key` | query | no | string | Free-text search. The fields searched are documented per endpoint. Matches are case-insensitive substring matches. Maximum length 256 characters. |
| `sort-direction` | query | no | string | Sort direction. Allowed values: `ASC`, `DESC`. Defaults to `DESC`. |
| `sort-field` | query | no | string | Field to order the result by. The accepted values are listed per endpoint; an unknown value is rejected with `400`. Each endpoint documents the default it applies, chosen so that paging with `offset` / `limit` is stable. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | One page of transactions, ordered by `sort-field`. Empty array when the company has no bank account or no unreconciled transaction matches. |
| `400` | ErrorMessage | `booking-date-from` or `booking-date-to` is not an ISO date or the range is inverted, `sort-field` is not one of the documented values, or a parameter is outside its bounds (`bank-account-id` below 1, `direction` not CREDIT or DEBIT, `search-key` longer than 256 characters, `offset` negative, `limit` outside 1-100, `sort-direction` not ASC or DESC). Error codes for this response (the `code` field of the response body):`request.sort_field_invalid` — sort-field was not one of the values the operation documents. The message lists the accepted values.`banktransaction.booking_date_invalid` — booking-date-from or booking-date-to was not an ISO date of the form yyyy-MM-dd, or booking-date-to was before booking-date-from.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/bookings

Search the journal: list bookings by text, booking date, amount or tag.

Returns the bookings of the caller's company, as listed in KLARA under Accounting > Journal, newest first (booking date, then id, both descending). Each entry is the booking header: `title` (the text of the first booking line), `totalAmount` (sum of the credit lines), dates, document ids and internal comment. The debit and credit lines are not part of this list; read them per booking with `getBookingV1` and the `id`. The list covers all fiscal years and every booking status (planned, booked, locked, sealed, inactive), like the journal; filter by booking date for one year, and use `listCurrentCompanyFinancialYearsV1` when a request means a fiscal year. A booking that consists only of delimitation lines is not listed, and delimitation lines never satisfy a filter. `search-key` is split into words at spaces and keeps the bookings in which any word occurs (contains, case-insensitive, no umlaut or accent folding, so `Miete` also finds `Mieter`) in the description of a booking line (the title is the first one) or in the stored internal comment (HTML markup included). A word that is a number also finds bookings with exactly that account code on a line, the booking with that id, and bookings linked to that booking; there is no filter for an account code alone. `booking-date-from` and `booking-date-to` filter by booking date (inclusive, `yyyy-MM-dd`). `amount-from` and `amount-to` keep bookings with at least one line whose amount is in the range (both inclusive; for more than 1000 send `amount-from=1000.01`); this is the line amount, not `totalAmount`. `tags` takes a comma-separated list and keeps bookings with a line that carries any of the tags (contains, case-insensitive: `Marketing` also finds `Online-Marketing`; there is no exact match); take the names from `listBookingTagsV1`. All conditions are checked per line: a booking is listed if one of its lines meets the amount and tag conditions and the search matches that line or the booking header. Uses the standard collection parameters: `search-key`, `offset` (default 0), `limit` (default 50, maximum 100); the order is fixed, `sort-field` accepts only `BOOKING_DATE` and `sort-direction` only `DESC` (`ASC` answers 400). The number of bookings matching the filters, ignoring `offset` and `limit`, is returned in the `X-Total-Count` header; to only count, call with `limit=1`. To read everything, repeat the call with `offset` increased by `limit` until `X-Total-Count` is reached; new bookings appear at the start and shift later pages, so fix `booking-date-to` for a stable export and remove duplicates by `id`. This is a read-only, idempotent operation without side effects. Tenant and company are derived from the bearer token and are never accepted from the URL or the request. The caller must hold the `ACCOUNTING` permission on the company; otherwise the endpoint returns `403`.

- operationId: `listBookingsV1`

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `amount-from` | query | no | number | Only bookings with at least one line whose amount is at least this value (CHF, line amounts are always positive). Applies to a line, not to the booking amount. |
| `amount-to` | query | no | number | Only bookings with at least one line whose amount is at most this value; not below `amount-from`. Applies to the same line as `amount-from`. |
| `booking-date-from` | query | no | string (date) | Only bookings with a booking date on or after this date, ISO `yyyy-MM-dd`. |
| `booking-date-to` | query | no | string (date) | Only bookings with a booking date on or before this date, ISO `yyyy-MM-dd`; not before `booking-date-from`. |
| `limit` | query | no | integer | Page size. Defaults to `50`; hard cap `100`. Requests above the cap are rejected with `400`. |
| `offset` | query | no | integer | 0-based pagination offset — the number of rows to skip before the returned page. Defaults to `0`. Combine with `limit` to walk the full collection. |
| `search-key` | query | no | string | Free-text search. The fields searched are documented per endpoint. Matches are case-insensitive substring matches. Maximum length 256 characters. |
| `sort-direction` | query | no | string | Sort direction. Allowed values: `ASC`, `DESC`. Defaults to `DESC`. |
| `sort-field` | query | no | string | Field to order the result by. The accepted values are listed per endpoint; an unknown value is rejected with `400`. Each endpoint documents the default it applies, chosen so that paging with `offset` / `limit` is stable. |
| `tags` | query | no | string | Comma-separated tags; keeps bookings with a line carrying any of them (contains, case-insensitive). Tag names come from `listBookingTagsV1`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | One page of bookings, newest first. Empty array when no booking matches. |
| `400` | ErrorMessage | `booking-date-from` or `booking-date-to` is not an ISO date or the range is inverted, `amount-to` is below `amount-from`, `sort-field` is not `BOOKING_DATE`, `sort-direction` is `ASC`, or a parameter is outside its bounds (`amount-from` or `amount-to` not a decimal number, `search-key` or `tags` longer than 256 characters, `offset` negative, `limit` outside 1-100, `sort-direction` not ASC or DESC). Error codes for this response (the `code` field of the response body):`request.sort_field_invalid` — sort-field was not one of the values the operation documents. The message lists the accepted values.`request.sort_direction_unsupported` — sort-direction=ASC was sent to an operation whose order is fixed; only DESC is available.`booking.booking_date_invalid` — booking-date-from or booking-date-to was not an ISO date of the form yyyy-MM-dd, or booking-date-to was before booking-date-from.`booking.amount_range_invalid` — amount-to was below amount-from.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `businessCaseId` | string | no | Id of the KLARA business case that produced this booking. Set by the server and ignored on input: a booking created through this API never belongs to a business case, so leave it empty. This is not the business case template code (use businessCase for a label). A non-numeric value is rejected with 400 booking.business_case_id_invalid. |
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
| `400` | ErrorMessage | Data invalid Error codes for this response (the `code` field of the response body):`booking.status_not_allowed` — bookingStatus was neither BOOKED nor PLANNED.`booking.business_case_id_invalid` — businessCaseId was set but is not a numeric business case id. Leave it empty when creating a booking; use businessCase for a label.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Error codes for this response (the `code` field of the response body):`auth.credentials_missing` — Neither an Authorization: Bearer header nor an API key header was present on the request.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/bookings/{bookingId}

Read one booking with all its debit and credit lines.

Returns one booking of the caller's company with all its lines, as shown when a booking is expanded in KLARA under Accounting > Journal. Take the `bookingId` from `listBookingsV1` or from `connectedLines`; booking ids are unique within the tenant. The header carries the same `title` and `totalAmount` as in `listBookingsV1` (text of the line with position 1, sum of all credit lines). `lines` holds every line in the order KLARA shows them, including VAT lines (separate lines on the VAT account) and delimitation lines (`bookingType` `DELIMIT`); a booking that consists only of delimitation lines can be read here although `listBookingsV1` does not list it. Each line has its account (number and name), `side` (`DEBIT` or `CREDIT`) and `amount` in CHF (always positive), the posting text, tags, the kind of line (`bookingType`), the VAT rate, a foreign currency amount, the open-item state of invoice and payment lines with due date, paid date and `overdue`, the service period, and who changed the line last (`lastModifiedBy`: an e-mail address, an API key name or `SYSTEM`; `lastModifiedAt` in UTC; a line never changed shows its creation). `references` lists the objects a line refers to, with type, id and the name KLARA shows now: customers and suppliers (same ids as in `listCustomersV1`; the partner of an invoice or payment is on its `AR_*` or `AP_*` line), company bank accounts (`listCompanyBankAccounts`), bank transactions, VAT cases, invoices, payments, employees, social insurances, salary runs, tax-at-source cantons, KLARA's built-in gift-card and miscellaneous partner entries, and sub-accounts of the chart of accounts (with `category`); a name is missing when it could not be resolved. `connectedLines` lists the lines of other bookings a line is connected to: `RECONCILIATION` (a payment or bank reconciliation against an open item; listed on every line involved, so the invoice line lists each payment line and the payment line each invoice line), `DELIMITATION` (accrual), `PREPAYMENT_CLEARING`, `CREDIT_NOTE_CLEARING` and `MAIN_LINE` (the line this automatically created line was derived from, for example a VAT line booked with a payment that points to the revenue line of the invoice); read the connected booking with this operation and its `bookingId`. Not returned: the booking status (planned and inactive bookings are returned like booked ones, as the journal shows them), the amount still open on an open item (use `listCustomerOpenPositionsV1`), the VAT amount of a line, the amount a single reconciliation settled, and who changed the booking header (date, comment, documents). Reading the lines of many bookings takes one call per booking. Empty values and empty lists are left out. Account names and reference names follow `Accept-Language`. Returns `404` with code `booking.unknown` when no booking with this id exists in the caller's company; a booking of another company is answered the same way, to read it a token for that company is needed. A `bookingId` that is not a number also answers `404` (no such path). This is a read-only, idempotent operation without side effects. Tenant and company are derived from the bearer token and are never accepted from the URL or the request. The caller must hold the `ACCOUNTING` permission on the company; otherwise the endpoint returns `403`; a `403` never means that the booking exists.

- operationId: `getBookingV1`

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `bookingId` | path | yes | integer (int64) | Id of the booking, from the `id` of `listBookingsV1`. |
| `Accept-Language` | header | no | string | Language of account names and reference names, as in KLARA (for example `de-CH`, `fr-CH`, `it-CH`, `en`); default the tenant's language. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicApiBookingWithLines | The booking with all its lines. |
| `400` | ErrorMessage | `bookingId` is below 1. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | No booking with this id exists in the caller's company, or `bookingId` is not a number. Error codes for this response (the `code` field of the response body):`booking.unknown` — No booking with this id exists in the caller's company.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/customers/{customerId}/open-positions

List the open items of one customer or supplier.

Returns the open receivables (`position-type=RECEIVABLE`, default) or open payables (`position-type=PAYABLE`) of one customer or supplier of the caller's company, as shown in KLARA under Accounting > Open positions. Each item carries the amount still open at `reference-date` (default: today) and its history: when it was booked, which payments, credit notes or prepayments were reconciled against it, which payment reminders were sent and when the next one is planned (receivables), or when the next payment is due (payables). Obtain `customerId` from `listCustomersV1`; suppliers are customers in KLARA and use the same id. An unknown id, an id of another company, or a partner without open items returns an empty list, so check the id with `listCustomersV1` if the list is unexpectedly empty. To cover all customers or suppliers, page through `listCustomersV1` and call this operation once per customer. Items that reduce the balance (credit notes and prepayments) carry a negative `openAmount` and `totalAmount`, so the sum of `openAmount` is the net amount still owed. `search-key` matches the posting text (contains, case-insensitive) or, when the whole text is a date of the form `dd.MM.yyyy`, the due date. Sort with `sort-field` `ID` (default), `DUE_DATE` or `OPEN_AMOUNT`; items without a value come last. Uses the standard collection parameters: `search-key`, `offset` (default 0), `limit` (default 50, maximum 100), `sort-field` and `sort-direction` (default DESC). The number of rows matching the filters, ignoring `offset` and `limit`, is returned in the `X-Total-Count` header; to read everything, repeat the call with `offset` increased by `limit` until `X-Total-Count` is reached. Do not use `listBankReconciliationOpenPositionsV1` for this purpose: it lists candidates for reconciling a bank or credit-card transaction, without reference date and without history. This is a read-only, idempotent operation without side effects. Tenant and company are derived from the bearer token and are never accepted from the URL or the request. The caller must hold the `ACCOUNTING` permission on the company; otherwise the endpoint returns `403`.

- operationId: `listCustomerOpenPositionsV1`

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `customerId` | path | yes | integer (int64) | Id of the customer or supplier, as returned by `listCustomersV1`. |
| `limit` | query | no | integer | Page size. Defaults to `50`; hard cap `100`. Requests above the cap are rejected with `400`. |
| `offset` | query | no | integer | 0-based pagination offset — the number of rows to skip before the returned page. Defaults to `0`. Combine with `limit` to walk the full collection. |
| `position-type` | query | no | string | `RECEIVABLE` for the amounts the customer owes the company (default), `PAYABLE` for the amounts the company owes the supplier. Case-insensitive. |
| `reference-date` | query | no | string (date) | Date at which the items must be open, ISO `yyyy-MM-dd`. Items booked after this date are left out and amounts paid after it count as open. Defaults to today (Europe/Zurich). Future dates are accepted. |
| `search-key` | query | no | string | Free-text search. The fields searched are documented per endpoint. Matches are case-insensitive substring matches. Maximum length 256 characters. |
| `sort-direction` | query | no | string | Sort direction. Allowed values: `ASC`, `DESC`. Defaults to `DESC`. |
| `sort-field` | query | no | string | Field to order the result by. The accepted values are listed per endpoint; an unknown value is rejected with `400`. Each endpoint documents the default it applies, chosen so that paging with `offset` / `limit` is stable. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | One page of open items, ordered by `sort-field`. Empty array when the customer has no open items or the id is unknown. |
| `400` | ErrorMessage | `reference-date` is not an ISO date, `sort-field` is not one of the documented values, or a parameter is outside its bounds (`customerId` below 1, `position-type` not RECEIVABLE or PAYABLE, `search-key` longer than 256 characters, `offset` negative, `limit` outside 1-100, `sort-direction` not ASC or DESC). Error codes for this response (the `code` field of the response body):`openposition.reference_date_invalid` — reference-date was not an ISO date of the form yyyy-MM-dd. Omit it to use today.`request.sort_field_invalid` — sort-field was not one of the values the operation documents. The message lists the accepted values.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

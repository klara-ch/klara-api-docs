# Changelog

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#changelog

## Changelog

What changed and when. Changes that can break an existing integration are marked as such.

### API changes

#### 18 September 2026 · 1 new · 97 changed

#### New

- GET `/core/v1/invoices` — Search and page the invoices of the authenticated company.

#### Changed

- GET `/core/latest/article-categories` — new parameter `query:offset`; new parameter `query:search-key`; response body now documented for 401 (ErrorMessage), 429 (ErrorMessage)

- GET `/core/latest/article-filters` — new parameter `query:offset`; new parameter `query:search-key`; response body now documented for 401 (ErrorMessage), 429 (ErrorMessage)

- **33 endpoints** — response body now documented for 401 (ErrorMessage), 429 (ErrorMessage)Show the endpointsGET `/core/latest/articles`

- GET `/core/latest/articles/article-and-variants`

- GET `/core/latest/articles/{article-id}`

- GET `/core/latest/articles/{article-id}/article-set-items`

- GET `/core/latest/articles/{article-id}/variants`

- GET `/core/latest/articles/{article-id}/variants/{variant-id}`

- GET `/core/latest/company-configuration/including-vat`

- GET `/core/v1/accounting/accounts`

- GET `/core/v1/accounting/accounts/account-displaying`

- GET `/core/v1/accounting/accounts/by-code/{accountCode}`

- GET `/core/v1/accounting/booking-types`

- GET `/core/v1/accounting/business-cases`

- GET `/core/v1/accounting/business-cases/v2`

- GET `/core/v1/accounting/business-cases/{businessCaseId}`

- GET `/core/v1/accounting/companies/current`

- GET `/core/v1/accounting/companies/current/business-case-templates`

- GET `/core/v1/accounting/companies/current/financial-years`

- GET `/core/v1/accounting/currencies/exchange-rate`

- GET `/core/v1/accounting/master-vats`

- GET `/core/v1/accounting/master-vats/current`

- GET `/core/v1/accounting/master-vats/{masterVatId}`

- GET `/core/v1/accounting/vat-types`

- GET `/core/v1/accounting/vat-types/{vatTypeId}`

- GET `/core/v1/order-documents/{order-type}/next-document-number`

- GET `/core/v1/order-numbering-configurations`

- GET `/core/v1/orders/next-order-number`

- POST `/core/latest/article-categories`

- POST `/core/latest/article-filters`

- POST `/core/latest/articles`

- POST `/core/latest/articles/bulk`

- POST `/core/v1/bookings`

- PUT `/core/latest/articles/{article-id}`

- PUT `/core/v1/bookings/{id}/documents`

**25 endpoints** — response body now documented for 401 (ErrorMessage), 404 (ErrorMessage), 429 (ErrorMessage)Show the endpoints

- DELETE `/core/latest/article-categories/{category-id}`

- DELETE `/core/latest/article-filters/{filter-id}`

- DELETE `/core/latest/customers/{customer-id}/additional-addresses/{address-id}`

- DELETE `/core/latest/customers/{customer-id}/contacts/{contact-id}`

- GET `/core/latest/article-categories/{category-id}`

- GET `/core/latest/article-filters/{filter-id}`

- GET `/core/latest/cities/{city-id}`

- GET `/core/latest/customers`

- GET `/core/latest/customers/{customer-id}`

- GET `/core/latest/customers/{customer-id}/additional-addresses`

- GET `/core/latest/customers/{customer-id}/contacts`

- GET `/core/v1/accounting/business-case-templates/{businessCaseTemplateId}`

- GET `/core/v1/customers`

- GET `/core/v1/invoices/{id}`

- POST `/core/latest/article-categories/{category-id}/assign-to-articles`

- POST `/core/latest/article-filters/{filter-id}/assign-to-articles`

- POST `/core/latest/customers`

- POST `/core/latest/customers/{customer-id}/additional-addresses`

- POST `/core/latest/customers/{customer-id}/contacts`

- POST `/core/v1/invoices/{id}/send`

- PUT `/core/latest/article-categories/{category-id}`

- PUT `/core/latest/article-filters/{filter-id}`

- PUT `/core/latest/customers/{customer-id}`

- PUT `/core/latest/customers/{customer-id}/additional-addresses/{address-id}`

- PUT `/core/latest/customers/{customer-id}/contacts/{contact-id}`

**6 endpoints** — response body now documented for 401 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage)Show the endpoints

- DELETE `/core/latest/articles/{article-id}`

- DELETE `/core/latest/articles/{article-id}/images/{image-id}`

- GET `/core/latest/articles/article-numbers`

- GET `/core/latest/articles/{article-id}/images/{image-id}`

- GET `/core/v1/order-documents/filter/type-and-order-number`

- POST `/core/latest/payroll-interface-file`

POST `/core/latest/articles/{article-id}/images` — response body now documented for 401 (ErrorMessage), 415 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage) PUT `/core/latest/articles/{article-id}/images/{image-id}` — response body now documented for 401 (ErrorMessage), 415 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage) GET `/core/latest/articles/search` — new parameter `query:search-key`; response body now documented for 401 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage) POST `/core/latest/companies/{company-id}/documents` — response body now documented for 401 (ErrorMessage), 415 (ErrorMessage), 429 (ErrorMessage); response schema renamed, 400: `ErrorMessage1` to `ErrorMessage`; 403: `ErrorMessage1` to `ErrorMessage`; 413: `ErrorMessage1` to `ErrorMessage`; 500: `ErrorMessage1` to `ErrorMessage`. Generated client type names change with it **5 endpoints** — response body now documented for 401 (ErrorMessage)Show the endpoints

- GET `/core/latest/company-vats`

- GET `/core/latest/vat-cases`

- GET `/core/latest/vat-cases/{vat-case-id}`

- GET `/core/v2/tenants/individuals/profile`

- POST `/core/v2/tenants/individuals/profile`

DELETE `/core/latest/customers/{customer-id}` — response body now documented for 401 (ErrorMessage), 404 (ErrorMessage), 429 (ErrorMessage); response schema renamed, 400: `ErrorResponse` to `ErrorMessage`. Generated client type names change with it POST `/core/latest/generic-token` — response body now documented for 400 (ErrorMessage), 401 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage) POST `/core/latest/token` — response body now documented for 400 (ErrorMessage), 401 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage) POST `/core/latest/token/by-microsoft` — response body now documented for 400 (ErrorMessage), 401 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage) **5 endpoints** — response body now documented for 429 (ErrorMessage)Show the endpoints

- GET `/core/v2/tenants/companies`

- POST `/core/latest/subscriptions`

- POST `/core/latest/tenants`

- POST `/core/v2/tenants/companies`

- PUT `/core/v2/tenants/companies`

GET `/core/v1/accounting/business-case-templates` — new parameter `query:sort-field`; response body now documented for 401 (ErrorMessage), 429 (ErrorMessage) GET `/core/v1/bank-reconciliation/open-positions` — new parameter `query:search-key`; response body now documented for 401 (ErrorMessage), 429 (ErrorMessage) GET `/core/v1/accounting/vat-profile` — new status codes: 429; response body now documented for 401 (ErrorMessage) GET `/core/v1/accounting/vat-suggestions` — new status codes: 429; response body now documented for 401 (ErrorMessage) **6 endpoints** — response body now documented for 401 (ErrorMessage), 404 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage)Show the endpoints

- GET `/core/v1/company-bank-accounts`

- GET `/core/v1/invoices/next-invoice-number`

- POST `/core/v1/invoices`

- POST `/core/v1/invoices/{id}/printed-document`

- POST `/core/v1/vat-clearing-reports/{year}/{quarter}`

- PUT `/core/v1/invoices`

GET `/core/v1/employees/short-info` — new parameter `query:status`; response body now documented for 401 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage); response schema renamed, 400: `ErrorMessage1` to `ErrorMessage`; 403: `ErrorMessage1` to `ErrorMessage`; 404: `ErrorMessage1` to `ErrorMessage`. Generated client type names change with it POST `/core/v1/payroll/contracts/{contractId}/payslips/{payslipId}/salary-items` — response body now documented for 401 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage); response schema renamed, 400: `ErrorMessage1` to `ErrorMessage`; 403: `ErrorMessage1` to `ErrorMessage`; 404: `ErrorMessage1` to `ErrorMessage`. Generated client type names change with it GET `/core/v1/payroll/employees/{employeeId}/addable-salary-items` — response body now documented for 401 (ErrorMessage), 429 (ErrorMessage), 500 (ErrorMessage); response schema renamed, 400: `ErrorMessage1` to `ErrorMessage`; 403: `ErrorMessage1` to `ErrorMessage`; 404: `ErrorMessage1` to `ErrorMessage`. Generated client type names change with it

#### 7 September 2026 · 7 changed

#### Changed

- **7 endpoints** — new status codes: 404Show the endpointsGET `/core/latest/customers`

- GET `/core/latest/customers/{customer-id}/additional-addresses`

- GET `/core/latest/customers/{customer-id}/contacts`

- GET `/core/v1/customers`

- POST `/core/latest/customers`

- POST `/core/latest/customers/{customer-id}/additional-addresses`

- POST `/core/latest/customers/{customer-id}/contacts`

#### 31 August 2026 · 72 changed

#### Changed

- **68 endpoints** — operationId added, `…`. A generated client used to invent a method name from the path and will now use this idShow the endpointsDELETE `/core/latest/article-categories/{category-id}`

- DELETE `/core/latest/article-filters/{filter-id}`

- DELETE `/core/latest/articles/{article-id}`

- DELETE `/core/latest/articles/{article-id}/images/{image-id}`

- DELETE `/core/latest/customers/{customer-id}`

- GET `/core/latest/article-categories`

- GET `/core/latest/article-categories/{category-id}`

- GET `/core/latest/article-filters`

- GET `/core/latest/article-filters/{filter-id}`

- GET `/core/latest/articles`

- GET `/core/latest/articles/article-and-variants`

- GET `/core/latest/articles/article-numbers`

- GET `/core/latest/articles/search`

- GET `/core/latest/articles/{article-id}`

- GET `/core/latest/articles/{article-id}/article-set-items`

- GET `/core/latest/articles/{article-id}/images/{image-id}`

- GET `/core/latest/articles/{article-id}/variants`

- GET `/core/latest/articles/{article-id}/variants/{variant-id}`

- GET `/core/latest/cities/{city-id}`

- GET `/core/latest/company-configuration/including-vat`

- GET `/core/latest/company-vats`

- GET `/core/latest/customers`

- GET `/core/latest/customers/{customer-id}`

- GET `/core/latest/customers/{customer-id}/additional-addresses`

- GET `/core/latest/customers/{customer-id}/contacts`

- GET `/core/latest/vat-cases`

- GET `/core/latest/vat-cases/{vat-case-id}`

- GET `/core/v1/company-bank-accounts`

- GET `/core/v1/customers`

- GET `/core/v1/employees/short-info`

- GET `/core/v1/invoices/next-invoice-number`

- GET `/core/v1/invoices/{id}`

- GET `/core/v1/order-documents/filter/type-and-order-number`

- GET `/core/v1/order-documents/{order-type}/next-document-number`

- GET `/core/v1/order-numbering-configurations`

- GET `/core/v1/orders/next-order-number`

- GET `/core/v1/payroll/employees/{employeeId}/addable-salary-items`

- GET `/core/v2/tenants/companies`

- GET `/core/v2/tenants/individuals/profile`

- POST `/core/latest/article-categories`

- POST `/core/latest/article-categories/{category-id}/assign-to-articles`

- POST `/core/latest/article-filters`

- POST `/core/latest/article-filters/{filter-id}/assign-to-articles`

- POST `/core/latest/articles`

- POST `/core/latest/articles/bulk`

- POST `/core/latest/articles/{article-id}/images`

- POST `/core/latest/customers`

- POST `/core/latest/customers/{customer-id}/additional-addresses`

- POST `/core/latest/customers/{customer-id}/contacts`

- POST `/core/latest/generic-token`

- POST `/core/latest/payroll-interface-file`

- POST `/core/latest/subscriptions`

- POST `/core/latest/tenants`

- POST `/core/latest/token`

- POST `/core/latest/token/by-microsoft`

- POST `/core/v1/invoices`

- POST `/core/v1/invoices/{id}/printed-document`

- POST `/core/v1/invoices/{id}/send`

- POST `/core/v1/payroll/contracts/{contractId}/payslips/{payslipId}/salary-items`

- POST `/core/v2/tenants/companies`

- POST `/core/v2/tenants/individuals/profile`

- PUT `/core/latest/article-categories/{category-id}`

- PUT `/core/latest/article-filters/{filter-id}`

- PUT `/core/latest/articles/{article-id}`

- PUT `/core/latest/articles/{article-id}/images/{image-id}`

- PUT `/core/latest/customers/{customer-id}`

- PUT `/core/v1/invoices`

- PUT `/core/v2/tenants/companies`

PUT `/core/latest/customers/{customer-id}/additional-addresses/{address-id}` — operationId renamed from `update additional-addresses` to `updateCustomerAdditionalAddress`. Generated method names change with it DELETE `/core/latest/customers/{customer-id}/additional-addresses/{address-id}` — operationId renamed from `delete additional-address` to `deleteCustomerAdditionalAddress`. Generated method names change with it PUT `/core/latest/customers/{customer-id}/contacts/{contact-id}` — operationId renamed from `update CustomerContact` to `updateCustomerContact`. Generated method names change with it DELETE `/core/latest/customers/{customer-id}/contacts/{contact-id}` — operationId renamed from `delete CustomerContact` to `deleteCustomerContact`. Generated method names change with it

#### 28 August 2026 · 1 changed

#### Changed

- POST `/core/latest/generic-token` — moved from area Klara authentication generic to KLARA authentication generic

### Documentation changes

#### 18 September 2026 · Invoice list, one search pattern, sunset dates and error bodies

- **New: `GET /core/v1/invoices`** returns a page of invoices with free-text search, paging and sorting, so you no longer have to keep every invoice id in your own system. It needs the `FINANCE` permission. **Read the note on `status` before you build on it:** that parameter is the filter from the KLARA invoice overview, not an equality check on the field. `status=OPEN` returns rows that read `INVOICED` or `PAID`. Use `booking-status` to filter on the payment state exactly.

- **The superseded `/core/latest` accounting operations now carry a sunset date: 14 August 2027.** From the day after, they answer `410 Gone` instead of data. Each one names its `/core/v1` successor in the contract. Replacing the prefix remains the whole migration. See [Path prefixes and what is current](https://developer.klara.ch/#api-basics).

- **One search and sort pattern across the API.** `search-key`, `offset`, `limit`, `sort-field`, `sort-direction` and `status` are now the names everywhere. Nothing was renamed: the old names `keyword`, `general-search`, `orderColumns` and `filter-by-status` keep working and are marked deprecated. Send both and the new name wins.

- **Every error response now carries a documented body.** Until now 444 of them declared none. At the same time the duplicate schema `ErrorMessage1` is gone, and four operations that used it now answer with `ErrorMessage`. **If you generate a client, this is the one change that forces a regeneration:** the type name changes, the fields do not.

- The contract now names its own error codes in `x-error-codes`. There are three today, and the list is not exhaustive on purpose. See [What an error body contains](https://developer.klara.ch/#api-basics).

- **Pagination on article categories and article filters is fixed.** Both accept `offset` now and return the total in the `X-Total-Count` header. Until now they took `limit` without `offset`, so nothing beyond the first page was reachable.

- The specification names the real host `https://api.klara.ch` instead of `/`, and states in its own description that this is the only environment customers can reach.

#### 7 September 2026 · Roles for the document upload, and 404 on the customer endpoints

- **The document upload now names the roles that may call it.** `POST /core/latest/companies/{company-id}/documents` lists `COMPANY_ADMINISTRATOR`, `TRUSTED_USER`, `ACCOUNTANT`, `CUSTOMER_RELATIONSHIP_MANAGER`, `EMPLOYEE` and `MYKLARA_USER`, and one of them is enough. Until now this endpoint said the requirement was not established. The statement comes from the specification text rather than from the source analysis behind the other permission statements, and the endpoint says so.

- **Seven customer endpoints now declare `404`**: reading and creating customers under both `/core/latest` and `/core/v1`, plus their additional addresses and contacts. No response body, as with the other error statuses there. If you generate a client, it gains a `404` branch on those calls.

- Nothing was added or removed. 97 endpoints, same paths, same request and response shapes.

#### 31 August 2026 · Every endpoint now has a stable operationId

- **All 97 operations carry an `operationId`**, 68 of them for the first time. Until now a client generator had to invent method names from the path, and those names changed whenever a path changed. From now on the id is the name.

- **If you generate a client, expect method names to change once.** Where no `operationId` existed, your generator invented one; it will now use ours. Paths, parameters, request bodies and responses are untouched, so nothing changes on the wire. Regenerate, fix the names in your code, and you are done.

- Four ids that contained spaces and could not be used in generated code are corrected: `update additional-addresses` is now `updateCustomerAdditionalAddress`, `delete additional-address` is `deleteCustomerAdditionalAddress`, `update CustomerContact` is `updateCustomerContact`, and `delete CustomerContact` is `deleteCustomerContact`.

- The endpoint set is unchanged: 97 operations, same paths, same permissions.

#### 28 August 2026 · Machine-readable formats, error bodies, search, roles and superseded paths

- **This documentation is now available in machine-readable form.** [`llms.txt`](llms.txt) indexes every chapter as a Markdown file and every area of the reference as a Markdown file with parameters, request and response bodies, the required permission and the roles that carry it. Built in the same run as this page, from the same source. The specification itself is unchanged and remains the contract.

- The specification is now linked at the top of this page instead of only inside [API basics](https://developer.klara.ch/#api-basics).

- **The specification now spells the brand KLARA in capitals**, including one tag name that was `Klara authentication generic`. If your client generator groups methods by tag, that group is renamed. Paths, parameters and response shapes are untouched.

- The superseded `/core/latest` accounting and booking operations are excluded from the published specification, not just left undocumented. They still respond at the API. Each has an identical counterpart under `/core/v1`, and replacing the prefix is the whole migration. See [Path prefixes and what is current](https://developer.klara.ch/#api-basics).

- Error bodies on the authentication endpoints are now listed per endpoint and per status, because the general rule does not hold for them. Three of them declare no body for any error status. `POST /core/latest/tenants` declares `error` and `error_description` for `400`, `401`, `403` and `500`, and nothing for `429`. See [Authentication](https://developer.klara.ch/#authentication).

- **Free-text search and filtering by field are now separated**, because they are not the same thing. Free-text search exists on six endpoints. 32 endpoints filter by field, with 73 parameters in total, and there you have to know the value you are filtering on. Two endpoints accept a sort order. See [Searching and filtering](https://developer.klara.ch/#limits).

- **Correction, please check your key:** the permission summary of [Webshop order to customer and invoice](https://developer.klara.ch/#uc-webshop) was incomplete. It did not include the permissions of the invoicing steps that flow reuses. `ARTICLE_READ_ONLY` and `FINANCE_BANK_ACCOUNT` are now listed. A key provisioned from the earlier list is short of both.

- **Correction, the recommended starting role has changed.** The getting started guide recommended `Accountant`, which cannot read articles, so the first call of the guide answered `403`. The guide now recommends a role that covers every call it makes.

#### 24 August 2026 · Rate limit, error responses, pagination and API keys

- The rate limit is **at least 10,000 requests per minute**, counted per calling IP address, the same on every endpoint. The informational rate limit headers are suppressed at the gateway, so do not expect them. See [Limits](https://developer.klara.ch/#limits).

- `401` returns `ErrorMessage` even where the specification declares no body. `429` returns a body produced by the gateway rather than the application. There is no governed catalogue of error codes, so branch on the HTTP status and not on the code.

- Pagination has no total count and no guaranteed order, so a full read is a snapshot rather than a transaction. `GET /core/latest/article-categories` and `GET /core/latest/article-filters` accept `limit` without `offset`, so only the first page is reachable.

- API keys do not expire. You can revoke one yourself with immediate effect under **Benutzer**, and a last used timestamp is shown per key. The role of a key is fixed when it is created and cannot be changed later.

- Adding a salary item is **not idempotent**: a retry after a timeout appends a second item. `PUT` and `DELETE` do not exist on salary items yet, so corrections happen in the KLARA interface.

- New endpoints are versioned explicitly and start on `/core/v1`. A replaced endpoint stays available for **twelve months** after it is marked deprecated.

- **Correction to an earlier statement:** `External user` holds the same Public API permissions as `Administrator`. Choosing it instead of Administrator does not narrow what a key can reach.

#### 17 August 2026 · Initial publication

- 97 endpoints across accounting, finance, articles, customers, payroll and delivery. Accounting, finance and payroll are served under `/core/v1`, articles, customers and authentication under `/core/latest`.

- Every endpoint names the permission it requires and the roles that carry it.

- Eight integration flows with the calls they take and the permissions those calls need in total.

- The full specification is available for download, for generating clients and for contract testing.

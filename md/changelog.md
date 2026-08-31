# Changelog

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#changelog

## Changelog

What changed and when. Changes that can break an existing integration are marked as such.

### API changes

#### 31 August 2026

72 changed

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

#### 28 August 2026

1 changed

#### Changed

- POST `/core/latest/generic-token` — moved from area Klara authentication generic to KLARA authentication generic

### Documentation changes

#### 31 August 2026

Every endpoint now has a stable operationId

- **All 97 operations carry an `operationId`**, 68 of them for the first time. Until now a client generator had to invent method names from the path, and those names changed whenever a path changed. From now on the id is the name.

- **If you generate a client, expect method names to change once.** Where no `operationId` existed, your generator invented one; it will now use ours. Paths, parameters, request bodies and responses are untouched, so nothing changes on the wire. Regenerate, fix the names in your code, and you are done.

- Four ids that contained spaces and could not be used in generated code are corrected: `update additional-addresses` is now `updateCustomerAdditionalAddress`, `delete additional-address` is `deleteCustomerAdditionalAddress`, `update CustomerContact` is `updateCustomerContact`, and `delete CustomerContact` is `deleteCustomerContact`.

- The endpoint set is unchanged: 97 operations, same paths, same permissions.

#### 28 August 2026

Machine-readable formats, error bodies, search, roles and superseded paths

- **This documentation is now available in machine-readable form.** [`llms.txt`](llms.txt) indexes every chapter as a Markdown file and every area of the reference as a Markdown file with parameters, request and response bodies, the required permission and the roles that carry it. Built in the same run as this page, from the same source. The specification itself is unchanged and remains the contract.

- The specification is now linked at the top of this page instead of only inside [API basics](https://developer.klara.ch/#api-basics).

- **The specification now spells the brand KLARA in capitals**, including one tag name that was `Klara authentication generic`. If your client generator groups methods by tag, that group is renamed. Paths, parameters and response shapes are untouched.

- The superseded `/core/latest` accounting and booking operations are excluded from the published specification, not just left undocumented. They still respond at the API. Each has an identical counterpart under `/core/v1`, and replacing the prefix is the whole migration. See [Path prefixes and what is current](https://developer.klara.ch/#api-basics).

- Error bodies on the authentication endpoints are now listed per endpoint and per status, because the general rule does not hold for them. Three of them declare no body for any error status. `POST /core/latest/tenants` declares `error` and `error_description` for `400`, `401`, `403` and `500`, and nothing for `429`. See [Authentication](https://developer.klara.ch/#authentication).

- **Free-text search and filtering by field are now separated**, because they are not the same thing. Free-text search exists on six endpoints. 32 endpoints filter by field, with 73 parameters in total, and there you have to know the value you are filtering on. Two endpoints accept a sort order. See [Searching and filtering](https://developer.klara.ch/#limits).

- **Correction, please check your key:** the permission summary of [Webshop order to customer and invoice](https://developer.klara.ch/#uc-webshop) was incomplete. It did not include the permissions of the invoicing steps that flow reuses. `ARTICLE_READ_ONLY` and `FINANCE_BANK_ACCOUNT` are now listed. A key provisioned from the earlier list is short of both.

- **Correction, the recommended starting role has changed.** The getting started guide recommended `Accountant`, which cannot read articles, so the first call of the guide answered `403`. The guide now recommends a role that covers every call it makes.

#### 24 August 2026

Rate limit, error responses, pagination and API keys

- The rate limit is **at least 10,000 requests per minute**, counted per calling IP address, the same on every endpoint. The informational rate limit headers are suppressed at the gateway, so do not expect them. See [Limits](https://developer.klara.ch/#limits).

- `401` returns `ErrorMessage` even where the specification declares no body. `429` returns a body produced by the gateway rather than the application. There is no governed catalogue of error codes, so branch on the HTTP status and not on the code.

- Pagination has no total count and no guaranteed order, so a full read is a snapshot rather than a transaction. `GET /core/latest/article-categories` and `GET /core/latest/article-filters` accept `limit` without `offset`, so only the first page is reachable.

- API keys do not expire. You can revoke one yourself with immediate effect under **Benutzer**, and a last used timestamp is shown per key. The role of a key is fixed when it is created and cannot be changed later.

- Adding a salary item is **not idempotent**: a retry after a timeout appends a second item. `PUT` and `DELETE` do not exist on salary items yet, so corrections happen in the KLARA interface.

- New endpoints are versioned explicitly and start on `/core/v1`. A replaced endpoint stays available for **twelve months** after it is marked deprecated.

- **Correction to an earlier statement:** `External user` holds the same Public API permissions as `Administrator`. Choosing it instead of Administrator does not narrow what a key can reach.

#### 17 August 2026

Initial publication

- 97 endpoints across accounting, finance, articles, customers, payroll and delivery. Accounting, finance and payroll are served under `/core/v1`, articles, customers and authentication under `/core/latest`.

- Every endpoint names the permission it requires and the roles that carry it.

- Eight integration flows with the calls they take and the permissions those calls need in total.

- The full specification is available for download, for generating clients and for contract testing.

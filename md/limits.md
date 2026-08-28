# Limits, pagination and environments

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#limits

## Limits, pagination and environments

### Environments

There is one environment: `https://api.klara.ch`. It serves your live company data.

> **Warning: There is no test environment, and that is a decision**
>
> Every request you send acts on real data in your company. The product owner confirmed in August 2026 that no sandbox is planned for the Public API, so this is the intended model rather than a gap that will close. Plan for that:
>
> - **Explore with `GET` first.** Reading is safe. Get familiar with the data shapes before you write anything.
>
> - **Writes take effect immediately.** A created invoice is a real invoice, and a salary item added to a payslip is real payroll data.
>
> - **Use a separate key for development,** named so you can recognise and revoke it, for example `integration-dev`. Delete it when you go live.
>
> - **If you can, develop against a company you control** rather than a client's production company.

### Rate limiting

The API enforces a rate limit at the gateway. When you exceed it, the response is `429 API rate limit exceeded`. Nearly every endpoint can return it.

| What | Value |
|---|---|
| Limit | At least 10,000 requests per minute |
| Counted against | The calling **IP address**, not the API key and not the company |
| Scope | The same limit everywhere. File exports are not treated differently |

> **Warning: Two consequences of counting per IP address**
>
> Several customers behind one outbound address share a single budget. If you run a hosting platform or your own software as a service, one busy tenant can throttle the others.
>
> The reverse is also true: spreading calls across several source addresses multiplies your throughput. That works, but it is not a supported pattern and may change.

The figure is stated as a floor rather than an exact number on purpose. Each gateway instance counts on its own, so the effective ceiling rises with the number of instances and the counter resets when one restarts. Today one instance runs, which makes 10,000 per minute correct, but build against the floor.

### Headers on 429

The gateway **deliberately suppresses** the informational headers. You will not see `RateLimit-Limit`, `RateLimit-Remaining` or `RateLimit-Reset`, and their `X-` variants are absent as well. This is a configuration decision, not an omission in the specification.

Whether `Retry-After` is sent is being confirmed. Do not depend on it yet.

### How to build for it

- **Retry with exponential backoff.** On `429`, wait, then retry with an increasing delay. Do not retry in a tight loop.

- **Cache reference data.** Accounts, VAT rates, booking types and article categories change rarely. Fetch them once per run, not per record.

- **Batch by run, not by record.** Creating two hundred records means two hundred requests. Spread them out.

### Pagination

8 endpoints accept pagination parameters:

| Endpoint | Parameters |
|---|---|
| GET `/core/latest/article-categories` | `limit` no offset, only the first page is reachable |
| GET `/core/latest/article-filters` | `limit` no offset, only the first page is reachable |
| GET `/core/latest/articles` | `limit`, `offset` |
| GET `/core/latest/articles/article-and-variants` | `limit`, `offset` |
| GET `/core/latest/articles/search` | `limit`, `offset` |
| GET `/core/v1/accounting/business-case-templates` | `limit`, `offset` |
| GET `/core/v1/accounting/master-vats` | `limit`, `offset` |
| GET `/core/v1/customers` | `limit`, `offset` |

Three properties of pagination that decide whether a run is reliable.

| Question | Answer |
|---|---|
| Is there a total count? | No. A page smaller than the limit is the only signal that you have reached the end |
| Is the order stable between requests? | No guarantee, with one exception: `GET /core/v1/accounting/business-case-templates` takes an optional `orderColumns` list |
| Can every page be reached? | Not everywhere. `GET /core/latest/article-categories` and `GET /core/latest/article-filters` accept `limit` but no `offset`, so nothing beyond the first page is reachable. Under review |

> **Warning: No stable order means pagination can miss or repeat records**
>
> Without a guaranteed order, a record can move between two requests and end up counted twice or not at all. For a full read, prefer a single large page over many small ones where the limit allows it, and treat the result as a snapshot rather than a transaction.

### Searching and filtering

#### Free-text search

6 endpoints take a search term and match it against several fields at once. This is the only way to find a record without knowing an id.

| Endpoint | Parameter |
|---|---|
| GET `/core/latest/article-categories` | `keyword` |
| GET `/core/latest/article-filters` | `keyword` |
| GET `/core/latest/articles/search` | `keyword` |
| GET `/core/v1/bank-reconciliation/open-positions` | `general-search` |
| GET `/core/v1/customers` | `search-key` |
| GET `/core/v1/employees/short-info` | `search-key` |

#### Filtering by field

32 endpoints narrow the result by a field value, 73 parameters in total. Filtering is not searching: you have to know the value you are filtering on.

| Endpoint | Parameters |
|---|---|
| GET `/core/latest/article-categories` | `active-status` |
| GET `/core/latest/article-filters` | `active-status` |
| GET `/core/latest/articles` | `product-type` |
| GET `/core/latest/articles/article-and-variants` | `include-quantity`, `sell-in-booking`, `sell-in-online-shop`, `use-pos` |
| GET `/core/latest/articles/article-numbers` | `article-numbers`, `export-vat`, `price-date`, `should-validate-vat` |
| GET `/core/latest/articles/search` | `export-vat`, `price-date`, `should-validate-vat` |
| POST `/core/latest/payroll-interface-file` | `file-format`, `payslip-ids`, `salary-run-id` |
| POST `/core/latest/subscriptions` | `marketing-code` |
| GET `/core/latest/vat-cases` | `applicability` |
| GET `/core/v1/accounting/accounts` | `legal-form` |
| GET `/core/v1/accounting/accounts/account-displaying` | `legal-form` |
| GET `/core/v1/accounting/accounts/by-code/{accountCode}` | `legal-form` |
| GET `/core/v1/accounting/business-cases` | `businessCaseTemplateId`, `dateForFilteringCompanyVat`, `dateForFilteringFiscalYear` |
| GET `/core/v1/accounting/business-cases/v2` | `businessCaseTemplateId`, `dateForFilteringCompanyVat`, `dateForFilteringFiscalYear`, `group-insurance-by-insurer`, `snippet-id`, `snippet-value` |
| GET `/core/v1/accounting/currencies/exchange-rate` | `exchange-date`, `from-currency-code` |
| GET `/core/v1/accounting/master-vats` | `code`, `current-period`, `default-vat`, `includeHiddenVatCase` |
| GET `/core/v1/accounting/vat-profile` | `date` |
| GET `/core/v1/accounting/vat-suggestions` | `accountCode`, `amount`, `amountKind`, `date`, `direction`, `vatCase` |
| GET `/core/v1/bank-reconciliation/open-positions` | `action`, `booking-type-codes`, `crdr-type`, `credit-card-not-reconciled`, `invoice-date`, `partner`, `payment-date`, `payment-date-from`, `payment-date-to`, `position-status`, `tags` |
| POST `/core/v1/bookings` | `autoCalculateVat`, `confirmDontMindClosingFiscalYear`, `ignore-unsubscripted-dates` |
| PUT `/core/v1/bookings/{id}/documents` | `ignore-unsubscripted-dates` |
| GET `/core/v1/company-bank-accounts` | `iban-number`, `type` |
| GET `/core/v1/customers` | `status` |
| GET `/core/v1/employees/short-info` | `filter-by-status`, `workplace-ids` |
| PUT `/core/v1/invoices` | `confirmDontMindClosingFiscalYear` |
| POST `/core/v1/invoices` | `confirmDontMindClosingFiscalYear` |
| POST `/core/v1/invoices/{id}/send` | `channel` |
| GET `/core/v1/order-documents/filter/type-and-order-number` | `order-number`, `type` |
| GET `/core/v1/order-numbering-configurations` | `order-type` |
| POST `/core/v1/payroll/contracts/{contractId}/payslips/{payslipId}/salary-items` | `recalculate` |
| GET `/core/v1/payroll/employees/{employeeId}/addable-salary-items` | `month` |
| POST `/core/v1/vat-clearing-reports/{year}/{quarter}` | `recalculate` |

#### Sorting

2 endpoints accept a sort order: `/core/v1/accounting/business-case-templates`, `/core/v1/employees/short-info`. Everywhere else the order is not guaranteed, which matters when you page through a large collection.

Invoices are the most important gap: there is no list endpoint at all, so they cannot be listed or filtered. You have to keep the ids you created. See [Known limitations](https://developer.klara.ch/#roadmap).

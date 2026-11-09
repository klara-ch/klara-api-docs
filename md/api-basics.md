# API basics

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#api-basics

## API basics

Everything you need once, so you never have to guess again.

### API routes

All endpoints live on `https://api.klara.ch`. Paths in this reference are relative and must be combined with the host:

```
GET /core/v1/invoices/42
→ https://api.klara.ch/core/v1/invoices/42
```

Three prefixes are in use. `/core/v1` holds accounting, finance and payroll, `/core/v2` holds company and individual profiles, and `/core/latest` still holds articles, customers and authentication.

### Path prefixes and what is current

Three prefixes are in use. `/core/v1` and `/core/v2` are the current ones. `/core/latest` is older and mixed: some endpoints live only there and are perfectly current, others are superseded.

> **Warning: The superseded `/core/latest` operations stop answering on 14 August 2027**
>
> 24 operations carry `deprecated` in the source contract: 20 in `accounting`, 2 in `bookings`, 1 in `bank-reconciliation`, 1 in `vat-clearing-reports`. They are not in the specification published here, and they carry a sunset date in the source contract: on 14 August 2027, they answer `410 Gone` instead of returning data. Until then they still respond, so an existing integration does not break today, but the migration has a deadline. New work must use the current path.
>
> | Superseded | Use instead |
> |---|---|
> | GET `/core/latest/accounting/accounts` | `/core/v1/accounting/accounts` |
> | GET `/core/latest/accounting/accounts/account-displaying` | `/core/v1/accounting/accounts/account-displaying` |
> | GET `/core/latest/accounting/accounts/by-code/{accountCode}` | `/core/v1/accounting/accounts/by-code/{accountCode}` |
> | GET `/core/latest/accounting/booking-types` | `/core/v1/accounting/booking-types` |
> | GET `/core/latest/accounting/business-case-templates` | `/core/v1/accounting/business-case-templates` |
> | GET `/core/latest/accounting/business-case-templates/{businessCaseTemplateId}` | `/core/v1/accounting/business-case-templates/{businessCaseTemplateId}` |
> | GET `/core/latest/accounting/business-cases` | `/core/v1/accounting/business-cases` |
> | GET `/core/latest/accounting/business-cases/v2` | `/core/v1/accounting/business-cases/v2` |
> | GET `/core/latest/accounting/business-cases/{businessCaseId}` | `/core/v1/accounting/business-cases/{businessCaseId}` |
> | GET `/core/latest/accounting/companies/current` | `/core/v1/accounting/companies/current` |
> | GET `/core/latest/accounting/companies/current/business-case-templates` | `/core/v1/accounting/companies/current/business-case-templates` |
> | GET `/core/latest/accounting/companies/current/financial-years` | `/core/v1/accounting/companies/current/financial-years` |
> | GET `/core/latest/accounting/currencies/exchange-rate` | `/core/v1/accounting/currencies/exchange-rate` |
> | GET `/core/latest/accounting/master-vats` | `/core/v1/accounting/master-vats` |
> | GET `/core/latest/accounting/master-vats/current` | `/core/v1/accounting/master-vats/current` |
> | GET `/core/latest/accounting/master-vats/{masterVatId}` | `/core/v1/accounting/master-vats/{masterVatId}` |
> | GET `/core/latest/accounting/vat-profile` | `/core/v1/accounting/vat-profile` |
> | GET `/core/latest/accounting/vat-suggestions` | `/core/v1/accounting/vat-suggestions` |
> | GET `/core/latest/accounting/vat-types` | `/core/v1/accounting/vat-types` |
> | GET `/core/latest/accounting/vat-types/{vatTypeId}` | `/core/v1/accounting/vat-types/{vatTypeId}` |
> | GET `/core/latest/bank-reconciliation/open-positions` | `/core/v1/bank-reconciliation/open-positions` |
> | POST `/core/latest/bookings` | `/core/v1/bookings` |
> | PUT `/core/latest/bookings/{id}/documents` | `/core/v1/bookings/{id}/documents` |
> | POST `/core/latest/vat-clearing-reports/{year}/{quarter}` | `/core/v1/vat-clearing-reports/{year}/{quarter}` |

Replacing `/core/latest/` with `/core/v1/` in the path is the whole migration. Request and response are unchanged.

### HTTP verbs

| Verb | Used for | Endpoints |
|---|---|---|
| GET | Retrieving resources | 71 |
| PUT | Replacing a resource completely | 10 |
| POST | Creating resources, and some actions such as `/send` | 26 |
| DELETE | Removing a resource | 7 |

114 endpoints in total, which is the same number as in the reference below and in the downloadable specification.

The KLARA API does not use `PATCH`. To change part of a resource, send a complete `PUT`. Read the current state first if you do not hold it.

### Headers

| Header | Value | When |
|---|---|---|
| `X-API-KEY` | your API key | API key authentication |
| `Authorization` | `Bearer <JWT>` | Token authentication |
| `Accept` | `application/json` | Almost everywhere. Three endpoints return binary or PDF instead, see below |
| `Content-Type` | depends on the endpoint | On any request with a body. Most take `application/json`, the token endpoints take `application/x-www-form-urlencoded` and the upload endpoints take `multipart/form-data`, see below |
| `Accept-Language` | `de-CH`, `fr-CH`, `it-CH` or `en` | Optional, on the endpoints that return translated labels |

### Media types

#### Request bodies

Send the matching `Content-Type`. Form-encoded endpoints reject a JSON body, which is the most common reason a first token request fails.

| Media type | Endpoints | Which ones |
|---|---|---|
| `application/json` | 23 | every endpoint that has one |
| `application/x-www-form-urlencoded` | 4 | `POST /core/latest/generic-token` `POST /core/latest/tenants` `POST /core/latest/token` `POST /core/latest/token/by-microsoft` |
| `multipart/form-data` | 3 | `POST /core/latest/articles/{article-id}/images` `POST /core/latest/companies/{company-id}/documents` `PUT /core/latest/articles/{article-id}/images/{image-id}` |

#### Responses

Ask for what the endpoint returns. `Accept: application/json` is correct almost everywhere, but not for the binary and PDF responses below.

| Media type | Endpoints | Which ones |
|---|---|---|
| `application/json` | 114 | every endpoint that has one |
| `application/octet-stream` | 2 | `GET /core/latest/articles/{article-id}/images/{image-id}` `POST /core/latest/payroll-interface-file` |
| `application/pdf` | 1 | `POST /core/v1/invoices/{id}/printed-document` |

### What an error body contains

The specification declares 533 error responses on 114 operations. Every one of them declares a body, so you can generate a type for the error case and read the `code` and the `uuid` from it.

| Schema | Fields | Error responses | Operations |
|---|---|---|---|
| `ErrorMessage` | `code`, `createdTime`, `detail`, `message`, `uuid` | 514 | 114 |
| `ErrorResponse` | `error`, `error_description` | 19 | 5 |

The OAuth-style pair `error` and `error_description` is declared on 5 operations:

- `GET /core/v2/tenants/companies`

- `POST /core/latest/subscriptions`

- `POST /core/latest/tenants`

- `POST /core/v2/tenants/companies`

- `PUT /core/v2/tenants/companies`

It is not the shape of the authentication endpoints as a group. What the authentication endpoints declare, per endpoint and per status, is listed under [Authentication](https://developer.klara.ch/#authentication). That listing is the binding one; do not generalise from this table to them.

Two of the entries above are not the application speaking. A `429` body comes from the gateway, not from the API, so it is outside this contract and may look like none of these shapes. And where a status declares no body, a gateway may still add one; that too is outside the contract.

The contract names 39 error codes of its own, in the `code` field of the error body:

| Code | Status | Meaning | Where |
|---|---|---|---|
| `account.unknown` | `404` | No account with this number exists in the chart of accounts. | 2 operations |
| `auth.credentials_missing` | `401` | Neither an Authorization: Bearer header nor an API key header was present on the request. | 3 operations |
| `balancesheet.date_invalid` | `400` | date was missing or not an ISO date of the form yyyy-MM-dd, or compare-date was not an ISO date of that form. | 1 operation |
| `banktransaction.booking_date_invalid` | `400` | booking-date-from or booking-date-to was not an ISO date of the form yyyy-MM-dd, or booking-date-to was before booking-date-from. | 1 operation |
| `booking.amount_range_invalid` | `400` | amount-to was below amount-from. | 1 operation |
| `booking.amount_zero` | `400` | grossAmount was zero. The Booking Workflow does not book a document of zero; a negative amount is accepted for a correction. | 1 operation |
| `booking.booking_date_invalid` | `400` | booking-date-from or booking-date-to was not an ISO date of the form yyyy-MM-dd, or booking-date-to was before booking-date-from; for a document booking, bookingDate or booking-date was not an ISO date of that form. | 3 operations |
| `booking.business_case_id_invalid` | `400` | businessCaseId was set but is not a numeric business case id. Leave it empty when creating a booking; use businessCase for a label. | 1 operation |
| `booking.due_date_invalid` | `400` | dueDate was not an ISO date of the form yyyy-MM-dd. | 1 operation |
| `booking.payment_date_invalid` | `400` | paymentDate was not an ISO date of the form yyyy-MM-dd. | 1 operation |
| `booking.payment_date_missing` | `400` | The chosen payment option has a payment date (bank account, card, cash register, cashless payment, transfer account) and paymentDate was not sent. | 1 operation |
| `booking.service_period_invalid` | `400` | servicePeriodFrom or servicePeriodTo was not an ISO date of the form yyyy-MM-dd, only one of them was sent, or servicePeriodTo was before servicePeriodFrom. | 1 operation |
| `booking.status_not_allowed` | `400` | bookingStatus was neither BOOKED nor PLANNED. | 1 operation |
| `booking.unknown` | `404` | No booking with this id exists in the caller's company. | 1 operation |
| `businesscase.field_not_offered` | `400` | A value was sent that the chosen template and payment option do not have (text, servicePeriodFrom, dueDate, paymentPurpose or internalComment). The Booking Workflow does not show that field for this choice; the message names it. | 1 operation |
| `businesscase.partner_unknown` | `404` | No customer or supplier with partnerId exists in the caller's company. | 1 operation |
| `businesscase.payment_group_not_offered` | `400` | The business case template has no already paid option for the payment group, or the group is not available for the company (as in the Booking Workflow, for example EMPLOYEE without employees). | 2 operations |
| `businesscase.payment_source_unknown` | `404` | paymentSourceId was missing or is not one of the sources listBusinessCasePaymentSourcesV1 returns for this template, payment group and booking date. | 1 operation |
| `businesscase.payroll_date_unknown` | `400` | payrollDate was missing for EMPLOYEE or is not one of the payroll dates of the employee. | 1 operation |
| `businesscase.template_not_supported` | `400` | The business case template is not offered by the Booking Workflow for the booking date, or it needs input this operation does not take (asset purchase, current account, transfer account, tax and social insurance templates). The message names the missing input. | 1 operation |
| `businesscase.vat_entry_unknown` | `400` | vatCaseId or vatRateId was missing on a VAT template, or is not one of its entries, or the rate does not belong to the case, or the rate is valid neither on bookingDate nor within the service period. | 1 operation |
| `document.archive_not_supported` | `400` | The document is stored in the KLARA eArchive. bookDocumentAsPaidV1 does not book eArchive documents yet; they are booked in the web app. | 1 operation |
| `document.not_in_booking_queue` | `409` | The document is not waiting in the booking queue, for example because it is already booked. | 1 operation |
| `document.unknown` | `404` | No document with this id exists in the caller's company. | 1 operation |
| `invoice.id_must_be_zero` | `400` | The request body carried a non-zero id. This operation only creates invoices; use PUT /core/v1/invoices to modify an existing one. | 1 operation |
| `invoice.vat_code_missing` | `400` | At least one orderItems[] entry set VAT on vatType, vatCase or the top-level vatCode while leaving vat.vatCode empty. Only vat.vatCode is stored, so that line would have been booked at 0%. Move the code to vat.vatCode, or send "0" ("No VAT") for a position that genuinely carries none. | 2 operations |
| `invoice.vat_rate_mismatch` | `400` | At least one orderItems[] entry sent a vat.rate that is not the rate of its vat.vatCode. The booking always uses the code's rate, so the invoice document would state a different percentage than the accounting record. Send the matching rate, or omit vat.rate and let the server fill it in. | 2 operations |
| `kpi.date_invalid` | `400` | date-from or date-to was missing or not an ISO date of the form yyyy-MM-dd with a year from 0001, or date-to was before date-from; for getSpendingsAndEarningsV1 also when the comparison period was incomplete, not of that form or reversed, or the default comparison period would start before the year 0001. | 2 operations |
| `ledger.account_range_invalid` | `400` | account-to was below account-from. | 1 operation |
| `openposition.reference_date_invalid` | `400` | reference-date was not an ISO date of the form yyyy-MM-dd. Omit it to use today. | 1 operation |
| `partneramounts.date_invalid` | `400` | to-date was not an ISO date of the form yyyy-MM-dd. | 1 operation |
| `profitandloss.date_invalid` | `400` | date-from or date-to was missing or not an ISO date of the form yyyy-MM-dd, date-to was before date-from, or the comparison period was incomplete, not of that form or reversed. | 1 operation |
| `request.search_key_unsupported` | `400` | search-key was sent to an operation that has no text search. | 2 operations |
| `request.sort_direction_unsupported` | `400` | sort-direction was sent with the direction an operation with a fixed order does not have. The message names the available direction. | 3 operations |
| `request.sort_field_invalid` | `400` | sort-field was not one of the values the operation documents. The message lists the accepted values. | 7 operations |
| `statement.booking_type_invalid` | `400` | An entry of booking-types was not one of the published booking types. | 2 operations |
| `statement.date_invalid` | `400` | date-from or date-to was not an ISO date of the form yyyy-MM-dd, or date-to was before date-from. | 2 operations |
| `statement.reference_invalid` | `400` | reference was not of the form TYPE:id or SUB_ACCOUNT:category:id with a type, category and id as published in references of getBookingV1. | 2 operations |
| `statement.vat_filter_invalid` | `400` | An entry of vat was not none, a VAT case id, or a VAT case id and a VAT rate id separated by a colon. | 2 operations |

That list is not exhaustive and is not meant to be: a failure detected by a downstream service carries that service's own code. Branch on the HTTP status, treat an unknown code as a generic error of that status, and log the code and the `uuid` for a support request.

### Errors

Most error responses use this shape:

```
{
  "uuid":        "b3f1c8e2-...",
  "createdTime": "2026-07-30T09:12:44Z",
  "code":        "VALIDATION_FAILED",
  "message":     "Human readable description"
}
```

A small group of endpoints uses the OAuth-style shape instead. Which ones is listed in the table above, and the authentication endpoints are covered separately under [Authentication](https://developer.klara.ch/#authentication).

```
{ "error": "invalid_grant", "error_description": "Bad credentials" }
```

The `uuid` identifies one specific occurrence. Include it in support requests. It is the fastest route to an answer.

The field `detail` still appears in some responses but is deprecated. Do not build on it.

### OpenAPI specification

> **Note: Download the specification**
>
> The full KLARA API is published as an OpenAPI 3.0.3 document. Generate clients, import it into Postman or Insomnia, spin up a mock server, or run contract tests against it.
>
> [klara-openapi.json](klara-openapi.json)

> **Note: Machine-readable versions of this page**
>
> Besides the specification there is [`llms.txt`](llms.txt), an index in Markdown that lists every chapter of this documentation and every area of the reference as a separate Markdown file. Built in the same run as this page, from the same source. Useful for coding assistants, retrieval and anything that would otherwise parse the HTML.

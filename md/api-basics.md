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

> **Note: The superseded `/core/latest` operations are not in this specification**
>
> 24 operations carry `deprecated` in the source contract: 20 in `accounting`, 2 in `bookings`, 1 in `bank-reconciliation`, 1 in `vat-clearing-reports`. They are excluded from the specification published here, so searching that file for them finds nothing. They still respond at the API, so an existing integration does not break, but they are not part of this contract and new work must use the current path.
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
| GET | Retrieving resources | 55 |
| PUT | Replacing a resource completely | 10 |
| POST | Creating resources, and some actions such as `/send` | 25 |
| DELETE | Removing a resource | 7 |

97 endpoints in total, which is the same number as in the reference below and in the downloadable specification.

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
| `application/json` | 22 | every endpoint that has one |
| `application/x-www-form-urlencoded` | 4 | `POST /core/latest/generic-token` `POST /core/latest/tenants` `POST /core/latest/token` `POST /core/latest/token/by-microsoft` |
| `multipart/form-data` | 3 | `POST /core/latest/articles/{article-id}/images` `POST /core/latest/companies/{company-id}/documents` `PUT /core/latest/articles/{article-id}/images/{image-id}` |

#### Responses

Ask for what the endpoint returns. `Accept: application/json` is correct almost everywhere, but not for the binary and PDF responses below.

| Media type | Endpoints | Which ones |
|---|---|---|
| `application/json` | 97 | every endpoint that has one |
| `application/octet-stream` | 2 | `GET /core/latest/articles/{article-id}/images/{image-id}` `POST /core/latest/payroll-interface-file` |
| `application/pdf` | 1 | `POST /core/v1/invoices/{id}/printed-document` |

### What an error body contains

The specification declares 433 error responses on 97 operations. 234 of them declare no body at all, which is why the shape matters less than the status code.

| Schema | Fields | Error responses | Operations |
|---|---|---|---|
| `ErrorMessage`, `ErrorMessage1` | `code`, `createdTime`, `detail`, `message`, `uuid` | 179 | 88 |
| `ErrorResponse` | `error`, `error_description` | 20 | 6 |
| no body declared | — | 234 | 97 |

The OAuth-style pair `error` and `error_description` is declared on 6 operations:

- `DELETE /core/latest/customers/{customer-id}`

- `GET /core/v2/tenants/companies`

- `POST /core/latest/subscriptions`

- `POST /core/latest/tenants`

- `POST /core/v2/tenants/companies`

- `PUT /core/v2/tenants/companies`

It is not the shape of the authentication endpoints as a group. What the authentication endpoints declare, per endpoint and per status, is listed under [Authentication](https://developer.klara.ch/#authentication). That listing is the binding one; do not generalise from this table to them.

Two of the entries above are not the application speaking. A `429` body comes from the gateway, not from the API, so it is outside this contract and may look like none of these shapes. And where a status declares no body, a gateway may still add one; that too is outside the contract.

> **Warning: Do not branch on error codes**
>
> There is no governed catalogue of `code` values. Branch on the HTTP status, log the message, and treat the code as information for a human reading a support ticket.

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

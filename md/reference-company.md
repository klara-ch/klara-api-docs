# Company

3 operations of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### GET /core/v2/tenants/companies

Find KLARA business company of tenant

Retrieve the business company of a tenant. Use your KLARA token from your username & password & tenant-id.

- operationId: `findCompany`
- Required permission: `COMPENSATION_COMPANY_INFO`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Objektmanager, Online Terminbuchung, Zeit- und Materialrapporte

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `201` | BusinessCompany | Company found |
| `401` | ErrorResponse | Invalid credentials Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorResponse | The user has been disabled Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorResponse | Something went wrong when find company Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/v2/tenants/companies

Create a KLARA business company

A KLARA user might have multiple company tenants. Use this endpoint to create a business company and its correspondence tenant. Use your KLARA token from your username & password & tenant-id.

- operationId: `createTenantAndBusinessCompany`
- Required permission: `COMPENSATION`, `FINANCE_COMPANY_VAT`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Request body** (`application/json`, not marked required in the specification)

| Field | Type | Required | Notes |
|---|---|---|---|
| `name` | string | yes | Name of the company |
| `legalForm` | object | yes |  |
| `phones` | array | yes | Phone numbers of the company |
| `emails` | array | yes | Emails of this company |
| `addresses` | array | yes | Address list of this company, at least one should be add. For company, address type MUST be PRIVATE |
| `language` | string | yes | Preferred language of the company. Supported: English, German, French, Italian One of `de`, `en`, `fr`, `it`. |
| `corporateIdentificationNumber` | string | no | Every business active in Switzerland is given a unique enterprise identification number (UID). To ensure that numbers are correctly allocated and managed, the UID register is run by the Federal Statistical Office |
| `foundingDate` | string (date) | no | Founding date of this company in ISO 8601 format (yyyy-mm-dd) |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `201` | application/json | Tenant and company created |
| `400` | ErrorResponse | Invalid company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorResponse | Invalid credentials Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorResponse | The user has been disabled Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorResponse | Something went wrong when creating tenant and business company Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### PUT /core/v2/tenants/companies

Update a KLARA business company

A KLARA user might have multiple tenants. Use this endpoint to update a KLARA business company. Use your KLARA token from your username & password & tenant-id.

- operationId: `updateCompany`
- Required permission: `COMPENSATION`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Request body** (`application/json`, not marked required in the specification)

| Field | Type | Required | Notes |
|---|---|---|---|
| `name` | string | yes | Name of the company |
| `legalForm` | object | yes |  |
| `phones` | array | yes | Phone numbers of the company |
| `emails` | array | yes | Emails of this company |
| `addresses` | array | yes | Address list of this company, at least one should be add. For company, address type MUST be PRIVATE |
| `language` | string | yes | Preferred language of the company. Supported: English, German, French, Italian One of `de`, `en`, `fr`, `it`. |
| `corporateIdentificationNumber` | string | no | Every business active in Switzerland is given a unique enterprise identification number (UID). To ensure that numbers are correctly allocated and managed, the UID register is run by the Federal Statistical Office |
| `foundingDate` | string (date) | no | Founding date of this company in ISO 8601 format (yyyy-mm-dd) |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `201` | BusinessCompany | Company updated |
| `400` | ErrorResponse | Invalid company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorResponse | Invalid credentials Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorResponse | The user has been disabled Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorResponse | Something went wrong when updating tenant Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

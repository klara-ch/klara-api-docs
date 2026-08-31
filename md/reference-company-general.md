# Company general

4 operations of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### GET /core/latest/company-configuration/including-vat

Get the VAT inclusion setting for the authenticated company.

Returns whether invoice amounts for the company resolved from the bearer JWT are displayed and calculated including VAT (`includingVat: true`) or excluding VAT (`includingVat: false`). Use this value before creating or displaying invoices to apply the correct pricing model. When no configuration record exists for the company, the endpoint returns `false` (excluding VAT) as the default. The caller must hold the `FINANCE` permission on the target company.

- operationId: `getCompanyIncludingVatSetting`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | CompanyConfigurationIncludingVatResult | VAT inclusion setting for the authenticated company. |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### GET /core/latest/company-vats

Returns vat list of a company

- operationId: `listCompanyVatLiabilities`
- Required permission: `FINANCE_COMPANY_VAT`
- Roles that carry it: Administrator, Buchhalter, External user, Mitarbeiter und Lohn

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `Accept-Language` | header | no | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Company vats |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |

### GET /core/latest/vat-cases

Returns VAT case list.

- operationId: `listVatCases`
- Required permission: none, this endpoint is how a token is obtained

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `applicability` | query | no | ApplicabilityVatCaseType |  |
| `Accept-Language` | header | no | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | VAT cases |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |

### GET /core/latest/vat-cases/{vat-case-id}

Get the VAT case by Id.

- operationId: `getVatCaseById`
- Required permission: none, this endpoint is how a token is obtained

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `vat-case-id` | path | yes | string |  |
| `Accept-Language` | header | no | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | VatCase | VAT case |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |

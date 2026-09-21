# Authentication

3 operations of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### POST /core/latest/tenants

Returns all tenants of a user

A KLARA user might have multiple tenants. Use this endpoint to get the list of tenants containing tenant id, company id. The tenant and company id returned by this endpoint can be used to generate tokens to access other endpoints. Use your KLARA username and password OR access-token to get the list of tenants.

- operationId: `listTenants`
- Required permission: none, this endpoint is how a token is obtained

**Request body** (`application/x-www-form-urlencoded`, not marked required in the specification)

| Field | Type | Required | Notes |
|---|---|---|---|
| `username` | string | no |  |
| `password` | string | no |  |
| `access_token` | string | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Found tenants |
| `400` | ErrorResponse | Missing parameters Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorResponse | Invalid credentials Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorResponse | The user has been disabled Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorResponse | Something went wrong when getting list of tenants Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/latest/token

Generate tokens to access other KLARA core endpoints

After obtaining tenant id and company id for the desired tenant, use this endpoint to get the access token for a specific tenant, and refresh token. A grant_type of password is used for a Password grant, which requires username, password, tenant and company id to produce access token, refresh token from scratch. A grant_type of refresh_token is used for a Refresh token grant, which is used for acquiring the access token using refresh token. A grant_type of token_exchange performs an OAuth 2.0 Token Exchange (RFC 8693). Provide subject_token (the raw upstream Bearer token, without the 'Bearer ' prefix) and audience to identify the target token type. Supported audience values:cossa — exchanges a COSSA bearer token for a ePost access token.Note: subject_token must be the raw token value without the 'Bearer ' prefix.

- operationId: `createPublicApiToken`
- Required permission: none, this endpoint is how a token is obtained

**Request body** (`application/x-www-form-urlencoded`, not marked required in the specification)

| Field | Type | Required | Notes |
|---|---|---|---|
| `username` | string | no |  |
| `password` | string | no |  |
| `grant_type` | string | no |  |
| `tenant_id` | string | no |  |
| `company_id` | string | no |  |
| `refresh_token` | string | no |  |
| `subject_token` | string | no |  |
| `audience` | string | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicAPIToken | Token created |
| `400` | ErrorMessage | Could not get token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | Invalid credentials Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Internal server error Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/latest/token/by-microsoft

Exchange Microsoft access token for system token

Provide a Microsoft access token and tenant id to exchange for a system token.

- operationId: `createPublicApiTokenByMicrosoft`
- Required permission: none, this endpoint is how a token is obtained

**Request body** (`application/x-www-form-urlencoded`, not marked required in the specification)

| Field | Type | Required | Notes |
|---|---|---|---|
| `microsoft_access_token` | string | no |  |
| `tenant_id` | string | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | AccessTokenResponse | Token created |
| `400` | ErrorMessage | Could not get token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | Invalid credentials Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Internal server error Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

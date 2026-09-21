# KLARA authentication generic

1 operation of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### POST /core/latest/generic-token

Generate tokens to use KLARA specific endpoint

Use this endpoint to get the access token for a specific user, and refresh token. A grant_type of password is used for a Password grant, which requires username, password, access token, refresh token from scratch. A grant_type of refresh_token is used for a Refresh token grant, which is used for acquiring the access token using refresh token.

- operationId: `createGenericApiToken`
- Required permission: none, this endpoint is how a token is obtained

**Request body** (`application/x-www-form-urlencoded`, not marked required in the specification)

| Field | Type | Required | Notes |
|---|---|---|---|
| `username` | string | no |  |
| `password` | string | no |  |
| `grant_type` | string | no |  |
| `refresh_token` | string | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | PublicAPIToken | Token created |
| `400` | ErrorMessage | Could not get token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | Invalid credentials Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Internal server error Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

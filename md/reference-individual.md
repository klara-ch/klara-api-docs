# Individual

2 operations of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### GET /core/v2/tenants/individuals/profile

[PREVIEW_API] Get profile from tenant's ID

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Retrieved profile successfully |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `404` | ErrorMessage | Profile not found |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

### POST /core/v2/tenants/individuals/profile

[PREVIEW_API] Create profile

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Created profile successfully |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `404` | ErrorMessage | Resource not found |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. |

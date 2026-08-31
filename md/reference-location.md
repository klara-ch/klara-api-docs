# Location

1 operation of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### GET /core/latest/cities/{city-id}

Returns city details

Returns a City details with given id.

- operationId: `getCityById`
- Required permission: none, this endpoint is how a token is obtained

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `city-id` | path | yes | string | City id |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | City | Found city |
| `401` | none declared | No Authorization header found or invalid token |
| `404` | none declared | Resource not found |
| `429` | none declared | API rate limit exceeded |

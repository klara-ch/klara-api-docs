# Authentication

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#authentication

## Authentication

The KLARA API accepts two mechanisms. Most endpoints accept either.

| Mechanism | How | Best for |
|---|---|---|
| `apiKeyAuth` | Header `X-API-KEY` | Server-to-server integrations bound to one company |
| `bearerAuth` | Header `Authorization: Bearer <JWT>` | User-context flows, multi-tenant tools |

### Token flow

A KLARA user can belong to several tenants. Resolve the tenant first, then request a token.

```
POST /core/latest/tenants     # returns tenant id + company id
POST /core/latest/token       # grant_type: password | refresh_token | token_exchange
```

> **Warning: The token endpoints expect a form body, not JSON**
>
> All four take `Content-Type: application/x-www-form-urlencoded`. Sending JSON is the most common reason a first token request fails. Full media type list under [Requests and responses](https://developer.klara.ch/#api-basics).

#### Which fields each grant needs

The specification does not mark any of these form fields as required, so the table below is taken from the endpoint descriptions rather than from the schema. Treat it as the working contract and expect `400` if a field is missing.

| Endpoint | `grant_type` | Required fields | Returns |
|---|---|---|---|
| `POST /core/latest/token` | `password` | `username`, `password`, `tenant_id`, `company_id` | Access token scoped to one company, plus refresh token |
| `refresh_token` | `refresh_token` | A new access token without re-sending credentials |  |
| `token_exchange` | `subject_token`, `audience` | OAuth 2.0 Token Exchange, RFC 8693. Pass `subject_token` as the raw upstream token value, without the `Bearer ` prefix. The only `audience` value the specification documents is `cossa` |  |
| `POST /core/latest/generic-token` | `password` | `username`, `password` | Access token for the user, not bound to a company |
| `refresh_token` | `refresh_token` | A new access token |  |
| `POST /core/latest/token/by-microsoft` | not applicable | `microsoft_access_token`, `tenant_id` | System token |
| `POST /core/latest/tenants` | not applicable | `username` and `password`, *or* `access_token` | The user's tenants with their tenant and company ids |

**Which one do I use?** `/core/latest/token` returns a token bound to one tenant and company, which is what you want for a normal integration. `/core/latest/generic-token` takes no tenant or company and returns a token for the user alone. If you are unsure, use `/core/latest/token`.

#### Complete example, password grant

```
curl -X POST "https://api.klara.ch/core/latest/tenants" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "username=$KLARA_USER" \
  --data-urlencode "password=$KLARA_PASSWORD"
# → pick the tenant id and company id you want to work with

curl -X POST "https://api.klara.ch/core/latest/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "grant_type=password" \
  --data-urlencode "username=$KLARA_USER" \
  --data-urlencode "password=$KLARA_PASSWORD" \
  --data-urlencode "tenant_id=$TENANT_ID" \
  --data-urlencode "company_id=$COMPANY_ID"

# use the access_token from the response on any other endpoint
curl -X GET "https://api.klara.ch/core/v1/customers?limit=10" \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -H "Accept: application/json"
```

#### Refreshing

```
curl -X POST "https://api.klara.ch/core/latest/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "grant_type=refresh_token" \
  --data-urlencode "refresh_token=$REFRESH_TOKEN"
```

> **Warning: Error bodies on the authentication endpoints, per endpoint**
>
> | Endpoint | Statuses with no declared body | Declared schema |
> |---|---|---|
> | `POST /core/latest/generic-token` | `400`, `401`, `429`, `500` | — |
> | `POST /core/latest/tenants` | `429` | `ErrorResponse` |
> | `POST /core/latest/token` | `400`, `401`, `429`, `500` | — |
> | `POST /core/latest/token/by-microsoft` | `400`, `401`, `429`, `500` | — |
>
> 3 of these endpoints declare nothing at all for any error status. Where no body is declared, plan for the status code alone and log the request you sent. Whatever a gateway adds on top is not part of this contract, so do not parse it.

#### Endpoints that need no authentication

4 endpoints require no authentication, because they are how you obtain a token in the first place. Everything else in this reference accepts either an API key or a bearer token, and no endpoint accepts only one of the two.

- POST `/core/latest/generic-token`: Generate tokens to use KLARA specific endpoint

- POST `/core/latest/tenants`: Returns all tenants of a user

- POST `/core/latest/token`: Generate tokens to access other KLARA core endpoints

- POST `/core/latest/token/by-microsoft`: Exchange Microsoft access token for system token

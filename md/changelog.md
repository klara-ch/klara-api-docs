# Changelog

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#changelog

## Changelog

What changed and when. Changes that can break an existing integration are marked as such.

### API changes

#### 28 August 2026

1 changed

#### Changed

- POST `/core/latest/generic-token` — moved from area Klara authentication generic to KLARA authentication generic

### Documentation changes

#### 28 August 2026

Machine-readable formats, error bodies, search, roles and superseded paths

- **This documentation is now available in machine-readable form.** [`llms.txt`](llms.txt) indexes every chapter as a Markdown file and every area of the reference as a Markdown file with parameters, request and response bodies, the required permission and the roles that carry it. Built in the same run as this page, from the same source. The specification itself is unchanged and remains the contract.

- The specification is now linked at the top of this page instead of only inside [API basics](https://developer.klara.ch/#api-basics).

- **The specification now spells the brand KLARA in capitals**, including one tag name that was `Klara authentication generic`. If your client generator groups methods by tag, that group is renamed. Paths, parameters and response shapes are untouched.

- The superseded `/core/latest` accounting and booking operations are excluded from the published specification, not just left undocumented. They still respond at the API. Each has an identical counterpart under `/core/v1`, and replacing the prefix is the whole migration. See [Path prefixes and what is current](https://developer.klara.ch/#api-basics).

- Error bodies on the authentication endpoints are now listed per endpoint and per status, because the general rule does not hold for them. Three of them declare no body for any error status. `POST /core/latest/tenants` declares `error` and `error_description` for `400`, `401`, `403` and `500`, and nothing for `429`. See [Authentication](https://developer.klara.ch/#authentication).

- **Free-text search and filtering by field are now separated**, because they are not the same thing. Free-text search exists on six endpoints. 32 endpoints filter by field, with 73 parameters in total, and there you have to know the value you are filtering on. Two endpoints accept a sort order. See [Searching and filtering](https://developer.klara.ch/#limits).

- **Correction, please check your key:** the permission summary of [Webshop order to customer and invoice](https://developer.klara.ch/#uc-webshop) was incomplete. It did not include the permissions of the invoicing steps that flow reuses. `ARTICLE_READ_ONLY` and `FINANCE_BANK_ACCOUNT` are now listed. A key provisioned from the earlier list is short of both.

- **Correction, the recommended starting role has changed.** The getting started guide recommended `Accountant`, which cannot read articles, so the first call of the guide answered `403`. The guide now recommends a role that covers every call it makes.

#### 24 August 2026

Rate limit, error responses, pagination and API keys

- The rate limit is **at least 10,000 requests per minute**, counted per calling IP address, the same on every endpoint. The informational rate limit headers are suppressed at the gateway, so do not expect them. See [Limits](https://developer.klara.ch/#limits).

- `401` returns `ErrorMessage` even where the specification declares no body. `429` returns a body produced by the gateway rather than the application. There is no governed catalogue of error codes, so branch on the HTTP status and not on the code.

- Pagination has no total count and no guaranteed order, so a full read is a snapshot rather than a transaction. `GET /core/latest/article-categories` and `GET /core/latest/article-filters` accept `limit` without `offset`, so only the first page is reachable.

- API keys do not expire. You can revoke one yourself with immediate effect under **Benutzer**, and a last used timestamp is shown per key. The role of a key is fixed when it is created and cannot be changed later.

- Adding a salary item is **not idempotent**: a retry after a timeout appends a second item. `PUT` and `DELETE` do not exist on salary items yet, so corrections happen in the KLARA interface.

- New endpoints are versioned explicitly and start on `/core/v1`. A replaced endpoint stays available for **twelve months** after it is marked deprecated.

- **Correction to an earlier statement:** `External user` holds the same Public API permissions as `Administrator`. Choosing it instead of Administrator does not narrow what a key can reach.

#### 17 August 2026

Initial publication

- 97 endpoints across accounting, finance, articles, customers, payroll and delivery. Accounting, finance and payroll are served under `/core/v1`, articles, customers and authentication under `/core/latest`.

- Every endpoint names the permission it requires and the roles that carry it.

- Eight integration flows with the calls they take and the permissions those calls need in total.

- The full specification is available for download, for generating clients and for contract testing.

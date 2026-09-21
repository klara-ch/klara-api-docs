# Subscription

1 operation of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### POST /core/latest/subscriptions

Create subscriptions for KLARA tenants.

A KLARA user might have multiple subscriptions. Each subscription will enable a set of feature inside KLARA platform. This endpoint allows to create subscriptions for a KLARA tenant. Each subscription is subjective to its own terms and conditions. Please ask your KLARA contacts or see more details using the KLARA webclient.

- operationId: `createSubscription`
- Required permission: `STORE_CUSTOMER`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `marketing-code` | query | no | array | Marketing code to identify which product will be subscribed. The marketing codes to create subscriptions for. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `201` | application/json | Subscription created successfully |
| `400` | ErrorResponse | Invalid subscription data. Note: for historical reasons this operation reports errors in the OAuth `error`/`error_description` shape rather than the `ErrorMessage` shape used elsewhere in the business API. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorResponse | Invalid credentials Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorResponse | The user has been disabled Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorResponse | Something went wrong when creating subscriptions for the company. Note: for historical reasons this operation reports errors in the OAuth `error`/`error_description` shape rather than the `ErrorMessage` shape used elsewhere in the business API. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

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
| `400` | ErrorResponse | Invalid subscription data |
| `401` | ErrorResponse | Invalid credentials |
| `403` | ErrorResponse | The user has been disabled |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorResponse | Something went wrong when creating subscriptions for the company |

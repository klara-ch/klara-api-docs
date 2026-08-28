# Customer

14 operations of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### GET /core/latest/customers

Returns all customers of a company

- Required permission: `FINANCE_GET_CUSTOMER`
- Roles that carry it: Administrator, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Customers |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |

### POST /core/latest/customers

Create a new customer

- Required permission: `FINANCE_CREATE_CUSTOMER`
- Roles that carry it: Administrator, External user, Kundenbeziehungen, Mitarbeiter und Lohn

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of this Customer. Does not need to be included when creating customer |
| `person` | object | no |  |
| `company` | object | no |  |
| `priceCategory` | string | no | You can select / enter a price category. On the articles you can define a special price for this price category. When such an article is sold / invoiced, the price of this category will apply if it is identical for the article and the customer. |
| `customerType` | object | yes |  |
| `_links` | object | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `201` | Customer | Customer created |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |

### DELETE /core/latest/customers/{customer-id}

Delete a customer

- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `customer-id` | path | yes | string | Id of the customer to be deleted |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `204` | none declared | Customer deleted |
| `400` | ErrorResponse | Could not delete Customer that contain orders |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | none declared | Resource not found |
| `429` | none declared | API rate limit exceeded |

### GET /core/latest/customers/{customer-id}

Returns a customer of a company based on given id

- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `customer-id` | path | yes | string | Id of the customer |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | Customer | Found Customer |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | none declared | Resource not found |
| `429` | none declared | API rate limit exceeded |

### PUT /core/latest/customers/{customer-id}

Update an existing customer

- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `customer-id` | path | yes | string | Id of the customer to be updated |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of this Customer. Does not need to be included when creating customer |
| `person` | object | no |  |
| `company` | object | no |  |
| `priceCategory` | string | no | You can select / enter a price category. On the articles you can define a special price for this price category. When such an article is sold / invoiced, the price of this category will apply if it is identical for the article and the customer. |
| `customerType` | object | yes |  |
| `_links` | object | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | Customer | Customer updated |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | none declared | Resource not found |
| `429` | none declared | API rate limit exceeded |

### GET /core/latest/customers/{customer-id}/additional-addresses

Gets all additional addresses of a customer

- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `customer-id` | path | yes | string | Id of the customer to get additional addresses |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | List of all additional addresses |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |

### POST /core/latest/customers/{customer-id}/additional-addresses

Creates a new customer's additional address

- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `customer-id` | path | yes | string | Id of the customer to add additional addresses to |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of this Address. Does not need to be included when creating customer |
| `validFrom` | string (date) | no | The timestamp from which this address is valid |
| `validTo` | string (date) | no | The timestamp to which this address is valid |
| `addressLines` | string | yes | The address lines for this Address |
| `addressType` | string | yes | The type of address, could be [PRIVATE, WORK, SHIPPING]. For company, address type MUST be PRIVATE. |
| `cityName` | string | yes | Name of this City |
| `cityZipCode` | string | no | The postal code of a city for this address |
| `countryIso2Code` | string | yes | 2 letter country code. For company, only accept Switzerland |
| `countryIso3Code` | string | no | 3 letter country code. For company, only accept Switzerland |
| `countryNumericCode` | string | no | ISO-numeric code. For company, only accept Switzerland |
| `city_href` | string | no | The path to get City object by city's id, /cities/{} |
| `definitionName` | string | no | definition name of this address; in case main address, value is null; else value is not blank |
| `additionalAddress` | string | no | Additional address for more specific |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | Address | Customer's new additional address created |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |

### DELETE /core/latest/customers/{customer-id}/additional-addresses/{address-id}

Delete an additional-address data by customer's id and customer's contact-id

- operationId: `delete additional-address`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `address-id` | path | yes | string | Id of the additional address needed to be updated |
| `customer-id` | path | yes | string | Id of the customer to update an additional address deleted |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `204` | none declared | Additional-address deleted |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | none declared | Resource not found |
| `429` | none declared | API rate limit exceeded |

### PUT /core/latest/customers/{customer-id}/additional-addresses/{address-id}

Updates a Address

- operationId: `update additional-addresses`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `address-id` | path | yes | string | The id of the requested Address |
| `customer-id` | path | yes | string | Id of the customer needed to update an additional address |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of this Address. Does not need to be included when creating customer |
| `validFrom` | string (date) | no | The timestamp from which this address is valid |
| `validTo` | string (date) | no | The timestamp to which this address is valid |
| `addressLines` | string | yes | The address lines for this Address |
| `addressType` | string | yes | The type of address, could be [PRIVATE, WORK, SHIPPING]. For company, address type MUST be PRIVATE. |
| `cityName` | string | yes | Name of this City |
| `cityZipCode` | string | no | The postal code of a city for this address |
| `countryIso2Code` | string | yes | 2 letter country code. For company, only accept Switzerland |
| `countryIso3Code` | string | no | 3 letter country code. For company, only accept Switzerland |
| `countryNumericCode` | string | no | ISO-numeric code. For company, only accept Switzerland |
| `city_href` | string | no | The path to get City object by city's id, /cities/{} |
| `definitionName` | string | no | definition name of this address; in case main address, value is null; else value is not blank |
| `additionalAddress` | string | no | Additional address for more specific |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | Address | Updated additional-addresses |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | none declared | Resource not found |
| `429` | none declared | API rate limit exceeded |

### GET /core/latest/customers/{customer-id}/contacts

Gets all contacts of a customer

- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `customer-id` | path | yes | string | Id of the customer to get all contacts from |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | List of contacts |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |

### POST /core/latest/customers/{customer-id}/contacts

Creates a new customer's contact

- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `customer-id` | path | yes | string | The id of the requested customer to add new contact to |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of this contact. Does not need to be included when creating CustomerContact |
| `imageId` | string | no | Id of this contact's image. Does not need to be included when creating CustomerContact |
| `salutation` | object | no |  |
| `firstName` | string | yes | First name of this contact |
| `lastName` | string | yes | Last name of this contact |
| `email` | string | no | Email of this contact |
| `website` | string | no | Website of this contact |
| `additionalAddressDefinition` | string | no | Additional address definition of this contact |
| `phones` | array | no | Phone number list of this contact |
| `categories` | array | no | Category list of this contact |
| `onlinePlatforms` | array | no | The list of online platforms that this contact uses |
| `customFields` | array | no | The list of custom information of this contact |
| `function` | string | no | Function of this contact |
| `birthday` | string (date) | no | Birth date of this person in ISO 8601 format (yyyy-mm-dd) |
| `note` | string | no | Note of this contact |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | CustomerContact | Customer's new contact created |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |

### DELETE /core/latest/customers/{customer-id}/contacts/{contact-id}

Delete a CustomerContact data by customer's id and customer's contact-id

- operationId: `delete CustomerContact`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `contact-id` | path | yes | string | Id of a customer's contact to be deleted |
| `customer-id` | path | yes | string | Id of the requested customer to have a contact deleted |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `204` | none declared | Customer's contact deleted |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | none declared | Resource not found |
| `429` | none declared | API rate limit exceeded |

### PUT /core/latest/customers/{customer-id}/contacts/{contact-id}

Updates a CustomerContact

- operationId: `update CustomerContact`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `contact-id` | path | yes | string | The id of the requested customer's contact to be updated |
| `customer-id` | path | yes | string | Id of the customer to have a contact updated |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of this contact. Does not need to be included when creating CustomerContact |
| `imageId` | string | no | Id of this contact's image. Does not need to be included when creating CustomerContact |
| `salutation` | object | no |  |
| `firstName` | string | yes | First name of this contact |
| `lastName` | string | yes | Last name of this contact |
| `email` | string | no | Email of this contact |
| `website` | string | no | Website of this contact |
| `additionalAddressDefinition` | string | no | Additional address definition of this contact |
| `phones` | array | no | Phone number list of this contact |
| `categories` | array | no | Category list of this contact |
| `onlinePlatforms` | array | no | The list of online platforms that this contact uses |
| `customFields` | array | no | The list of custom information of this contact |
| `function` | string | no | Function of this contact |
| `birthday` | string (date) | no | Birth date of this person in ISO 8601 format (yyyy-mm-dd) |
| `note` | string | no | Note of this contact |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | CustomerContact | Updated contact |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `404` | none declared | Resource not found |
| `429` | none declared | API rate limit exceeded |

### GET /core/v1/customers

Search and page the customers of the caller's company.

Returns a page of customers (`Persons` + `Companies`) of the caller's company, with optional free-text search (`search-key`) and status filter (`status`). Results are paged with `offset` and `limit`: to retrieve every customer, iterate `offset=0, limit, 2×limit, …` until a response returns fewer than `limit` rows. All query parameters are optional; without any, the endpoint returns the first `50` active customers (downstream default ordering). This is a read-only, idempotent operation. The tenant and company are derived from the JWT (`@CurrentSession Token`) and are never accepted as parameters. The caller must be authenticated (`apiKeyAuth` + `bearerAuth`) and must hold the `FINANCE_GET_CUSTOMER` permission on that company; otherwise the endpoint returns `403`.

- Required permission: `FINANCE_GET_CUSTOMER`
- Roles that carry it: Administrator, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `limit` | query | no | integer | Page size. Defaults to `50`; hard cap `100`. Requests above the cap are rejected with `400`. |
| `offset` | query | no | integer | 0-based pagination offset — the number of customers to skip before the returned page. Defaults to `0`. Combine with `limit` to walk the full list. |
| `search-key` | query | no | string | Free-text search across customer `name`, `email`, `phone` and `customer number`. Tokenised on whitespace; matches are case-insensitive substring matches. Maximum length 256 characters. |
| `status` | query | no | string | Filter by customer state. Allowed values: `ACTIVE`, `ALL`, `ARCHIVED`. When omitted, the downstream service applies its default (active customers only). |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Customers |
| `400` | ErrorMessage | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage | The current user is not allowed to access this company data |
| `429` | none declared | API rate limit exceeded |

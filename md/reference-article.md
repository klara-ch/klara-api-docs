# Article

28 operations of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### GET /core/latest/article-categories

Get all categories

Returns the company's article categories. Page the collection with `offset` and `limit`; the size of the whole filtered collection is returned in the `X-Total-Count` response header, so a caller knows how far it still has to walk. Omitting `limit` returns the whole collection, as before.

- operationId: `listArticleCategories`
- Required permission: `ARTICLE_READ_ONLY`
- Roles that carry it: Administrator, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung, Zeit- und Materialrapporte

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `active-status` | query | no | boolean | Active status |
| `keyword` | query | no | string | Renamed to `search-key`. This name is accepted until 2027-09-01; when both are sent, `search-key` wins. After that date, a request sending `keyword` without `search-key` is rejected with 400. Deprecated — use `search-key` instead. Free-text search over the name. This name has no length limit and `search-key` rejects values over 256 characters, so shorten anything longer before migrating. |
| `limit` | query | no | integer (int32) | Limit filters returned |
| `offset` | query | no | integer (int32) | 0-based pagination offset - the number of rows to skip before the returned page. Defaults to `0`. Combine with `limit` to walk the full collection; the total to walk is returned in the X-Total-Count response header. |
| `search-key` | query | no | string | Free-text search over the name, matched case-insensitively in the language selected by `Accept-Language`. Maximum length 256 characters. |
| `Accept-Language` | header | no | string | Language code is used for filtering by the keyword with multilingual |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Return successfully |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/latest/article-categories

Create a new category

- operationId: `createArticleCategory`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Request body** (`application/json`, not marked required in the specification)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of the category. Does not need to be included when creating article category |
| `nameDE` | string | yes | Name of the category in German |
| `nameEN` | string | no | Name of the category in English |
| `nameFR` | string | no | Name of the category in French |
| `nameIT` | string | no | Name of the category in Italian |
| `order` | integer (int32) | no | Order of this category. Does not need to be included when creating article category. |
| `active` | boolean | no | Indicates if the category is active or not |
| `imageId` | string | no | Image Id of the category. Does not need to be included when creating article category |
| `_links` | object | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `201` | ArticleCategory | Category created |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### DELETE /core/latest/article-categories/{category-id}

Delete category by Id

- operationId: `deleteArticleCategory`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `category-id` | path | yes | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `204` | none declared | Deleted successfully |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/article-categories/{category-id}

Get category by id

- operationId: `getArticleCategoryById`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `category-id` | path | yes | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | ArticleCategory | Return successfully |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### PUT /core/latest/article-categories/{category-id}

Update category by Id

- operationId: `updateArticleCategory`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `category-id` | path | yes | string |  |

**Request body** (`application/json`, not marked required in the specification)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of the category. Does not need to be included when creating article category |
| `nameDE` | string | yes | Name of the category in German |
| `nameEN` | string | no | Name of the category in English |
| `nameFR` | string | no | Name of the category in French |
| `nameIT` | string | no | Name of the category in Italian |
| `order` | integer (int32) | no | Order of this category. Does not need to be included when creating article category. |
| `active` | boolean | no | Indicates if the category is active or not |
| `imageId` | string | no | Image Id of the category. Does not need to be included when creating article category |
| `_links` | object | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | ArticleCategory | Updated successfully |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/latest/article-categories/{category-id}/assign-to-articles

Assign category to articles

Assign the specified article category to multiple articles for usage in Online Booking, Online shop, and Point of sale. Only the articles entered in the request body will have the specified article category added to their respective filters. Other articles will not be affected

- operationId: `assignArticleCategoryToArticles`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `category-id` | path | yes | string |  |

**Request body** (`application/json`, not marked required in the specification)

| Field | Type | Required | Notes |
|---|---|---|---|
| `articleIdsForOnlineShop` | array | no | Article ids need to be assigned with a online shop category |
| `articleIdsForBooking` | array | no | Article ids need to be assigned with a booking category |
| `articleIdsForPos` | array | no | Article ids need to be assigned with a pos category |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `204` | none declared | Assigned successfully |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/article-filters

Search article filters

Returns the company's article filters. Page the collection with `offset` and `limit`; the size of the whole filtered collection is returned in the `X-Total-Count` response header, so a caller knows how far it still has to walk. Omitting `limit` returns the whole collection, as before.

- operationId: `listArticleFilters`
- Required permission: `ARTICLE_READ_ONLY`
- Roles that carry it: Administrator, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung, Zeit- und Materialrapporte

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `active-status` | query | no | boolean | Active status |
| `keyword` | query | no | string | Renamed to `search-key`. This name is accepted until 2027-09-01; when both are sent, `search-key` wins. After that date, a request sending `keyword` without `search-key` is rejected with 400. Deprecated — use `search-key` instead. Free-text search over the name. This name has no length limit and `search-key` rejects values over 256 characters, so shorten anything longer before migrating. |
| `limit` | query | no | integer (int32) | Limit filters returned |
| `offset` | query | no | integer (int32) | 0-based pagination offset - the number of rows to skip before the returned page. Defaults to `0`. Combine with `limit` to walk the full collection; the total to walk is returned in the X-Total-Count response header. |
| `search-key` | query | no | string | Free-text search over the name, matched case-insensitively in the language selected by `Accept-Language`. Maximum length 256 characters. |
| `Accept-Language` | header | no | string | Language code is used for filtering by the keyword with multilingual |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Filters |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/latest/article-filters

Create a article filter

- operationId: `createArticleFilter`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of the filter. Does not need to be included when creating article filter |
| `nameDE` | string | yes | Name of the filter in german |
| `nameEN` | string | no | Name of the filter in english |
| `nameFR` | string | no | Name of the filter in french |
| `nameIT` | string | no | Name of the filter in italy |
| `order` | integer (int32) | no | Order of this filter. Does not need to be included when creating article filter. |
| `active` | boolean | no | Indicates if the filter is active or not |
| `imageId` | string | no | The image id of the filter. Does not need to be included when creating article |
| `_links` | object | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `201` | ArticleFilter | Filter created |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### DELETE /core/latest/article-filters/{filter-id}

Delete article filter by id

This filter will be removed from all articles

- operationId: `deleteArticleFilter`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `filter-id` | path | yes | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `204` | none declared | Delete successfully |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/article-filters/{filter-id}

Get article filter by id

- operationId: `getArticleFilterById`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `filter-id` | path | yes | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | ArticleFilter | Filter |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### PUT /core/latest/article-filters/{filter-id}

Update article filter by id

- operationId: `updateArticleFilter`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `filter-id` | path | yes | string |  |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of the filter. Does not need to be included when creating article filter |
| `nameDE` | string | yes | Name of the filter in german |
| `nameEN` | string | no | Name of the filter in english |
| `nameFR` | string | no | Name of the filter in french |
| `nameIT` | string | no | Name of the filter in italy |
| `order` | integer (int32) | no | Order of this filter. Does not need to be included when creating article filter. |
| `active` | boolean | no | Indicates if the filter is active or not |
| `imageId` | string | no | The image id of the filter. Does not need to be included when creating article |
| `_links` | object | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | ArticleFilter | Update successfully |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/latest/article-filters/{filter-id}/assign-to-articles

Assign filter to articles

Assign the specified article filter to multiple articles for usage in Online Booking, Online shop, and Point of sale. Only the articles entered in the request body will have the specified article filter added to their respective filters. Other articles will not be affected

- operationId: `assignArticleFilterToArticles`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `filter-id` | path | yes | string |  |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `assignedPosArticleIds` | array | no | Article ids will have the filter as a pos filter |
| `assignedOnlineShopArticleIds` | array | no | Article ids will have the filter as a online shop filter |
| `assignedBookingArticleIds` | array | no | Article ids will have the filter as a booking filter |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `204` | none declared | Successfully |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/articles

Returns article list of a company

- operationId: `listArticles`
- Required permission: `ARTICLE_READ_ONLY`
- Roles that carry it: Administrator, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung, Zeit- und Materialrapporte

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `limit` | query | no | integer (int32) | Define the limit of the article list that this API returns. For example, if a company have 20 articles in total, and this limit parameter is 5, and the offset parameter is 0, then this API will return article list containing the first 5 articles If not set, then default value of 48 is used |
| `offset` | query | no | integer (int32) | Define which position to start getting article list. For example, if a company have 20 articles in total, and this offset parameter is 5, then this API will return article list from the 5th article. If not set, then default value of 0 is used |
| `product-type` | query | no | ProductType | Filter articles with this product type. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Articles |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/latest/articles

Create a new article

- operationId: `createArticle`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of the article. Does not need to be included when creating article |
| `nameDE` | string | yes | Name of the article in German |
| `nameEN` | string | no | Name of the article in English |
| `nameFR` | string | no | Name of the article in French |
| `nameIT` | string | no | Name of the article in Italian |
| `descriptionDE` | string | no | Description of the article in German |
| `descriptionEN` | string | no | Description of the article in English |
| `descriptionFR` | string | no | Description of the article in French |
| `descriptionIT` | string | no | Description of the article in Italian |
| `extendedDescriptionDE` | string | no | Extended description of the article in German |
| `extendedDescriptionEN` | string | no | Extended description of the article in English |
| `extendedDescriptionFR` | string | no | Extended description of the article in French |
| `extendedDescriptionIT` | string | no | Extended description of the article in Italian |
| `unitDE` | string | yes | Unit of the article in German |
| `unitEN` | string | no | Unit of the article in English |
| `unitFR` | string | no | Unit of the article in French |
| `unitIT` | string | no | Unit of the article in Italian |
| `barcode` | string | no | Barcode of the article |
| `usePos` | boolean | no | Decides if this article is used for POS or not |
| `pricePeriods` | array | no | Price periods for the article. If article only has single price period, ignore and don't include validFrom and validTo in the PricePeriod. If article have different prices valid for different periods, set value for validFrom for each price period or that price period is ignored. |
| `options` | array | no | Options for the article. If specify, variants for this article will be generated. |
| `imageHrefs` | array | no | Reference uris for the images of this article if present. |
| `isArticleSet` | boolean | no | Decides if this article is an article set |
| `articleSetName` | string | no | Name of the article set. Does not need to be included if article is not an article set. |
| `defaultQuantity` | number | no | The default quantity of the article |
| `accountingTags` | array | yes | Tags used for accounting. Articles with the same tag, same VAT case and same VAT rate are grouped together in the posting |
| `articleNumber` | string | yes | Article number |
| `hasVariant` | boolean | no | Specify if the article has variants or not |
| `includedInArticleSets` | array | no | Names of the article sets that the article is included in. If the article have some variants, then only the variants will show the article set name they belong to, not the parent article (null value is shown). |
| `sellInOnlineShop` | boolean | no | Specify if the article is able to be sold on the Online shop or not |
| `isAdultArticle` | boolean | no | Specify if the article is only used for adult or not |
| `productType` | object | yes |  |
| `posCategories` | array | no | Categories used for Point of Sale of the article Provide only either id or href of each category when creating Article |
| `posFilters` | array | no | Filters used for Point of Sale of the article Provide only either id or href of each filter when creating Article |
| `onlineShopCategories` | array | no | Categories used for Online shop of the article Provide only either id or href of each category when creating Article |
| `onlineShopFilters` | array | no | Filters used for Online shop of the article Provide only either id or href of each filter when creating Article |
| `bookingCategories` | array | no | Categories used for Online Booking of the article Provide only either id or href of each category when creating Article |
| `searchTags` | array | no | Search tags make it easier for your customer to find your product in the online shop |
| `shippingInfo` | object | no |  |
| `_links` | object | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `201` | Article | Article created |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/articles/article-and-variants

Returns list of articles and treat a variant combination same as an article

- operationId: `listArticlesWithVariants`
- Required permission: `ARTICLE_READ_ONLY`
- Roles that carry it: Administrator, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung, Zeit- und Materialrapporte

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `include-quantity` | query | no | boolean | flag to include quantity in the result. |
| `limit` | query | no | integer (int32) | Define the limit of the article list that this API returns. For example, if a company have 20 articles in total, and this limit parameter is 5, and the offset parameter is 0, then this API will return article list containing the first 5 articles If not set, then default value of 48 is used |
| `offset` | query | no | integer (int32) | Define which position to start getting article list. For example, if a company have 20 articles in total, and this offset parameter is 5, then this API will return article list from the 5th article. If not set, then default value of 0 is used |
| `sell-in-booking` | query | no | boolean | flag to define sellable article for booking. |
| `sell-in-online-shop` | query | no | boolean | flag to query articles that are sold in online shop. |
| `use-pos` | query | no | boolean | flag to define article that are using in POS. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | List of article and variant |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/articles/article-numbers

Find articles by article numbers

Batch-resolve a list of article numbers to their priced article rows of the caller's company. Returns each row enriched with the price valid on `price-date` and the appropriate VAT rate. Unknown article numbers are silently skipped (returned list contains only matching rows). Requires permission `ARTICLE_READ_ONLY`.

- operationId: `listArticlesByArticleNumbers`
- Required permission: `ARTICLE_READ_ONLY`
- Roles that carry it: Administrator, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung, Zeit- und Materialrapporte

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-numbers` | query | no | array | Article numbers to look up. Repeat the parameter for each value (e.g. `?article-numbers=ART-001&article-numbers=ART-002`). At most 100 entries; each entry up to 64 characters. |
| `export-vat` | query | no | boolean | If `true`, return ABROAD (export) VAT rate; otherwise NORMAL. Defaults to false. |
| `price-date` | query | no | string (date) | Date on which prices are evaluated. Format: yyyy-MM-dd. Defaults to today when omitted. |
| `should-validate-vat` | query | no | boolean | If `true`, cross-validate each row's VAT code against the company's VAT setup on `price-date`. Defaults to false. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Matching articles |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/latest/articles/bulk

Create articles

- operationId: `createArticlesBulk`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `[]` | object | no | array of the fields below |
| `id` | string | no | Id of the article. Does not need to be included when creating article |
| `nameDE` | string | yes | Name of the article in German |
| `nameEN` | string | no | Name of the article in English |
| `nameFR` | string | no | Name of the article in French |
| `nameIT` | string | no | Name of the article in Italian |
| `descriptionDE` | string | no | Description of the article in German |
| `descriptionEN` | string | no | Description of the article in English |
| `descriptionFR` | string | no | Description of the article in French |
| `descriptionIT` | string | no | Description of the article in Italian |
| `extendedDescriptionDE` | string | no | Extended description of the article in German |
| `extendedDescriptionEN` | string | no | Extended description of the article in English |
| `extendedDescriptionFR` | string | no | Extended description of the article in French |
| `extendedDescriptionIT` | string | no | Extended description of the article in Italian |
| `unitDE` | string | yes | Unit of the article in German |
| `unitEN` | string | no | Unit of the article in English |
| `unitFR` | string | no | Unit of the article in French |
| `unitIT` | string | no | Unit of the article in Italian |
| `barcode` | string | no | Barcode of the article |
| `usePos` | boolean | no | Decides if this article is used for POS or not |
| `pricePeriods` | array | no | Price periods for the article. If article only has single price period, ignore and don't include validFrom and validTo in the PricePeriod. If article have different prices valid for different periods, set value for validFrom for each price period or that price period is ignored. |
| `options` | array | no | Options for the article. If specify, variants for this article will be generated. |
| `imageHrefs` | array | no | Reference uris for the images of this article if present. |
| `isArticleSet` | boolean | no | Decides if this article is an article set |
| `articleSetName` | string | no | Name of the article set. Does not need to be included if article is not an article set. |
| `defaultQuantity` | number | no | The default quantity of the article |
| `accountingTags` | array | yes | Tags used for accounting. Articles with the same tag, same VAT case and same VAT rate are grouped together in the posting |
| `articleNumber` | string | yes | Article number |
| `hasVariant` | boolean | no | Specify if the article has variants or not |
| `includedInArticleSets` | array | no | Names of the article sets that the article is included in. If the article have some variants, then only the variants will show the article set name they belong to, not the parent article (null value is shown). |
| `sellInOnlineShop` | boolean | no | Specify if the article is able to be sold on the Online shop or not |
| `isAdultArticle` | boolean | no | Specify if the article is only used for adult or not |
| `productType` | object | yes |  |
| `posCategories` | array | no | Categories used for Point of Sale of the article Provide only either id or href of each category when creating Article |
| `posFilters` | array | no | Filters used for Point of Sale of the article Provide only either id or href of each filter when creating Article |
| `onlineShopCategories` | array | no | Categories used for Online shop of the article Provide only either id or href of each category when creating Article |
| `onlineShopFilters` | array | no | Filters used for Online shop of the article Provide only either id or href of each filter when creating Article |
| `bookingCategories` | array | no | Categories used for Online Booking of the article Provide only either id or href of each category when creating Article |
| `searchTags` | array | no | Search tags make it easier for your customer to find your product in the online shop |
| `shippingInfo` | object | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | BulkArticleCreatingResponse | Article created |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/articles/search

Free-text search across sellable articles

Free-text search across sellable articles (and their variants) of the caller's company. Matches by name, article number, barcode, variant number and set-header name. Returns each row enriched with the price valid on `price-date` and the appropriate VAT rate. Requires permission `ARTICLE_READ_ONLY`.

- operationId: `searchSellableArticles`
- Required permission: `ARTICLE_READ_ONLY`
- Roles that carry it: Administrator, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung, Zeit- und Materialrapporte

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `export-vat` | query | no | boolean | If `true`, return ABROAD (export) VAT rate; otherwise NORMAL. |
| `keyword` | query | no | string | Renamed to `search-key`. This name is accepted until 2027-09-01; when both are sent, `search-key` wins. After that date, a request sending `keyword` without `search-key` is rejected with 400. Deprecated — use `search-key` instead. Free-text search key. Trimmed; empty/missing returns the first page unfiltered. |
| `limit` | query | no | integer | Page size (1–100). |
| `offset` | query | no | integer | 0-based pagination offset. |
| `price-date` | query | no | string (date) | Date on which prices are evaluated. Format: yyyy-MM-dd. Defaults to today when omitted. |
| `search-key` | query | no | string | Free-text search key. Trimmed; empty/missing returns the first page unfiltered. |
| `should-validate-vat` | query | no | boolean | If `true`, cross-validate each row's VAT code against the company's VAT setup on `price-date`. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Matching articles |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### DELETE /core/latest/articles/{article-id}

Delete an article

- operationId: `deleteArticle`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-id` | path | yes | string | Id of the article to be deleted |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `204` | none declared | Article deleted by given article id |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/articles/{article-id}

Get article by article id

- operationId: `getArticleById`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-id` | path | yes | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | Article | Found article |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### PUT /core/latest/articles/{article-id}

Update an existing article

- operationId: `updateArticle`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-id` | path | yes | string |  |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | no | Id of the article. Does not need to be included when creating article |
| `nameDE` | string | yes | Name of the article in German |
| `nameEN` | string | no | Name of the article in English |
| `nameFR` | string | no | Name of the article in French |
| `nameIT` | string | no | Name of the article in Italian |
| `descriptionDE` | string | no | Description of the article in German |
| `descriptionEN` | string | no | Description of the article in English |
| `descriptionFR` | string | no | Description of the article in French |
| `descriptionIT` | string | no | Description of the article in Italian |
| `extendedDescriptionDE` | string | no | Extended description of the article in German |
| `extendedDescriptionEN` | string | no | Extended description of the article in English |
| `extendedDescriptionFR` | string | no | Extended description of the article in French |
| `extendedDescriptionIT` | string | no | Extended description of the article in Italian |
| `unitDE` | string | yes | Unit of the article in German |
| `unitEN` | string | no | Unit of the article in English |
| `unitFR` | string | no | Unit of the article in French |
| `unitIT` | string | no | Unit of the article in Italian |
| `barcode` | string | no | Barcode of the article |
| `usePos` | boolean | no | Decides if this article is used for POS or not |
| `pricePeriods` | array | no | Price periods for the article. If article only has single price period, ignore and don't include validFrom and validTo in the PricePeriod. If article have different prices valid for different periods, set value for validFrom for each price period or that price period is ignored. |
| `options` | array | no | Options for the article. If specify, variants for this article will be generated. |
| `imageHrefs` | array | no | Reference uris for the images of this article if present. |
| `isArticleSet` | boolean | no | Decides if this article is an article set |
| `articleSetName` | string | no | Name of the article set. Does not need to be included if article is not an article set. |
| `defaultQuantity` | number | no | The default quantity of the article |
| `accountingTags` | array | yes | Tags used for accounting. Articles with the same tag, same VAT case and same VAT rate are grouped together in the posting |
| `articleNumber` | string | yes | Article number |
| `hasVariant` | boolean | no | Specify if the article has variants or not |
| `includedInArticleSets` | array | no | Names of the article sets that the article is included in. If the article have some variants, then only the variants will show the article set name they belong to, not the parent article (null value is shown). |
| `sellInOnlineShop` | boolean | no | Specify if the article is able to be sold on the Online shop or not |
| `isAdultArticle` | boolean | no | Specify if the article is only used for adult or not |
| `productType` | object | yes |  |
| `posCategories` | array | no | Categories used for Point of Sale of the article Provide only either id or href of each category when creating Article |
| `posFilters` | array | no | Filters used for Point of Sale of the article Provide only either id or href of each filter when creating Article |
| `onlineShopCategories` | array | no | Categories used for Online shop of the article Provide only either id or href of each category when creating Article |
| `onlineShopFilters` | array | no | Filters used for Online shop of the article Provide only either id or href of each filter when creating Article |
| `bookingCategories` | array | no | Categories used for Online Booking of the article Provide only either id or href of each category when creating Article |
| `searchTags` | array | no | Search tags make it easier for your customer to find your product in the online shop |
| `shippingInfo` | object | no |  |
| `_links` | object | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | Article | Article updated |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/articles/{article-id}/article-set-items

Get list of items from article set

- operationId: `getArticleSetWithItemsById`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-id` | path | yes | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Return successfully |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/latest/articles/{article-id}/images

Add an image to an article

Image size limit: 5MB Supported image type: PNG, JPG, JPEG

- operationId: `addArticleImage`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-id` | path | yes | string | Id of the article to add an image to |

**Request body** (`multipart/form-data`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `file` | string (binary) | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `201` | ArticleImage | Image added for article |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `415` | ErrorMessage | Unsupported Media Type Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### DELETE /core/latest/articles/{article-id}/images/{image-id}

Delete an article image

- operationId: `deleteArticleImage`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-id` | path | yes | string | Id of the article to delete an image from |
| `image-id` | path | yes | string | Id of the image to delete |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `204` | none declared | Image deleted for given article |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | The image of the article or the article itself could not be found. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/articles/{article-id}/images/{image-id}

Get the content of an article image

- operationId: `getArticleImage`
- Required permission: `ARTICLE_READ_ONLY`
- Roles that carry it: Administrator, External user, Kundenbeziehungen, Mitarbeiter und Lohn, Online Terminbuchung, Zeit- und Materialrapporte

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-id` | path | yes | string | Id of the article to get an image from |
| `image-id` | path | yes | string | Id of the image to get the content from |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/octet-stream | Content of an article image |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | The image of the article or the article itself could not be found. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### PUT /core/latest/articles/{article-id}/images/{image-id}

Update an article image

Image size limit: 5MB Supported image type: PNG, JPG, JPEG

- operationId: `updateArticleImage`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-id` | path | yes | string | Id of the article to update an image |
| `image-id` | path | yes | string | Id of the image to update |

**Request body** (`multipart/form-data`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `file` | string (binary) | no |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | ArticleImage | Image updated for article |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | The image of the article or the article itself could not be found. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `415` | ErrorMessage | Unsupported Media Type Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/articles/{article-id}/variants

Get variants of an article

- operationId: `getVariantsByArticleId`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-id` | path | yes | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Found variants |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/latest/articles/{article-id}/variants/{variant-id}

Get variants of an article with id

- operationId: `getVariantById`
- Required permission: `ARTICLE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `article-id` | path | yes | string |  |
| `variant-id` | path | yes | string |  |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | Variant | Found article variant |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

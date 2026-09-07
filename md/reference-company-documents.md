# Company documents

1 operation of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### POST /core/latest/companies/{company-id}/documents

Upload a document for a company

Uploads a single file under one of a fixed set of document categories. The tenant is derived from the bearer token; only the authenticated company's id may be used in the path. Maximum file size: 25 MB. For category LIABILITY_UPLOAD the downstream service additionally triggers asynchronous AI analysis (the upload response is returned regardless of the AI outcome). Required caller roles (enforced by the downstream service): COMPANY_ADMINISTRATOR, TRUSTED_USER, ACCOUNTANT, CUSTOMER_RELATIONSHIP_MANAGER, EMPLOYEE or MYKLARA_USER. Important: When uploading documents as part of the create booking functionality only the `LIABILITY_UPLOAD` category is allowed (the documents are automatically moved to `LIABILITIES` when the booking is created successfully via `POST /core/v1/bookings`).

- operationId: `uploadCompanyDocument`

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `company-id` | path | yes | integer (int64) | Identifier of the company that owns the document. Must match the company id carried by the bearer token, otherwise the request is rejected with 403. |

**Request body** (`multipart/form-data`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `category` | string | yes | Document category. Determines where the file is stored and which downstream post-processing is triggered (e.g. LIABILITY_UPLOAD triggers asynchronous AI analysis). Important: For the create booking functionality, only `LIABILITY_UPLOAD` is allowed — the booking creation GUI must always use `LIABILITY_UPLOAD` when uploading documents in this context. One of `SALARY_STATEMENTS`, `YEARLY_REPORTS`, `PAYMENT_FILES`, `INSURANCE_CERTIFICATES`, `SALARY_TRANSMISSIONS`, `OWN_DOCUMENTS`, `LIABILITY_UPLOAD`, `EXPENSES`, `LIABILITIES`. |
| `file` | string (binary) | yes | The document file to upload. Maximum size 25 MB. Allowed file types are enforced by the downstream service. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Document successfully uploaded. |
| `400` | ErrorMessage1 | Data invalid |
| `401` | none declared | No Authorization header found or invalid token |
| `403` | ErrorMessage1 | The current user is not allowed to access this company data |
| `413` | ErrorMessage1 | Uploaded file exceeds the 25 MB limit. |
| `415` | none declared | Unsupported Media Type |
| `429` | none declared | API rate limit exceeded |
| `500` | ErrorMessage1 | Something went wrong on our side while processing the request. Please kindly contact our support. |

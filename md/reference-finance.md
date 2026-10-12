# Finance

12 operations of the KLARA API, generated from `klara-openapi.json`. Canonical page: https://developer.klara.ch/#reference

### GET /core/v1/company-bank-accounts

List the bank accounts of the authenticated company.

Returns the bank accounts of the company resolved from the bearer JWT, optionally filtered by business `type`. The caller must hold the `FINANCE_BANK_ACCOUNT` permission on the target company. The result is sorted by Swiss-bank name then IBAN. This operation is idempotent and read-only — it can be safely retried.

- operationId: `listCompanyBankAccounts`
- Required permission: `FINANCE_BANK_ACCOUNT`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen, Mitarbeiter und Lohn

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `iban-number` | query | no | string | Optional IBAN filter. When provided, the response contains the single matching bank account (returned as a one-element array for a uniform response shape) or HTTP 404 if no account exists with that IBAN. Spaces in the IBAN are tolerated. When provided, the `type` parameter is ignored. |
| `type` | query | no | string | Optional business-type filter. Allowed values: `AR` (Accounts Receivable), `AP` (Accounts Payable), `HR` (Salary / HR payments). When omitted, all bank accounts are returned. Ignored when `iban-number` is provided. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Bank accounts of the authenticated company. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/invoices

Search and page the invoices of the authenticated company.

Returns a page of the company's invoices, newest first, with optional free-text search (`search-key`) and status filter (`status`). Each row is an `InvoiceSummary` — a flat projection carrying the identity, amounts, dates, customer and both status fields. Retrieve the full document, including order items and attachments, with `GET /core/v1/invoices/{id}`. Paging. Walk the collection with `offset` and `limit`: iterate `offset=0, limit, 2×limit, …` until a response returns fewer than `limit` rows. Ordering defaults to `sort-field=ID`, `sort-direction=DESC`, which is unique per row and therefore stable across pages — if you sort by a non-unique field instead (for example `LAST_MODIFIED`), rows sharing a value may shift between pages. The two status fields mean different things. `status` on a row is the invoice document status; `bookingStatus` is the accounting payment state of its open position, supplied by `an internal service`. A fully paid invoice reads `status: INVOICED` with `bookingStatus: PAID`. The `status` query parameter is the KLARA invoice-overview filter, not an equality filter on the row's `status` field. It matches on both the document status and the payment state, exactly as the KLARA invoice list does:`DRAFT`, `CANCELLED` — document status only.`INVOICED`, `SENT` — invoiced and still open.`OPEN` — invoiced or paid, with `bookingStatus=OPEN`. Rows come back reading `status: INVOICED` or `PAID`, so do not expect the filter value to be echoed in the row.`PAID` — `bookingStatus` of `PAID` or `PARTLY_PAID`.`CLEARED` — `bookingStatus` of `CLEARED` or `PARTLY_CLEARED` (credit notes).Use `booking-status` to filter on the payment state precisely. It matches the row's `bookingStatus` field and nothing else, accepts the full set — including `PARTLY_PAID` and `PARTLY_CLEARED`, which `status` cannot express — and combines with `status`: `?status=INVOICED&booking-status=OPEN` is the exact way to ask for "invoiced and not yet paid". Invoices that carry no booking at all (`DRAFT`, cancelled) have no payment state and are excluded whenever `booking-status` is given. Two further behaviours worth knowing. Invoices whose order has been archived are excluded. And `status=OPEN` — only that one value — applies its payment-state check after the page is cut, so such a page can contain fewer than `limit` rows while the offsets stay stable; keep paging rather than treating a short page as the end. `booking-status=OPEN` does not behave that way: it is part of the query, so a short page really is the last page. Prefer it. Read-only and idempotent. Authentication is API key + bearer JWT; the caller must hold the `FINANCE` permission on the company resolved from the JWT, otherwise the endpoint returns `403`.

- operationId: `listInvoices`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `booking-status` | query | no | string | Payment-state filter, matched against the row's `bookingStatus` field. Allowed values: `OPEN`, `PARTLY_PAID`, `PAID`, `PARTLY_CLEARED`, `CLEARED`. Independent of `status` and combinable with it. Invoices without a booking are excluded. When omitted, invoices in every payment state are returned. |
| `limit` | query | no | integer | Page size. Defaults to `50`; hard cap `100`. Requests above the cap are rejected with `400`. |
| `offset` | query | no | integer | 0-based pagination offset — the number of rows to skip before the returned page. Defaults to `0`. Combine with `limit` to walk the full collection. |
| `search-key` | query | no | string | Free-text search. The fields searched are documented per endpoint. Matches are case-insensitive substring matches. Maximum length 256 characters. |
| `sort-direction` | query | no | string | Sort direction. Allowed values: `ASC`, `DESC`. Defaults to `DESC`. |
| `sort-field` | query | no | string | Field to order the result by. The accepted values are listed per endpoint; an unknown value is rejected with `400`. Each endpoint documents the default it applies, chosen so that paging with `offset` / `limit` is stable. |
| `status` | query | no | string | Invoice-overview status filter. Allowed values: `DRAFT`, `INVOICED`, `SENT`, `PAID`, `OPEN`, `CLEARED`, `CANCELLED`. When omitted, invoices in every status are returned. See the operation description — this is not a plain equality filter on the row's `status` field. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | A page of invoices. Empty array when nothing matches. |
| `400` | ErrorMessage | Invalid filter or paging parameter — an unknown `status`, an unknown `sort-field`, a `sort-direction` other than `ASC`/`DESC`, a negative `offset`, or a `limit` outside 1–100. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/v1/invoices

Create a new KLARA invoice for the authenticated company.

Creates a new invoice for an existing customer under the company resolved from the bearer JWT. The request body's `id` must be `0` or omitted — this endpoint does not support updating existing invoices. Typical flow:Call `GET /core/v1/invoices/next-invoice-number` to obtain the next invoice number.(Optional) List existing invoices with `GET /core/v1/invoices` — filter by `status` and page with `offset`/`limit` — to check what is already booked before creating another.Look up the customer via `GET /core/v1/customers` (with search filters and pagination).Look up company bank accounts via `GET /core/v1/company-bank-accounts`.Search articles via `GET /core/latest/articles/search` or `GET /core/latest/articles/article-numbers` to populate order items.(Optional) Look up open positions via `GET /core/v1/bank-reconciliation/open-positions` to reconcile prepayments / credit notes.(Optional) Call `POST /core/v1/orders/next-order-number` to obtain the next `order.orderNumber`.(Optional) Call `GET /core/latest/company-configuration/including-vat` to determine the company default for `usingVAT`.Build the invoice payload with order items.Call this endpoint with `status=INVOICED` to save and book, or `status=DRAFT` to save without booking.On status `INVOICED` or `SENT` the invoice is also booked into KLARA accounting and inventory transactions are created. This endpoint does not send/deliver the invoice (no email/ePost/eBill/print&send), regardless of `postMethod` or a `SENT` status; retrieve the PDF via `POST /core/v1/invoices/{id}/printed-document` and deliver it yourself. The caller must hold the `FINANCE` permission on the target company. Status is caller-controlled. The KLARA GUI keeps an invoice in `DRAFT` until the user posts it in the final step; via the API you choose — send `status=DRAFT` to save without booking, or `status=INVOICED` (or `SENT`) to book immediately. A DRAFT can be finalized later with `PUT /core/v1/invoices` (`status=INVOICED`). Important — booking is created without a printed invoice. When you book via this endpoint (`status=INVOICED`/`SENT`), the accounting booking is created but no PDF is rendered or stored — the invoice and its booking have no printed document (unlike the GUI, which renders, stores and books in one step). To attach the printed invoice, run two follow-up calls: (1) `POST /core/v1/invoices/{id}/printed-document` to render the PDF and store its `printedFileId` on the invoice, then (2) `PUT /core/v1/bookings/{id}/documents` to link that `printedFileId` (in `documentIds`) to the booking returned in `bookingNumbers`.

- operationId: `createInvoice`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `confirmDontMindClosingFiscalYear` | query | no | boolean | When `true`, allows save/booking even if the document date falls inside a closing or closed fiscal year. Defaults to false. |
| `Accept-Language` | header | no | string | Preferred response language as a BCP-47 tag (e.g. `en`, `de`, `fr`, `it`). Forwarded to an internal service. |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | integer (int64) | no | Invoice id. Must be `0` or omitted when creating a new invoice. This endpoint only supports creation; updating an existing invoice is not supported. |
| `orderType` | string | no | Polymorphic discriminator. Must be `INVOICE`. Allowed values: `INVOICE`. |
| `status` | string | no | Invoice status. Allowed values: `DRAFT`, `INVOICED`, `SENT`, `PAID`, `CANCELLED`. Required. Status `INVOICED` or `SENT` triggers accounting booking. |
| `invoiceCode` | string | yes | Human-readable invoice code. Required and unique per company. |
| `invoiceNumber` | integer (int64) | no | Sequential invoice number, typically obtained from `GET /invoices/next-invoice-number`. |
| `documentDate` | string (date) | no | Invoice document date (ISO 8601). Required when `amount` is greater than 0. Must fall inside the company's active Order Management subscription period. |
| `paymentDate` | string (date) | no | Payment due date (ISO 8601). Required when `amount` is greater than 0. Must be on or after `documentDate`. |
| `deliveryDate` | string (date) | no | Service delivery date (ISO 8601). |
| `issuedDate` | string (date) | no | Date the invoice was issued (ISO 8601). |
| `servicePeriodFrom` | string (date) | no | Service period start date (ISO 8601). |
| `servicePeriodTo` | string (date) | no | Service period end date (ISO 8601). |
| `servicePeriodPattern` | string | no | Service period pattern label (e.g. `MONTHLY`, `YEARLY`). |
| `vatDate` | string (date) | no | VAT date. Server-managed (read-only). |
| `bookingDueDate` | string (date) | no | Booking due date returned by accounting. Server-managed (read-only). |
| `lastModified` | string (date-time) | no | Last modification timestamp. Server-managed (read-only). |
| `createDate` | string (date-time) | no | Creation timestamp. Server-managed (read-only). |
| `amount` | number | no | Invoice total. When `usingVAT=false` (VAT-exclusive items) this is the gross total = net + VAT; when `usingVAT=true` (VAT-inclusive items) it is the sum of the gross item amounts (VAT already inside). Caller-supplied — the server does not recompute it. |
| `usingVAT` | boolean | no | Controls whether each item `price`/`amount` is VAT-inclusive (gross) or VAT-exclusive (net). This is the GUI checkbox "VAT included in amount of each item". `true` = amounts already include VAT (INCLUDE_VAT); `false` = VAT is added on top of the net amounts (EXCLUDE_VAT). It does not turn VAT on or off — whether VAT applies at all is governed by the company's VAT registration. Use `GET /core/latest/company-configuration/including-vat` for the company default. The server stores the amounts you send verbatim and does not recompute them from this flag — send `price`/`amount` consistent with the chosen mode (see the create operation's VAT-handling notes). |
| `usingExportVat` | boolean | no | Export invoice flag — the GUI "Export" checkbox. When `true`, the invoice is treated as an export: every `orderItems[].vat.rate` must be `0` (export/zero VAT code), so no VAT is charged. When `false` (default) the items' normal VAT rates apply. The server does not recompute item VAT from this flag — set the per-item VAT accordingly. |
| `hasCompanyVat` | boolean | no | Whether the company is liable for VAT. Leave this out. The server derives it from the company's VAT registration for the invoice's effective VAT date (`deliveryDate` when set, otherwise `documentDate`) and a derived value always wins over a value sent here. It is documented only because it is accepted, and because sending `false` on a VAT-registered company used to book every position at 0% while returning 200 — this field is the master switch for VAT at booking time, ahead of any per-line VAT code. It is only consulted when the company has no VAT configuration for that date, where it cannot be derived at all. |
| `subject` | string | no | Free-text invoice subject. |
| `closeAndSignature` | string | no | Free-text closing remarks / signature block. |
| `ourReference` | string | no | Internal sender reference. |
| `yourReference` | string | no | Customer-side reference (e.g. PO number). |
| `companyCityAndDate` | string | no | Header line such as 'Zurich, 28.05.2026'. |
| `postMethod` | string | no | Distribution channel recorded on the invoice. Stored only — creating an invoice does not deliver it (no email/ePost/eBill/print&send). Allowed values: `A_POST`, `B_POST`, `SEND_EMAIL`, `PRINT_AND_MANUAL_SEND`, `EPOST`, `EBILL`. |
| `delivery` | string | no | Delivery method label (free text). |
| `ibanNumberCHF` | string | no | CHF IBAN for payment. Validated by downstream service. |
| `ibanNumberCHW` | string | no | WIR-franc IBAN for payment. |
| `wirAcceptanceRate` | number | no | WIR acceptance percentage. |
| `referenceNumber` | string | no | QR / ISR reference number. |
| `qrInvoice` | boolean | no | When `true`, the invoice is rendered/booked as a Swiss QR-invoice. Defaults to false. |
| `fromAutoInvoicing` | boolean | no | Indicates the invoice was generated by auto-invoicing. Defaults to false. |
| `fromInvoiceRun` | boolean | no | Indicates the invoice was produced by a recurring invoice run. When `true`, accounting booking and inventory transactions are skipped. Defaults to false. |
| `invoiceType` | string | no | Invoice type. Allowed values: `MANUAL`, `CREDIT_DEBIT`. |
| `originDistributionMethod` | string | no | Origin distribution method. Allowed values: `A_POST`, `B_POST`, `SEND_EMAIL`, `PRINT_AND_MANUAL_SEND`, `EPOST`, `EBILL`. |
| `templateId` | integer (int64) | no | Template id used to create this invoice. |
| `runHistoryId` | integer (int64) | no | Recurring invoice run history id. |
| `settledAmount` | number | no | KLARA-Pay settled amount (online shop only). |
| `bookingNumbers` | string | no | Accounting booking-number string. Server-managed (read-only). |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | Invoice | Invoice persisted successfully. |
| `400` | ErrorMessage | Data invalid Error codes for this response (the `code` field of the response body):`invoice.id_must_be_zero` — The request body carried a non-zero id. This operation only creates invoices; use PUT /core/v1/invoices to modify an existing one.`invoice.vat_code_missing` — At least one orderItems[] entry set VAT on vatType, vatCase or the top-level vatCode while leaving vat.vatCode empty. Only vat.vatCode is stored, so that line would have been booked at 0%. Move the code to vat.vatCode, or send "0" ("No VAT") for a position that genuinely carries none.`invoice.vat_rate_mismatch` — At least one orderItems[] entry sent a vat.rate that is not the rate of its vat.vatCode. The booking always uses the code's rate, so the invoice document would state a different percentage than the accounting record. Send the matching rate, or omit vat.rate and let the server fill it in.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Error codes for this response (the `code` field of the response body):`auth.credentials_missing` — Neither an Authorization: Bearer header nor an API key header was present on the request.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### PUT /core/v1/invoices

Update an existing DRAFT invoice for the authenticated company.

Updates a DRAFT invoice identified by `invoice.id` in the request body. Only invoices in `DRAFT` status can be updated — any other status returns 400. If the incoming `status` is `INVOICED` or `SENT`, the invoice is booked into KLARA accounting and inventory transactions are created, identical to the booking behavior of `POST /core/v1/invoices`. This is the call that finalizes a DRAFT — the GUI equivalent of posting in the final step; status is caller-controlled, so keep it `DRAFT` to re-save without booking. This endpoint does not send/deliver the invoice (no email/ePost/eBill/print&send), regardless of `postMethod`; retrieve the PDF via `POST /core/v1/invoices/{id}/printed-document` and deliver it yourself. The caller must hold the `FINANCE` permission on the target company. Important — finalizing a DRAFT books it without a printed invoice. Setting `status=INVOICED`/`SENT` here creates the accounting booking but renders/stores no PDF. To attach the printed invoice to the booking, after this call run (1) `POST /core/v1/invoices/{id}/printed-document` to render and store the `printedFileId`, then (2) `PUT /core/v1/bookings/{id}/documents` to link that `printedFileId` (in `documentIds`) to the booking from `bookingNumbers`.

- operationId: `updateInvoice`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `confirmDontMindClosingFiscalYear` | query | no | boolean | When `true`, allows save/booking even if the document date falls inside a closing or closed fiscal year. Defaults to false. |

**Request body** (`application/json`, required)

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | integer (int64) | no | Invoice id. Must be `0` or omitted when creating a new invoice. This endpoint only supports creation; updating an existing invoice is not supported. |
| `orderType` | string | no | Polymorphic discriminator. Must be `INVOICE`. Allowed values: `INVOICE`. |
| `status` | string | no | Invoice status. Allowed values: `DRAFT`, `INVOICED`, `SENT`, `PAID`, `CANCELLED`. Required. Status `INVOICED` or `SENT` triggers accounting booking. |
| `invoiceCode` | string | yes | Human-readable invoice code. Required and unique per company. |
| `invoiceNumber` | integer (int64) | no | Sequential invoice number, typically obtained from `GET /invoices/next-invoice-number`. |
| `documentDate` | string (date) | no | Invoice document date (ISO 8601). Required when `amount` is greater than 0. Must fall inside the company's active Order Management subscription period. |
| `paymentDate` | string (date) | no | Payment due date (ISO 8601). Required when `amount` is greater than 0. Must be on or after `documentDate`. |
| `deliveryDate` | string (date) | no | Service delivery date (ISO 8601). |
| `issuedDate` | string (date) | no | Date the invoice was issued (ISO 8601). |
| `servicePeriodFrom` | string (date) | no | Service period start date (ISO 8601). |
| `servicePeriodTo` | string (date) | no | Service period end date (ISO 8601). |
| `servicePeriodPattern` | string | no | Service period pattern label (e.g. `MONTHLY`, `YEARLY`). |
| `vatDate` | string (date) | no | VAT date. Server-managed (read-only). |
| `bookingDueDate` | string (date) | no | Booking due date returned by accounting. Server-managed (read-only). |
| `lastModified` | string (date-time) | no | Last modification timestamp. Server-managed (read-only). |
| `createDate` | string (date-time) | no | Creation timestamp. Server-managed (read-only). |
| `amount` | number | no | Invoice total. When `usingVAT=false` (VAT-exclusive items) this is the gross total = net + VAT; when `usingVAT=true` (VAT-inclusive items) it is the sum of the gross item amounts (VAT already inside). Caller-supplied — the server does not recompute it. |
| `usingVAT` | boolean | no | Controls whether each item `price`/`amount` is VAT-inclusive (gross) or VAT-exclusive (net). This is the GUI checkbox "VAT included in amount of each item". `true` = amounts already include VAT (INCLUDE_VAT); `false` = VAT is added on top of the net amounts (EXCLUDE_VAT). It does not turn VAT on or off — whether VAT applies at all is governed by the company's VAT registration. Use `GET /core/latest/company-configuration/including-vat` for the company default. The server stores the amounts you send verbatim and does not recompute them from this flag — send `price`/`amount` consistent with the chosen mode (see the create operation's VAT-handling notes). |
| `usingExportVat` | boolean | no | Export invoice flag — the GUI "Export" checkbox. When `true`, the invoice is treated as an export: every `orderItems[].vat.rate` must be `0` (export/zero VAT code), so no VAT is charged. When `false` (default) the items' normal VAT rates apply. The server does not recompute item VAT from this flag — set the per-item VAT accordingly. |
| `hasCompanyVat` | boolean | no | Whether the company is liable for VAT. Leave this out. The server derives it from the company's VAT registration for the invoice's effective VAT date (`deliveryDate` when set, otherwise `documentDate`) and a derived value always wins over a value sent here. It is documented only because it is accepted, and because sending `false` on a VAT-registered company used to book every position at 0% while returning 200 — this field is the master switch for VAT at booking time, ahead of any per-line VAT code. It is only consulted when the company has no VAT configuration for that date, where it cannot be derived at all. |
| `subject` | string | no | Free-text invoice subject. |
| `closeAndSignature` | string | no | Free-text closing remarks / signature block. |
| `ourReference` | string | no | Internal sender reference. |
| `yourReference` | string | no | Customer-side reference (e.g. PO number). |
| `companyCityAndDate` | string | no | Header line such as 'Zurich, 28.05.2026'. |
| `postMethod` | string | no | Distribution channel recorded on the invoice. Stored only — creating an invoice does not deliver it (no email/ePost/eBill/print&send). Allowed values: `A_POST`, `B_POST`, `SEND_EMAIL`, `PRINT_AND_MANUAL_SEND`, `EPOST`, `EBILL`. |
| `delivery` | string | no | Delivery method label (free text). |
| `ibanNumberCHF` | string | no | CHF IBAN for payment. Validated by downstream service. |
| `ibanNumberCHW` | string | no | WIR-franc IBAN for payment. |
| `wirAcceptanceRate` | number | no | WIR acceptance percentage. |
| `referenceNumber` | string | no | QR / ISR reference number. |
| `qrInvoice` | boolean | no | When `true`, the invoice is rendered/booked as a Swiss QR-invoice. Defaults to false. |
| `fromAutoInvoicing` | boolean | no | Indicates the invoice was generated by auto-invoicing. Defaults to false. |
| `fromInvoiceRun` | boolean | no | Indicates the invoice was produced by a recurring invoice run. When `true`, accounting booking and inventory transactions are skipped. Defaults to false. |
| `invoiceType` | string | no | Invoice type. Allowed values: `MANUAL`, `CREDIT_DEBIT`. |
| `originDistributionMethod` | string | no | Origin distribution method. Allowed values: `A_POST`, `B_POST`, `SEND_EMAIL`, `PRINT_AND_MANUAL_SEND`, `EPOST`, `EBILL`. |
| `templateId` | integer (int64) | no | Template id used to create this invoice. |
| `runHistoryId` | integer (int64) | no | Recurring invoice run history id. |
| `settledAmount` | number | no | KLARA-Pay settled amount (online shop only). |
| `bookingNumbers` | string | no | Accounting booking-number string. Server-managed (read-only). |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | Invoice | Invoice updated successfully. |
| `400` | ErrorMessage | Data invalid Error codes for this response (the `code` field of the response body):`invoice.vat_code_missing` — At least one orderItems[] entry set VAT on vatType, vatCase or the top-level vatCode while leaving vat.vatCode empty. Only vat.vatCode is stored, so that line would have been booked at 0%. Move the code to vat.vatCode, or send "0" ("No VAT") for a position that genuinely carries none.`invoice.vat_rate_mismatch` — At least one orderItems[] entry sent a vat.rate that is not the rate of its vat.vatCode. The booking always uses the code's rate, so the invoice document would state a different percentage than the accounting record. Send the matching rate, or omit vat.rate and let the server fill it in.Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/invoices/next-invoice-number

Get the next available invoice number for the authenticated company.

Returns the next available invoice number for the company resolved from the bearer JWT. The caller must hold the `FINANCE` permission on the target company. This operation is NOT idempotent — each successful call advances the persisted next-invoice-number counter on an internal service, so the returned value is reserved and will not be returned again. Do not retry on success.

- operationId: `getNextInvoiceNumber`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | NextInvoiceNumberResponse | Next available invoice number returned. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Resource not found Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/invoices/{id}

Get a single invoice by its numeric id.

Returns the fully-enriched invoice document for the given `id` under the company resolved from the bearer JWT. The response includes order items enriched with inventory selection data and accounting booking-status fields (`bookingStatus`, `bookingDueDate`, `bookingSealed`) populated by `an internal service`. The caller must hold the `FINANCE` permission on the target company.

- operationId: `getInvoiceById`
- Required permission: `FINANCE_GET_INVOICE`
- Roles that carry it: Administrator, Buchhalter, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `id` | path | yes | integer | Numeric primary key of the invoice to retrieve. Obtain this from the `id` field of a previously created invoice (`POST /core/v1/invoices`). |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | Invoice | Invoice found and returned. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | No invoice with the given id exists for the authenticated company. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/v1/invoices/{id}/printed-document

Return the invoice as a PDF (rendering it if needed).

Returns the invoice document as a binary `application/pdf` stream via the internal KLARA printing pipeline (`an internal service` + Aspose Words), mirroring the behaviour of the KLARA GUI. The caller must hold the `FINANCE` permission on the target company. Reuse vs. render (mirrors the KLARA GUI): if the invoice was already printed, the previously rendered PDF is fetched from storage and returned as-is; if it was not yet printed, it is rendered now and the `printedFileId` is stored on the invoice record. In both cases the response body is the binary PDF with a `Content-Disposition: attachment` header containing the suggested filename. This is a `POST` (not `GET`) because the first print persists the `printedFileId`.

- operationId: `printInvoice`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `id` | path | yes | integer | ID of the invoice to return as PDF. Must belong to the company resolved from the bearer JWT. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/pdf | Invoice PDF returned as a binary `application/pdf` stream with a `Content-Disposition: attachment` header — either freshly rendered (first print) or the previously stored PDF (already printed). |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | Invoice not found. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### POST /core/v1/invoices/{id}/send

Send a booked invoice (smart delivery or a forced channel).

Sends an already-booked invoice to its recipient, synchronously, under the company resolved from the bearer JWT. Omit `channel` for smart delivery: the system auto-routes through `EPOST`, `EBILL`, `SEND_EMAIL` and Print&Send and only fails if none can deliver. Provide `channel` to force that exact channel: if the recipient is not eligible (e.g. no email on file, not eBill-registered, Print&Send not subscribed) the call fails with 400. The invoice PDF is rendered on demand when it has not been printed yet. Prerequisites: the invoice must already be booked — `DRAFT` and cancelled invoices are rejected with 400. Create/book it via `POST /core/v1/invoices` (`status=INVOICED`) first. Not idempotent: each successful call performs a real delivery (sends an email / hands a letter to the postal channel) and overwrites the invoice's recorded post method — do not retry blindly on success. `channel` uses the same values as `Invoice.postMethod`, except `PRINT_AND_MANUAL_SEND`, which is not a deliverable channel and is rejected with 400. Authentication is API key + bearer JWT; the caller must hold the `FINANCE` permission on the target company.

- operationId: `sendInvoice`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `id` | path | yes | integer | Numeric primary key of the booked invoice to send. Must belong to the company resolved from the bearer JWT. Obtain it from the `id` of a previously created invoice (`POST /core/v1/invoices`). |
| `channel` | query | no | string | Forced distribution channel. Leave empty for smart delivery (auto-routing) — the default is no forced channel. Allowed values: `EPOST`, `EBILL`, `SEND_EMAIL`, `A_POST`, `B_POST`. `PRINT_AND_MANUAL_SEND` is rejected with 400 (not a deliverable channel). |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | SendInvoiceResult | Invoice delivered. Returns the channel actually used, whether it was smart-routed, the OneAPI delivery id and the archived PDF's printedFileId. |
| `400` | ErrorMessage | Invoice is DRAFT/cancelled, the channel is unsupported (`PRINT_AND_MANUAL_SEND`), the recipient is not eligible for the forced channel, or smart delivery found no channel. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `404` | ErrorMessage | No invoice with the given id exists for the authenticated company. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/order-documents/filter/type-and-order-number

List order-management documents by type and order number.

Returns every order-management document of the company resolved from the bearer JWT that has the given `type` and business `order-number`. Used to discover sibling documents on the same order chain (e.g. find the `DELIVERY_NOTE`s for an order number before creating an `INVOICE` from them). The caller must hold the `FINANCE` permission on the target company. This operation is idempotent and read-only — it can be safely retried.

- operationId: `listOrderDocumentsByTypeAndOrderNumber`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `order-number` | query | yes | integer | Business order number shared by all documents in the same order chain (e.g. the `order.orderNumber` returned by `POST /core/v1/invoices`). Exact match. Required. |
| `type` | query | yes | string | Order-document type to filter by. Allowed values: `OFFER`, `CONFIRMATION`, `DELIVERY_NOTE`, `INVOICE`, `CREDIT_NOTE`, `FRIENDLY_REMINDER`, `FIRST_REMINDER`, `SECOND_REMINDER`, `PAYSLIP`, `RECURRING_INVOICE_TEMPLATE`. Required. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | application/json | Matching order-management documents. |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/order-documents/{order-type}/next-document-number

Get the next document number for a given order-document type.

Returns the next available sequence number and its formatted document code for the authenticated company and the given `order-type`. Use the returned `documentCode` as `invoiceCode` / `offerCode` when subsequently creating a document with `POST /core/v1/invoices`. Side effect for `INVOICE`: the persisted counter is advanced on every call — do not call speculatively. Only meaningful for `OFFER`, `CONFIRMATION`, `DELIVERY_NOTE`, and `INVOICE`; other types always return `documentNumber = 0`. If the company has configured `MANUAL` numbering for the requested type, the downstream service returns HTTP 500 — check `GET /core/v1/order-numbering-configurations` first. The caller must hold the `FINANCE` permission on the target company.

- operationId: `getNextDocumentNumber`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `order-type` | path | yes | string | Order-document type whose counter to advance. Meaningful values: `OFFER`, `CONFIRMATION`, `DELIVERY_NOTE`, `INVOICE`. Other enum values (`CREDIT_NOTE`, `FRIENDLY_REMINDER`, `FIRST_REMINDER`, `SECOND_REMINDER`, `PAYSLIP`, `RECURRING_INVOICE_TEMPLATE`) are accepted but always return `documentNumber = 0`. An invalid value returns HTTP 400. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | OrderNumberingResult | Next document number and formatted code for the requested order type. |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/order-numbering-configurations

Get the order-numbering configuration for a document type.

Returns the numbering strategy configured for the given `order-type` of the company resolved from the bearer JWT. Use this before creating a document to decide how to supply the document number in `POST /core/v1/invoices`: `STANDARD` — server auto-assigns; `MANUAL` — set `invoiceCode` yourself; `CUSTOMISED` — set `invoiceCode` to a value matching the returned `format` pattern. If no configuration has been saved by the company administrator, a default `STANDARD` configuration is returned. The caller must hold the `FINANCE` permission on the target company. This operation is idempotent and read-only.

- operationId: `getOrderNumberingConfiguration`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Parameters**

| Name | In | Required | Type | Description |
|---|---|---|---|---|
| `order-type` | query | no | string | Document type to retrieve the numbering configuration for. Allowed values: `OFFER`, `CONFIRMATION`, `DELIVERY_NOTE`, `INVOICE`, `CREDIT_NOTE`, `FRIENDLY_REMINDER`, `FIRST_REMINDER`, `SECOND_REMINDER`, `PAYSLIP`, `RECURRING_INVOICE_TEMPLATE`. Recommended — omitting it returns a generic default with a null `orderType` field. |

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | OrderNumberingConfiguration | Numbering configuration for the requested document type. |
| `400` | ErrorMessage | Data invalid Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

### GET /core/v1/orders/next-order-number

Get the next available order number for the authenticated company.

Returns the next available business order number for the company resolved from the bearer JWT. Use the returned `nextOrderNumber` as the `orderNumber` field when subsequently creating an order. Not idempotent: each call reserves and advances the in-memory counter — call this immediately before creating the order and do not call speculatively. Gaps may appear in the sequence if the subsequent order creation fails. The caller must hold the `FINANCE` permission on the target company.

- operationId: `getNextOrderNumber`
- Required permission: `FINANCE`
- Roles that carry it: Administrator, External user, Kundenbeziehungen

**Responses**

| Status | Body | Meaning |
|---|---|---|
| `200` | OrderNextNumberResult | Next available order number for the authenticated company. |
| `401` | ErrorMessage | No Authorization header found or invalid token Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `403` | ErrorMessage | The current user is not allowed to access this company data Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `429` | ErrorMessage | API rate limit exceeded Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |
| `500` | ErrorMessage | Something went wrong on our side while processing the request. Please kindly contact our support. Failures detected by a downstream service carry that service's own `code`, which this reference does not enumerate, so the `code` field may hold a value not documented here. Treat an unrecognised code as a generic failure of the status it arrives with, and do not branch on a code this reference does not list. |

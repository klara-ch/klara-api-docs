# Common integration flows

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#use-cases

## Common integration flows

Eight things people build most often, each as the sequence of calls it actually takes. Every statement below comes from the specification. Where the specification is silent, this page says so rather than guessing.

All of these need a token or API key, and the permission named per flow. See [Authentication](https://developer.klara.ch/#authentication) and [Roles & permissions](https://developer.klara.ch/#roles).

### Creating invoices automatically

Turn an order in your system into a KLARA invoice. Five calls, of which three are lookups you can cache.

```
1. GET  /core/v1/invoices/next-invoice-number   → invoiceNumber
2. GET  /core/v1/customers?search-key=…         → the customer id
3. GET  /core/latest/articles/search?keyword=…  → the article rows for your line items
4. GET  /core/v1/company-bank-accounts          → the bank account to print on the invoice
5. POST /core/v1/invoices                       → the created invoice with its id
```

**Permissions needed for the whole flow:** `ARTICLE_READ_ONLY`, `FINANCE`, `FINANCE_BANK_ACCOUNT`, `FINANCE_GET_CUSTOMER`. **Roles that cover all of it:** Administrator, External user, Mitarbeiter und Lohn, Kundenbeziehungen.

#### What to watch out for

- **`invoiceCode` is the only required field** in the request body, and it must be unique within the company. Everything else the schema marks as optional, which does not mean the business logic accepts an empty invoice.

- **`id` must be `0` or omitted.** This endpoint creates, it never updates. Sending an existing id does not edit that invoice.

- **Step 1 is not idempotent.** Each call reserves the next number and advances the counter. Call it immediately before creating the invoice, never speculatively, or you leave gaps in the sequence.

- **`status` drives everything downstream.** Allowed values are `DRAFT`, `INVOICED`, `SENT`, `PAID` and `CANCELLED`. Create with `INVOICED` if you intend to send it, because sending rejects drafts.

- **`postMethod` is recorded, not executed.** Setting it on the invoice does not deliver anything. Delivery is a separate call, see [Sending documents](https://developer.klara.ch/#delivery).

- **Closing fiscal years are rejected** unless you pass `confirmDontMindClosingFiscalYear=true`. Decide deliberately whether your integration should be allowed to do that.

### Webshop order to customer and invoice

A new buyer in your shop becomes a KLARA customer and an invoice. This is the invoice flow above with a customer step in front and a delivery step behind.

```
1. GET  /core/v1/customers?search-key=…   → does this buyer already exist?
2. POST /core/latest/customers            → only if step 1 found nothing
3. …the five calls from Creating invoices…
4. POST /core/v1/invoices/{id}/send       → deliver it
```

**Permissions needed for the whole flow:** `ARTICLE_READ_ONLY`, `FINANCE`, `FINANCE_BANK_ACCOUNT`, `FINANCE_CREATE_CUSTOMER`, `FINANCE_GET_CUSTOMER`. **Roles that cover all of it:** Administrator, External user, Mitarbeiter und Lohn, Kundenbeziehungen.

#### What to watch out for

- **Always search before you create.** There is no upsert and no uniqueness guarantee on email. Skipping step 1 gives you duplicate customers, and duplicates in the customer master are painful to merge afterwards.

- **`customerType` is the only required field** on a customer, and it decides the shape of everything else. It is either a person or a company.

- **Step 4 is not idempotent.** Each successful call performs a real delivery. After a timeout, do not simply retry, read the invoice back first, see [Showing invoice status](https://developer.klara.ch/#uc-invoice-status).

### Handing over bookings

Post a manual journal entry from your system into KLARA Accounting.

```
1. GET  /core/v1/accounting/accounts                  → valid account codes
2. GET  /core/v1/accounting/master-vats               → VAT codes and rates
3. GET  /core/v1/accounting/business-case-templates   → the booking categories
4. POST /core/v1/bookings                             → the persisted booking
```

**Permissions needed for the whole flow:** `ACCOUNTING`, `ACCOUNTING_GET_BUSINESS_CASE_TEMPLATE`, `ACCOUNTING_GET_MASTER_VAT`. **Roles that cover all of it:** Administrator, External user, Buchhalter.

#### What to watch out for

- **At least two lines, and they must balance.** Total debit must equal total credit or the downstream service rejects the posting.

- **Required in the header:** `documentDate`, `bookingDate` and `bookingDetails`. **Required per line:** `accountCode`, `crdrType`, `description`, `amount`, `tags` and `bookingTypeCode`.

- **`amount` is always positive.** The sign is carried by `crdrType`, which is `DR` or `CR`.

- **Let the server do the VAT.** With `autoCalculateVat=true` it recalculates the way the manual booking screen does, which is safer than computing it yourself.

- **Two guards will reject otherwise valid bookings:** a date in a closing fiscal year, and a date outside the active KLARA Accounting subscription window. Both can be overridden with `confirmDontMindClosingFiscalYear` and `ignore-unsubscripted-dates`. Overriding is a business decision, not a technical one.

- **Tenant and company come from the token.** The `id` and company fields in the body are ignored.

### Attaching receipts to bookings

Upload a receipt and attach it to an existing booking. Two calls.

```
1. POST /core/latest/companies/{company-id}/documents   → the document id
2. PUT  /core/v1/bookings/{id}/documents                → attach it to the booking
```

**Permissions needed for the whole flow:** `ACCOUNTING`. **Roles that cover all of it:** Administrator, External user, Buchhalter. **Not yet established:** POST /core/latest/companies/{company-id}/documents. Plan for an Administrator key until this is confirmed.

#### What to watch out for

- **Step 1 is `multipart/form-data`, not JSON.** Both `category` and `file` are required. The allowed categories are `SALARY_STATEMENTS`, `YEARLY_REPORTS`, `PAYMENT_FILES`, `INSURANCE_CERTIFICATES`, `SALARY_TRANSMISSIONS`, `OWN_DOCUMENTS`, `LIABILITY_UPLOAD`, `EXPENSES` and `LIABILITIES`.

- **`LIABILITY_UPLOAD` behaves differently.** That category additionally triggers an asynchronous AI analysis downstream. Pick it deliberately, not as a default.

- **Maximum file size is 25 MB.** Larger uploads fail with `413`.

- **Step 2 replaces, it does not append.** Send the complete list of document ids every time. An empty array clears all attachments. If you want to add a receipt, read the current list first and send it back with yours appended.

- **Step 2 is idempotent.** Re-sending the same body produces the same result, so a retry after a timeout is safe here.

- **Only the company's own path id is accepted** in step 1. The tenant comes from the token.

### Synchronising master data

Keep customers and articles in step between KLARA and your system.

```
Customers   GET  /core/v1/customers?limit=100&offset=0     → page through them
            POST /core/latest/customers                    → create
Articles    GET  /core/latest/articles?limit=…&offset=…    → page through them
            POST /core/latest/articles                     → create
```

**Permissions needed for the whole flow:** `ARTICLE`, `ARTICLE_READ_ONLY`, `FINANCE_CREATE_CUSTOMER`, `FINANCE_GET_CUSTOMER`. **Roles that cover all of it:** Administrator, External user, Kundenbeziehungen.

#### What to watch out for

- **Paging is offset based and capped.** On customers, `limit` defaults to 50 and is capped at 100. A request above the cap is **rejected with `400`**, not quietly reduced. Iterate `offset=0, 100, 200…` until a page returns fewer rows than you asked for.

- **Both can be filtered, but differently.** Customers take `search-key`, which searches name, email, phone and customer number, and `status`, which takes `ACTIVE`, `ALL` or `ARCHIVED`. Articles take `product-type` only. For free-text article search use `GET /core/latest/articles/search` with `keyword`.

- **Without `status` you only see active customers.** A sync that omits it will never notice that a record was archived, and will keep it alive on your side forever.

- **Creating an article needs six fields:** `nameDE`, `unitDE`, `accountingTags`, `articleNumber`, `productType`. Note that the required name and unit are the German ones, whatever language you otherwise work in.

- **There is no change feed.** No webhook, no modified-since filter. A sync has to page through everything and compare, so run it on a schedule rather than continuously, and mind the rate limit.

### Showing invoice status in your CRM

Read the state of your invoices, as a filtered list or one at a time.

```
GET /core/v1/invoices?status=INVOICED&booking-status=OPEN   → invoiced and not yet paid, one page at a time
GET /core/v1/invoices/{id}                                → the full invoice including booking status
```

**Permissions needed for the whole flow:** `FINANCE`. **Roles that cover all of it:** Administrator, External user, Mitarbeiter und Lohn, Buchhalter, Kundenbeziehungen, Online Terminbuchung. **Not yet established:** GET /core/v1/invoices. Plan for an Administrator key until this is confirmed.

#### What to watch out for

- **`status` is the filter from the KLARA invoice overview,** not an equality check on the field. `status=OPEN` returns rows that read `INVOICED` or `PAID`, because it also looks at the payment state. Use `booking-status` to filter on the payment state exactly.

- **A list row and the full invoice report status differently.** For amounts of zero or more, a row reads `INVOICED` where the full invoice reads `SENT`. The full invoice uses `DRAFT`, `INVOICED`, `SENT`, `PAID` and `CANCELLED`.

- **Page with `offset` and `limit`.** Keep the default sort by id so pages stay stable while you walk them. Page size and its maximum are on the endpoint in the reference.

- **The full invoice also carries booking state:** `bookingStatus`, `bookingDueDate` and `bookingSealed`.

- **Polling is the only option today.** There are no webhooks, so poll gently and cache. See [rate limiting](https://developer.klara.ch/#limits).

- **This is also your recovery step.** After a timeout on a create or send, read the invoice back before retrying.

### Reusing finance and VAT data

Pull the VAT catalogue into your own reporting or tax logic.

```
GET /core/v1/accounting/master-vats                    → the full catalogue
GET /core/v1/accounting/master-vats?code=…             → specific VAT codes
GET /core/v1/accounting/master-vats?default-vat=true   → the company defaults
```

**Permissions needed for the whole flow:** `ACCOUNTING_GET_MASTER_VAT`. **Roles that cover all of it:** Administrator, External user, Mitarbeiter und Lohn, Buchhalter, Kundenbeziehungen.

#### What to watch out for

- **The query string switches behaviour, it does not just filter.** With `code` set you get those codes, otherwise with `default-vat=true` you get the company defaults, otherwise you get the whole catalogue ordered by rate.

- **Paging only works on the full catalogue.** `limit` and `offset` are silently ignored when `code` or `default-vat` is set. Silently, so you will not get an error, just all the rows.

- **Combine `code` with `current-period=true`** to get only rates valid today. VAT rows carry validity ranges, and using an expired rate is a real risk.

- **Labels are translatable.** Send `Accept-Language` with `de-CH`, `fr-CH`, `it-CH` or `en`, see [Response languages](https://developer.klara.ch/#languages).

### Fetching the chart of accounts

Get the account structure for your own reports or account mapping.

```
GET /core/v1/accounting/accounts                        → the master chart of accounts
GET /core/v1/accounting/accounts?legal-form=…           → only accounts for that legal form
```

**Permissions:** none of the steps checks a particular permission.

#### What to watch out for

- **This data is global, not per company.** The same chart of accounts comes back for every tenant, which is why there are no tenant or company parameters. Fetch it once and cache it.

- **`legal-form` both filters and translates.** It restricts the result to accounts that carry a translation for that legal form, and resolves the `name` field from that translation. The allowed values are listed on the endpoint in the reference.

- **No permission is declared** on this endpoint. Any authenticated token is accepted.

- **Read only, no side effects,** so it is safe to call while you are exploring.

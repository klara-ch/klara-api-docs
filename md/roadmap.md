# Known limitations

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#roadmap

## Known limitations

We open KLARA step by step rather than all at once. These are the gaps we know about, listed here so you find out before you build rather than halfway through.

- **No invoice list.** You can read a single invoice by id, but there is no collection endpoint and no status filter. Keep the invoice ids in your own system.

- **No webhooks.** There is no way to be notified of invoice or payment events, so you need to poll, and poll gently. See [rate limiting](https://developer.klara.ch/#limits).

- **No general order resource.** For webshop integrations, create a customer and an invoice instead.

- **Free-text search exists on six endpoints only.** Articles, article categories, article filters, customers, employees and open positions. Everywhere else you need to know the value you are looking for, because filtering by field is not the same as searching. See [Searching and filtering](https://developer.klara.ch/#limits) for both lists.

- **Payroll master data is not writable.** Variable salary items are writable, see [Submitting variable payroll data](https://developer.klara.ch/#payroll), but contracts and base salaries are not.

- **No appointment booking API,** despite the role name in KLARA.

- **No `PATCH`.** Partial updates require a complete `PUT`.

Separately, KLARA operates file-based interfaces for certified payroll partner systems. They are not part of this documentation. If you are a payroll partner, [contact support](https://support.klara.ch).

Are you blocked by one of these? [Tell us via support](https://support.klara.ch) and describe what you are building. That is what shapes what we open next.

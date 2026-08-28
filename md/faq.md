# FAQ

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#faq

## FAQ

Straight answers, including where the API cannot help you yet. Better to find out here than halfway through an integration.

#### Is an OpenAPI specification available?

Yes. [Download it here](https://developer.klara.ch/#openapi-spec). Use it to generate clients, mock servers or contract tests.

#### Can I list all invoices, or filter them by status?

Not yet. `GET /core/v1/invoices/{id}` returns a single invoice including `status`, `bookingStatus`, `paymentDate` and `bookingDueDate`, but there is no collection endpoint, so you need to keep the ids in your own system. See [Known limitations](https://developer.klara.ch/#roadmap).

#### Can I write variable payroll data?

Yes. Add salary items such as hours, allowances and bonuses to an employee's editable payslip. See [Submitting variable payroll data](https://developer.klara.ch/#payroll). Master data such as contracts and base salaries is still not writable through the API.

#### Is there a general order API?

No. Order endpoints exist only as a two-step partner protocol and are not part of this documentation. For webshop integrations, create a customer and an invoice instead. See [Getting started](https://developer.klara.ch/#getting-started).

#### Are there webhooks?

No. You need to poll, and poll gently. See [rate limiting](https://developer.klara.ch/#limits). If this blocks your use case, [let us know](https://support.klara.ch) and describe what you are building.

#### Can I book appointments through the API?

No. Despite the role name, KLARA Online Terminbuchung has no public endpoints. The tag *SHRM Booking* refers to salary-run bookings, not calendar appointments.

#### Why do I get a 401 even though my API key is correct?

Every endpoint that requires authentication accepts either an API key or a bearer token, so the method is not the problem. Check three things instead: the key belongs to the company whose data you are requesting, the header is spelled `X-API-KEY`, and the user behind the key holds the permission the endpoint needs. See [Roles & permissions](https://developer.klara.ch/#roles). Note that the token endpoints themselves take no authentication at all, listed under [Authentication](https://developer.klara.ch/#authentication).

#### Why do ePost and eBill appear in a KLARA API?

They are delivery channels for KLARA invoices, not separate products you need to integrate. See [Sending documents](https://developer.klara.ch/#delivery). If you are building on the ePost Communication Platform itself, you want [developer.epost.ch](https://developer.epost.ch).

#### Why is my use case not supported?

We open KLARA step by step rather than all at once. The gaps we already know about are listed under [Known limitations](https://developer.klara.ch/#roadmap). If yours is not there, [tell us via support](https://support.klara.ch) and describe what you are building. That is what shapes what we open next.

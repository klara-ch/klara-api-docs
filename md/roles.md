# Roles & permissions

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#roles

## Roles & permissions

An API key is linked to one or more KLARA roles. Any user holding that role can use the key to authenticate for that company. You create and manage keys under **Benutzer** in KLARA.

> **Warning: Least privilege matters more than usual here**
>
> A key bound to a role reaches every Public API endpoint that role unlocks, regardless of the restrictions configured for that user inside KLARA. Create one key per use case, with the narrowest role that works, and rotate keys when team members change.

### What each role reaches

A KLARA business account offers nine assignable roles, plus Object manager and myKLARA user behind a feature switch and Personal dossier with a Spay subscription. Eight of them matter for the API. The figures below count how many of the 97 documented endpoints each one can call.

> **Warning: External user is not a smaller Administrator**
>
> For the Public API the two hold **identical** permissions. The difference exists only in user management, payments and billing, none of which the API exposes. Choosing External user over Administrator therefore buys you nothing in terms of least privilege. If you want a narrow key, pick one of the specialist roles below.

| Role in KLARA | Technical name | Endpoints reachable, of 97 |
|---|---|---|
| Administrator | `company_administrator` | 94 |
| External user | `external_user` | 94 |
| Kundenbeziehungen | `customer_relationship_manager` | 75 |
| Buchhalter | `accountant` | 61 |
| Mitarbeiter und Lohn | `payroll_specialist` | 59 |
| Online Terminbuchung | `booking` | 48 |
| Zeit- und Materialrapporte | `reporter` | 21 |
| Objektmanager | `object_manager` | 13 |

Administrator and External user reach everything, which is exactly why neither belongs on an integration key unless the integration genuinely needs everything.

### Which role carries which permission

Sixteen permissions are in use across the documented endpoints. The **Endpoints** column counts how many of them check that permission. A tick means the role grants it.

| Permission | Endpoints | Administrator | External user | Mitarbeiter und Lohn | Buchhalter | Kundenbeziehungen | Zeit- und Materialrapporte | Online Terminbuchung | Objektmanager |
|---|---|---|---|---|---|---|---|---|---|
| `FINANCE` | 22 | ✓ | ✓ | ✓ | ✓ | ✓ | – | ✓ | – |
| `ARTICLE` | 21 | ✓ | ✓ | – | – | ✓ | – | – | – |
| `ACCOUNTING` | 13 | ✓ | ✓ | – | ✓ | – | – | – | – |
| `ARTICLE_READ_ONLY` | 7 | ✓ | ✓ | ✓ | – | ✓ | ✓ | ✓ | – |
| `COMPENSATION` | 4 | ✓ | ✓ | ✓ | ✓ | ✓ | – | ✓ | – |
| `ACCOUNTING_GET_MASTER_VAT` | 3 | ✓ | ✓ | ✓ | ✓ | ✓ | – | – | – |
| `ACCOUNTING_GET_BUSINESS_CASE_TEMPLATE` | 2 | ✓ | ✓ | ✓ | ✓ | – | – | – | – |
| `FINANCE_COMPANY_VAT` | 2 | ✓ | ✓ | ✓ | ✓ | – | – | – | – |
| `FINANCE_GET_CUSTOMER` | 2 | ✓ | ✓ | ✓ | – | ✓ | – | ✓ | – |
| `ACCOUNTING_GET_OPEN_POSITION` | 1 | ✓ | ✓ | – | ✓ | ✓ | – | – | – |
| `ACCOUNTING_INTERFACE` | 1 | ✓ | ✓ | ✓ | – | – | – | – | – |
| `COMPENSATION_COMPANY_INFO` | 1 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| `COMPENSATION_TIME_TRACKING` | 1 | ✓ | ✓ | ✓ | – | – | ✓ | – | – |
| `FINANCE_BANK_ACCOUNT` | 1 | ✓ | ✓ | ✓ | ✓ | ✓ | – | – | – |
| `FINANCE_CREATE_CUSTOMER` | 1 | ✓ | ✓ | ✓ | – | ✓ | – | – | – |
| `STORE_CUSTOMER` | 1 | ✓ | ✓ | ✓ | ✓ | ✓ | – | ✓ | – |

Two things stand out. `ARTICLE` sits with Kundenbeziehungen alone, so only that role can write articles. And `COMPENSATION_COMPANY_INFO` is the single permission every role holds.

### Three rules that decide whether a call succeeds

**Roles add up.** A user can hold several roles at once and gets the sum of their access. No role ever takes something away.

**A permission replaces, it does not extend.** Where an endpoint requires `ARTICLE_READ_ONLY`, a token holding only `ARTICLE` is not accepted, and the reverse is equally true. Almost every endpoint checks exactly one permission. One does not: `POST /core/v2/tenants/companies` lists two, and whether both are required is not confirmed. Its reference entry says so.

**Every endpoint names the roles that carry its permission.** Under **Required permission** you find not only the constant but also the roles that grant it, so you do not have to work backwards from a list of constants.

**The role alone is not enough.** The company also needs the matching subscription. Without it the function stays closed even for an Administrator. If a call fails although the role looks right, check the subscription first.

### How long a role and an endpoint stay

New endpoints are versioned explicitly and start on `/core/v1`. There will be no further additions under `/core/latest`, because a moving target cannot be integrated against safely. Once an endpoint is marked deprecated it **stays available for twelve months**. That commitment covers new endpoints; the older `/core/latest` and `/core/v2` operations predate it and no policy has been applied to them yet.

### Which permission does an endpoint need?

Every endpoint in the [reference](https://developer.klara.ch/#reference) states it under **Required permission**. The values come from a source code analysis of the API, not from the specification text, which names them only sporadically.

#### Two consequences worth knowing before you design

- **Only Kundenbeziehungen can write articles.** Every other role reaches at most `ARTICLE_READ_ONLY`. An integration that maintains articles needs that role or an Administrator.

- **Online Terminbuchung can change and delete customers but not create them.** The role holds `FINANCE` and `FINANCE_GET_CUSTOMER`, but not `FINANCE_CREATE_CUSTOMER`, and creating a customer checks that one instead of `FINANCE`.

### Endpoints that need no particular permission

A valid token is enough for these. They return data that is not tied to one company, such as the chart of accounts, booking types, VAT types and cities.

| Endpoint |
|---|
| GET `/core/latest/cities/{city-id}` |
| GET `/core/latest/vat-cases` |
| GET `/core/latest/vat-cases/{vat-case-id}` |
| GET `/core/v1/accounting/accounts` |
| GET `/core/v1/accounting/accounts/by-code/{accountCode}` |
| GET `/core/v1/accounting/booking-types` |
| GET `/core/v1/accounting/vat-types` |
| GET `/core/v1/accounting/vat-types/{vatTypeId}` |

The three token endpoints and `POST /core/latest/tenants` also need no permission, for the obvious reason that they are how a token is obtained. See [Authentication](https://developer.klara.ch/#authentication).

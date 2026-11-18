# Roles & permissions

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#roles

## Roles & permissions

An API key is linked to one or more KLARA roles. Any user holding that role can use the key to authenticate for that company. You create and manage keys under **Benutzer** in KLARA.

> **Warning: Least privilege matters more than usual here**
>
> A key bound to a role reaches every Public API endpoint that role unlocks, regardless of the restrictions configured for that user inside KLARA. Create one key per use case, with the narrowest role that works, and rotate keys when team members change.

### What each role reaches

A KLARA business account offers nine assignable roles, plus Object manager and myKLARA user behind a feature switch and Personal dossier with a Spay subscription. Thirteen roles can use the API. The figures below count how many of the documented endpoints each one can call.

> **Warning: Externer Benutzer is not a smaller Administrator**
>
> For the Public API the two hold **identical** permissions. The difference exists only in user management, payments and billing, none of which the API exposes. Choosing Externer Benutzer over Administrator therefore buys you nothing in terms of least privilege. If you want a narrow key, pick one of the specialist roles below.

| Role in KLARA | Technical name | Endpoints reachable, of 114 | On an API key |
|---|---|---|---|
| Administrator | `company_administrator` | 114 | yes |
| Externer Benutzer | `trusted_user` | 114 | yes |
| Kundenbeziehungen | `customer_relationship_manager` | 75 | yes |
| Buchhalter | `accountant` | 59 | yes |
| Mitarbeiter und Lohn | `payroll_specialist` | 37 | yes |
| Zeit- und Materialrapporte | `reporter` | 21 | yes |
| Online Terminbuchung | `booking` | 21 | yes |
| ePost Briefkasten | `epost_letterbox` | 17 | yes, ePost My Business only |
| SmartSend | `smart_send` | 15 | yes, ePost My Business only |
| Mitarbeiter | `employee` | 15 | no, user login only (every employee has it automatically) |
| Print & Send | `print_and_send_basic` | 14 | no, user login only (no longer offered) |
| myKLARA Benutzer | `myklara_user` | 14 | yes, behind a feature switch |
| Objektmanager | `object_manager` | 13 | yes, if asset management is enabled |

Administrator and Externer Benutzer reach everything, which is exactly why neither belongs on an integration key unless the integration genuinely needs everything.

### API key or user login?

What a call can do depends on how it signs in.

- **API key:** the call can do exactly what the roles chosen for the key allow.

- **User login** (`POST /core/latest/token`): the call can do everything the user's roles allow. This includes two roles that cannot be chosen for an API key: *Mitarbeiter* (every employee has it automatically) and *Print & Send* (no longer offered, but some users still have it). A user can therefore reach endpoints that no API key reaches.

- If a call sends both an API key and a user token, the API key is used.

Two endpoints check the role itself, not a permission: the document upload (`POST /core/latest/companies/{company-id}/documents`) and marking a document booking as paid (`POST /core/v1/accounting/document-bookings/paid`). Their pages list the roles that are allowed.

### Which role carries which permission

Nineteen permissions are in use across the documented endpoints. The **Endpoints** column counts how many of them check that permission. A tick means the role grants it.

| Permission | Endpoints | Administrator | Externer Benutzer | Mitarbeiter und Lohn | Buchhalter | Kundenbeziehungen | Zeit- und Materialrapporte | Online Terminbuchung | Objektmanager | ePost Briefkasten | SmartSend | Mitarbeiter | Print & Send | myKLARA Benutzer |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| `ACCOUNTING` | 29 | ✓ | ✓ | – | ✓ | – | – | – | – | – | – | – | – | – |
| `ARTICLE` | 21 | ✓ | ✓ | – | – | ✓ | – | – | – | – | – | – | – | – |
| `FINANCE` | 20 | ✓ | ✓ | – | – | ✓ | – | – | – | – | – | – | – | – |
| `ARTICLE_READ_ONLY` | 7 | ✓ | ✓ | ✓ | – | ✓ | ✓ | ✓ | – | – | – | – | – | – |
| `COMPENSATION` | 4 | ✓ | ✓ | ✓ | – | – | – | – | – | – | – | – | – | – |
| `FINANCE_GET_CUSTOMER` | 4 | ✓ | ✓ | – | ✓ | ✓ | – | – | – | ✓ | – | – | – | – |
| `ACCOUNTING_GET_MASTER_VAT` | 3 | ✓ | ✓ | ✓ | ✓ | ✓ | – | – | – | – | – | – | – | – |
| `ACCOUNTING_GET_BUSINESS_CASE_TEMPLATE` | 2 | ✓ | ✓ | ✓ | ✓ | – | – | – | – | – | – | – | – | – |
| `FINANCE_BANK_ACCOUNT` | 2 | ✓ | ✓ | ✓ | ✓ | ✓ | – | – | – | – | – | – | – | – |
| `FINANCE_COMPANY_GET_VAT` | 2 | ✓ | ✓ | ✓ | ✓ | ✓ | – | – | – | – | – | – | – | – |
| `LUZ_PROFILE` | 2 | ✓ | ✓ | ✓ | ✓ | – | – | – | – | ✓ | ✓ | – | – | – |
| `ACCOUNTING_GET_OPEN_POSITION` | 1 | ✓ | ✓ | – | ✓ | ✓ | – | – | – | – | – | – | – | – |
| `ACCOUNTING_INTERFACE` | 1 | ✓ | ✓ | ✓ | – | – | – | – | – | – | – | – | – | – |
| `COMPENSATION_COMPANY_INFO` | 1 | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | – | ✓ | ✓ | ✓ | ✓ |
| `COMPENSATION_TIME_TRACKING` | 1 | ✓ | ✓ | ✓ | – | – | ✓ | – | – | – | – | ✓ | – | – |
| `FINANCE_COMPANY_VAT` | 1 | ✓ | ✓ | ✓ | ✓ | – | – | – | – | – | – | – | – | – |
| `FINANCE_CREATE_CUSTOMER` | 1 | ✓ | ✓ | – | – | ✓ | – | – | – | – | – | – | – | – |
| `FINANCE_GET_INVOICE` | 1 | ✓ | ✓ | – | ✓ | ✓ | – | – | – | – | – | – | – | – |
| `STORE_CUSTOMER` | 1 | ✓ | ✓ | ✓ | ✓ | ✓ | – | ✓ | – | – | – | – | ✓ | – |

One thing stands out. Apart from Administrator and Externer Benutzer, `ARTICLE` sits with Kundenbeziehungen alone, so only that role can write articles.

### Three rules that decide whether a call succeeds

**Roles add up.** A user can hold several roles at once and gets the sum of their access. No role ever takes something away.

**A permission replaces, it does not extend.** Where an endpoint requires `ARTICLE_READ_ONLY`, a token holding only `ARTICLE` is not accepted, and the reverse is equally true. Almost every endpoint checks exactly one permission. Where an endpoint lists two, both are required, and its reference entry says so.

**Every endpoint names the roles that carry its permission.** Under **Required permission** you find not only the constant but also the roles that grant it, so you do not have to work backwards from a list of constants.

**The role alone is not enough.** The company also needs the matching subscription. Without it the function stays closed even for an Administrator. If a call fails although the role looks right, check the subscription first.

### How long a role and an endpoint stay

New endpoints are versioned explicitly and start on `/core/v1`. There will be no further additions under `/core/latest`, because a moving target cannot be integrated against safely. Once an endpoint is marked deprecated it **stays available for twelve months**. That commitment covers new endpoints; the older `/core/latest` and `/core/v2` operations predate it and no policy has been applied to them yet.

### Which permission does an endpoint need?

Every endpoint in the [reference](https://developer.klara.ch/#reference) states it under **Required permission**. The values come from a source code analysis of the API, not from the specification text, which names them only sporadically.

#### Two consequences worth knowing before you design

- **Only Kundenbeziehungen can write articles.** Every other role reaches at most `ARTICLE_READ_ONLY`. An integration that maintains articles needs that role or an Administrator.

- **Buchhalter reads invoices one at a time, but cannot list or write them.** The role holds `FINANCE_GET_INVOICE`, which the single-invoice call checks, but not `FINANCE`, which the invoice list and every invoice write check. An integration that creates or sends invoices needs Administrator, Externer Benutzer or Kundenbeziehungen.

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

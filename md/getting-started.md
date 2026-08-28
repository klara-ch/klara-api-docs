# Getting started

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#getting-started

## Getting started

1. **Prerequisites**

   An active KLARA account with a company set up, a user role that allows managing users, and an HTTP client. HTTP/1.0 is no longer supported, so use HTTP/1.1 or HTTP/2.

2. **Create an API key in KLARA**

   Go to *Benutzer*, add a new API key, give it a name and assign at least one KLARA user role. Suggested naming convention: `integration-payroll-prod`.

3. **Choose roles and permissions**

   Assign only the roles your use case needs. Any user holding that role can authenticate with the key, and the role cannot be changed afterwards, so pick it deliberately. See [Roles & permissions](https://developer.klara.ch/#roles).

   > **Note: Which role does this guide need?**
   >
   > The calls below require `ARTICLE_READ_ONLY`. These roles carry it: Zeit- und Materialrapporte, Online Terminbuchung, Mitarbeiter und Lohn, Kundenbeziehungen, Administrator, External user.
   >
   > **Zeit- und Materialrapporte** is the narrowest of them, so it is the least privilege choice for working through this page. Watch out for one that sounds right but is not: **Accountant** does not hold `ARTICLE_READ_ONLY`, so it cannot run the first call.

4. **Set up authentication**

   Send the key as an `X-API-KEY` header, or obtain a bearer token via the token flow. See [Authentication](https://developer.klara.ch/#authentication).

5. **Make your first API call**

   List your articles. This is a read-only request that touches no data.

   ```
   curl -X GET "https://api.klara.ch/core/latest/articles" \
     -H "X-API-KEY: $KLARA_API_KEY" \
     -H "Accept: application/json"
   ```

   A successful call returns `200` with a JSON array. If you get `401`, check the header name and that the key is assigned to a role. See [Authentication](https://developer.klara.ch/#authentication).

   This example only reads, which is deliberate: there is no separate test environment, so every call works on your live data. See [Environments](https://developer.klara.ch/#limits).

6. **Where to go next**

   Browse the reference by domain, or read the guides on errors, pagination and versioning.

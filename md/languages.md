# Getting responses in German, French, Italian or English

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#languages

## Getting responses in German, French, Italian or English

Switzerland has four language regions, and so does your customer base. A number of endpoints return content that is translated, for example account names, VAT descriptions, booking-type labels and salary-item descriptions. You choose the language with the standard `Accept-Language` request header.

```
curl -X GET "https://api.klara.ch/core/v1/accounting/accounts" \
  -H "X-API-KEY: $KLARA_API_KEY" \
  -H "Accept-Language: fr-CH" \
  -H "Accept: application/json"
```

The header takes an IETF language tag. The tags documented in the specification are `de-CH`, `fr-CH`, `it-CH` and `en`. Where a translation is missing, the response falls back to German. Some endpoints fall back to the language configured on the tenant instead when you omit the header, so pass it explicitly if you care about the result.

### Where it applies

23 endpoints accept `Accept-Language`. Look for it in the parameter list of any endpoint in the [API reference](https://developer.klara.ch/#reference). It is most common across accounting: accounts, booking types, VAT types, VAT cases, business-case templates and financial years. It also affects `POST /core/v1/bookings`, where it selects the language of localised error messages, and the payroll endpoint that lists addable salary items.

### Building your own language picker

Two schemas return not just the resolved text but the complete set of translations, keyed by language tag. Use these when your own interface offers a language switch and you would rather not call the API once per language.

| Schema | Fields |
|---|---|
| `PublicApiBusinessCaseTemplate` | `i18n`, `keywordI18ns` |
| `PublicApiVatType` | `i18n`, `i18nShortName` |

> **Note: This documentation is in English**
>
> The API specification is maintained in English, so the endpoint reference is English too.

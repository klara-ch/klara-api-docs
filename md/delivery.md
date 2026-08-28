# Sending documents with smart delivery

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#delivery

## Sending documents with smart delivery

You do not have to decide how an invoice reaches your customer. Book it, then call `POST /core/v1/invoices/{id}/send` without a channel. KLARA routes it through `EPOST`, `EBILL`, `SEND_EMAIL` and Print&Send, and only fails if none of them can deliver.

### Smart delivery

Omit the `channel` query parameter. There is no request body.

```
curl -X POST "https://api.klara.ch/core/v1/invoices/42/send" \
  -H "X-API-KEY: $KLARA_API_KEY" \
  -H "Accept: application/json"
```

### Forcing one channel

Pass `channel` as a query parameter to force exactly that channel. If the recipient is not eligible for it, for example no email on file, not registered for eBill, or Print&Send not subscribed, the call fails with `400`.

```
curl -X POST "https://api.klara.ch/core/v1/invoices/42/send?channel=SEND_EMAIL" \
  -H "X-API-KEY: $KLARA_API_KEY" \
  -H "Accept: application/json"
```

| Value | Channel | Can be forced |
|---|---|---|
| `SEND_EMAIL` | Email to the recipient | yes |
| `EPOST` | ePost digital letterbox | yes |
| `EBILL` | eBill, straight into the recipient's e-banking | yes |
| `A_POST` | Printed and posted, A Post | yes |
| `B_POST` | Printed and posted, B Post | yes |
| `PRINT_AND_MANUAL_SEND` | Printed for manual dispatch | **no**, rejected with `400` |

`PRINT_AND_MANUAL_SEND` is a value you may see on `Invoice.postMethod`, but it is not a deliverable channel and cannot be forced.

> **Warning: Two things that will bite you**
>
> - **The invoice must already be booked.** Drafts and cancelled invoices are rejected with `400`. Create and book it first through `POST /core/v1/invoices` with `status=INVOICED`.
>
> - **This call is not idempotent.** Every successful call performs a real delivery, sending an email or handing a letter to the postal channel, and overwrites the invoice's recorded post method. Do not retry blindly after a success. Remember there is no test environment, so this happens on live data.

Requires the `FINANCE` permission on the target company. The invoice PDF is rendered on demand if it has not been printed yet.

> **Note: Why ePost and eBill appear here**
>
> KLARA and ePost are both products of ePost Service AG. ePost and eBill are two of the delivery channels KLARA can use. You reach them through the KLARA API, with no separate integration. Building on the ePost Communication Platform itself? See [developer.epost.ch](https://developer.epost.ch).

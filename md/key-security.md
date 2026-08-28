# Keeping your API key safe

Part of the KLARA API documentation. Canonical page: https://developer.klara.ch/#key-security

## Keeping your API key safe

An API key authenticates as a role inside your company. Treat it like a password.

- **One key per use case.** Name it so you can identify it later, for example `integration-payroll-prod`. When something goes wrong you can revoke one integration instead of all of them.

- **Never in client-side code.** Not in a browser, not in a mobile app, not in anything a user can read. The key belongs on your server.

- **Never in a repository.** Use environment variables or a secret manager. A key committed once stays in the Git history even after you delete the line.

- **Separate keys for development and live operation.** There is only one environment, so the key name is what tells them apart. Delete the development key when you go live.

- **Rotate on staff changes.** Anyone holding the assigned role can use the key. When someone leaves the team, rotate.

- **Revoke what you no longer use.** Delete keys under *Benutzer* in KLARA as soon as an integration is retired.

### What the platform does and does not do for you

| Question | Answer |
|---|---|
| Do keys expire? | **No.** A key stays valid until someone deletes it |
| Can you revoke one yourself? | Yes, with immediate effect. In KLARA under **Benutzer**, panel **Public API Key Management**. A company administrator does this without support |
| Can you see whether a key is in use? | Yes. The same panel shows a **last used** timestamp per key. One timestamp, not a call-by-call log |
| How many keys may a company create? | No limit |
| Can you change the role of an existing key? | **No.** The role is fixed when the key is created and there is no edit path. To change it, create a new key and delete the old one |

> **Warning: Keys do not expire, so rotation is entirely on you**
>
> A key that reaches a repository or a log stays valid indefinitely and nothing signals the exposure. Put a rotation interval in your own calendar, and check the last used column when you suspect a key is no longer needed.

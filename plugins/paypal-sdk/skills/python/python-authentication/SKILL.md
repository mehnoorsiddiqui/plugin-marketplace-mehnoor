---
name: python-authentication
description: Authentication for an APIMatic-generated Python SDK — supplying credentials in typed or dict form, the managed OAuth2 token lifecycle and its caching, what a failed token fetch actually raises (and why it bypasses the non-raising response mode), and loading secrets safely. Load before wiring credentials into the client, or when a call fails with 401/403.
---

# Authenticating an APIMatic Python SDK client

Each security scheme the API declares appears as a **keyword argument on the client constructor**,
taking either a typed credentials model or a plain dict of the same keys. Set the one(s) your API
uses, then construct the client (see `python-client-initialization`).

> `{...}` is a placeholder for a name from your SDK — replace it with the concrete identifier.
> Which schemes a given SDK accepts is decided by **the client's constructor keywords**, which the
> contract sheet lists. The `core/auth/schemes/` directory ships *every* scheme the generator
> supports regardless of what this API uses, so never infer the scheme from that directory.

## The two spellings

Every credential accepts a typed model or a mapping, and they are exactly equivalent — a `coerce`
classmethod is the single place the dict form is resolved, so the two cannot produce different
requests:

```python
from {root_package}.core import ClientCredentials

client = Client(oauth2=ClientCredentials(client_id=cid, client_secret=secret))
client = Client(oauth2={"client_id": cid, "client_secret": secret})
```

Prefer the typed form in application code — it is what your type checker can verify. The dict form is
for config-driven construction, where the keys arrive from a settings object.

Credential models are frozen with **`extra="forbid"`**, so the dict form is checked properly: a
misspelled or unexpected key raises `pydantic.ValidationError` at construction rather than being
silently dropped. `ValidationError` subclasses `ValueError`, so an existing `except ValueError`
around your startup path already catches it.

## The scheme shapes

Only the ones your SDK's constructor exposes are relevant; the classes live under
`{root_package}.core`.

**OAuth 2.0 client credentials** (machine-to-machine — the common server-SDK case):

```python
from {root_package}.core import ClientCredentials

oauth2=ClientCredentials(client_id=..., client_secret=..., scopes=["a", "b"])
```

`scopes` is a **list**, and the SDK joins it with the space delimiter RFC 6749 §3.3 requires — never
pre-join it yourself, or you send one scope literally named `"a b"`. Omit it to get whatever the
client's default scopes are.

**Basic**: `BasicAuthCredentials(username=..., password=...)`.
**Bearer / API key**: the constructor keyword takes the token or key **as a plain string**, not a
model.
**Other OAuth2 grants** (`password`, `authorizationCode` with PKCE) ship as
`PasswordCredentials` / `AuthorizationCodeCredentials`; the authorization-code grant additionally
takes a prompt callable that receives the authorization URL and returns the code.

## The managed OAuth2 token lifecycle

You never request a token yourself. Set the credentials and the SDK does the rest — but *when* it
does is the part that matters:

- **The token is fetched lazily, on the first authenticated call** — not at construction. A client
  built with bad credentials constructs perfectly happily; the failure surfaces at the first
  operation.
- The fetch is a `POST` to the token endpoint derived from the client's `base_url`, form-encoded, with
  the client id and secret sent per the placement the spec declares (commonly HTTP Basic,
  `client_secret_basic`, both halves percent-encoded before base64).
- The resulting token is **cached in memory on the client's auth scheme** and reused across calls,
  which is the whole reason a client must be long-lived (`python-client-initialization`).
- A token with an `expires_in` is renewed before expiry. Without one, there is no client-side
  deadline and the token is used until the server rejects it.
- **On a `401`, the cached token is invalidated** so the next call re-authenticates — but **the failing
  request is not retried**. This is deliberate: the caller sees one `401` and then recovery. Do not
  write a retry loop *just* for this; do expect that a revoked credential surfaces as exactly one
  failed call.

### Overriding token acquisition

`oauth2_token_source=` replaces how the token is obtained — for a token broker, a sidecar, a
pre-issued token, or a test double. It takes anything satisfying the token-source protocol
(`fetch(credentials) -> OAuthToken`; awaited for the async client). Use it rather than reaching into
the scheme's cache, which is private.

## The failure mode that surprises everyone

**A failed token fetch raises the SDK's ordinary API exception — but its payload is a different type
than any operation's.** For a managed-OAuth SDK, the token endpoint's error body is fixed by
RFC 6749 §5.2, so the SDK decodes it into an `OAuthProviderError` (`error`, `error_description`,
`error_uri`) — or a `RawError` if the provider's body does not conform.

Two consequences, and both are the sort of thing that costs an afternoon:

1. **The exception surfaces out of your *operation* call**, because that is where the lazy fetch
   happens. A traceback for bad credentials points at `create_order`, and the frames underneath name
   the token source. Reading only the top frame sends you looking in the wrong place entirely.
2. **The non-raising response mode does not protect you.** The `with_raw_response` variant returns a
   result object instead of raising *for the operation* — but the token fetch calls `.unwrap()`
   internally, so an auth failure raises even there. Any code path that touches the SDK needs to
   handle this, including one written specifically to avoid exceptions.

So a boundary that catches only the operation's error union is incomplete. Narrow on the payload
type:

```python
from {root_package}.core import ApiError, OAuthProviderError, RawError

try:
    order = client.orders.create_order(body)
except ApiError as e:
    if isinstance(e.error, OAuthProviderError):
        # Credentials/token problem — no operation request was ever sent.
        log.error("auth failed: %s (%s)", e.error.error, e.error.error_description)
        raise ConfigurationError("PayPal credentials rejected") from e
    ...  # the operation's own error union
```

Treat this as a **configuration** failure, distinct from an operation rejection: nothing was
attempted, so retrying it or reporting it as a failed order are both wrong. `python-error-handling`
has the full catch ladder.

## Loading secrets

Keep credentials out of source and out of logs.

```python
import os

client_id = os.environ["PAYPAL_CLIENT_ID"]          # KeyError names the missing var
client_secret = os.environ["PAYPAL_CLIENT_SECRET"]
```

Prefer `os.environ[...]` over `os.getenv(...)` at startup: `getenv` returns `None`, which then travels
into the credential model and fails later with a message about validation rather than about your
deployment. If you want a friendlier message, check explicitly and fail fast — an obviously-missing
credential should never reach the first API call.

For anything larger than a script, read secrets through the project's existing settings layer
(`pydantic-settings`, `django.conf.settings`, a secret manager client) rather than reaching for
`os.environ` in the middle of a module.

Two things that help, both already built in:

- **Secrets are hidden in reprs.** The secret fields on credential models are declared `repr=False`,
  so logging a credential object or an SDK object that holds one does not print the secret. Do not
  rely on this for values you assemble yourself.
- **Never log a credential's `model_dump()`** — that *does* include the secret. The repr protection is
  on the repr only.

## Notes

- **Omitting the credentials keyword is legal and silent.** The client is then built with a no-auth
  scheme: requests go out unauthenticated and the API answers `401`. Nothing warns you at
  construction. If your integration is getting blanket `401`s, verify the keyword is actually set
  before suspecting the credentials themselves.
- Set credentials **at construction**. The scheme is built there; assigning to a private attribute
  afterwards is not supported. To rotate a credential, build a new client and close the old one.
- Rotation with a long-lived client: the token cache is per client instance, so swapping in a new
  client atomically (build, then replace the reference, then close the old one) rotates without a
  window where calls have no credential.

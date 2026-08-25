---
name: python-calling-endpoints
description: Calling operations on an APIMatic-generated Python SDK — finding the controller, the positional/keyword-only split, passing a body as a model or a dict, the two response modes (raising vs ApiResult), per-call request options, async usage, and paging. Load before writing the first call to an SDK operation.
---

# Calling endpoints on an APIMatic Python SDK

Operations are methods on **controller attributes** of the client, named in `snake_case`:
`client.{api_group}.{operation}(...)`. An operation belonging to no group sits directly on the
client. The controller attribute, the exact operation name and its signature come from the contract
sheet — operation names follow no fixed verb/resource pattern, so take the real name from the sheet,
never from memory.

> `{...}` is a placeholder for a name from your SDK.

## Signature convention

```python
def {operation}(
    self,
    {positional params},               # path params, and sometimes the body
    *,
    {keyword-only params} = {default},  # headers, query params, the body, request_options
) -> {ReturnType}: ...
```

Three facts that decide how you write the call:

- **Every *required* parameter is positional** — which is path parameters, but not *only* them. A
  required query parameter is positional too, and in this SDK three operations have one:
  `search_transactions(start_date, end_date)` (both query, no path parameter at all),
  `list_subscription_transactions(id, start_time, end_time)` (`id` is the path, the rest query), and
  `list_customer_payment_tokens(customer_id)` — where `customer_id` is a **query** parameter even
  though it reads like a path segment (the URL is `/v3/vault/payment-tokens`, no placeholder). Take
  the boundary from the sheet; do not infer it from the path.
- **Everything after `*` is keyword-only, and every one of them has a real default.** This is the
  important difference from the .NET generator: there is no parameter you must pass `None` to just to
  reach a later one. Omit what you do not need. Writing `pay_pal_mock_response=None,
  pay_pal_auth_assertion=None` explicitly is harmless but pure noise — and it obscures the parameters
  you actually meant to set.
- **The body's position varies per operation, and the minority case is the positional one.** Take it
  from the sheet; this is where a call reconstructed from memory goes wrong silently. In this SDK the
  40 operations split three ways:

  | Shape | Count | Which |
  |---|---|---|
  | `body` **positional** and **required** | 4 | `create_order`, `create_order_tracking`, `create_payment_token`, `create_setup_token` |
  | `body` **keyword-only**, `= None` | 18 | everything else that sends a body — including `capture_order`, `confirm_order`, `refund_captured_payment`, `create_subscription`, `create_billing_plan` |
  | no `body` parameter at all | 18 | the reads, the deletes, and `activate_billing_plan` / `deactivate_billing_plan` |

  **`body: … | None = None` does not mean the API accepts no body.** `create_subscription()` and
  `create_billing_plan()` type-check with no arguments at all and then fail at PayPal with a 400 —
  the SDK's optionality is the spec's, not a statement about what the endpoint needs. Where a create
  takes an optional-looking body, pass one.

A defaulted keyword is not always defaulted to `None`, **and a non-`None` default silently narrows
what comes back**. Read the sheet's default column before assuming a response is incomplete because
of the API. In this SDK:

| Default | Where | What it costs you |
|---|---|---|
| `prefer="return=minimal"` | 11 operations | The response is id, status and links — not the full resource. Pass `prefer="return=representation"` for the rest. |
| `fields="transaction_info"` | `search_transactions` | Payer, cart, shipping and store info are **absent** from every transaction. Widen it explicitly. |
| `balance_affecting_records_only="Y"` | `search_transactions` | Non-balance-affecting records are filtered out. |
| `page_size=100`, `page=1` | `search_transactions` | — |
| `page_size=10`, `page=1` | `list_billing_plans`, `list_subscriptions` | A "missing" record is usually page 2. |
| `page_size=5`, `page=1` | `list_customer_payment_tokens` | Five is a very small default; easy to mistake for "the customer has 5 tokens". |

Chasing one of these as an API bug is the single most common wasted afternoon on this SDK: the data
was never requested.

## Some operations return `None`

**11 of the 40 operations declare `-> None`** — `patch_order`, `update_order_tracking`,
`delete_payment_token`, and the eight mutating `subscriptions` operations (`activate_billing_plan`,
`deactivate_billing_plan`, `patch_billing_plan`, `update_billing_plan_pricing_schemes`,
`activate_subscription`, `suspend_subscription`, `cancel_subscription`, `patch_subscription`).

```python
client.subscriptions.cancel_subscription(sub_id, body={"reason": "..."})   # -> None
```

Three consequences:

- **Do not bind the result** and do not test it — `None` here means *the call succeeded*. Success is
  "no exception raised", exactly as for the others.
- **Re-read if you need the new state.** A `patch_subscription` tells you nothing about what the
  resource now looks like; follow it with `get_subscription` if that matters.
- **The raw peer is `ApiResult[None, …]`**, so `with_raw_response` is the only way to see the status
  code (`204` vs `200`) — `Success(payload=None, response=resp)`.

Take the return type from the sheet. Writing `order = client.orders.patch_order(...)` type-checks
under a loose annotation and then fails later on `order.status` with an `AttributeError` on `None`.

## Passing a body: model or dict

Body parameters are typed as a union of the model and its `TypedDict` companion, so both spellings
type-check:

```python
from {root_package}.models import OrderRequest

client.orders.create_order(OrderRequest(intent="CAPTURE", purchase_units=[...]))   # model
client.orders.create_order({"intent": "CAPTURE", "purchase_units": [...]})         # dict
```

The dict form nests too — a nested model's companion is accepted wherever the model is. Prefer models
in application code (better inference, better errors, and required members are enforced at
construction); the dict form suits payloads assembled from external data. See `python-models` for
required-vs-optional members, `UNSET`, and enums.

## Two response modes

Every operation has two forms, and choosing between them is a real design decision:

**Raising (the default).** Returns the decoded payload directly — no envelope, no unwrapping. On a
non-2xx it raises the SDK's `ApiError`.

```python
order = client.orders.create_order(body)      # -> Order
print(order.id, order.status)
```

**Non-raising — `with_raw_response`.** Returns a result object you branch on. Use it when you need the
**status code or response headers**, which the raising form does not expose on success, or when a
non-2xx is an expected outcome rather than an exception.

```python
from {root_package}.core import Success, Failure

match client.orders.with_raw_response.create_order(body):
    case Success(payload=order, response=resp):
        print(resp.status_code, order.id)
    case Failure(error=err, response=resp):
        print(resp.status_code, err)
```

`Success` and `Failure` are frozen dataclasses, so `match` works with no boilerplate; `isinstance`
narrowing is equivalent if you prefer it. Both carry `.response`. `.unwrap()` collapses either to the
payload — returning it for a `Success`, raising `ApiError` for a `Failure` — which is exactly how the
raising form is implemented.

**Two things `with_raw_response` does *not* protect you from**, and both are commonly assumed:

1. **Authentication failures still raise.** The token fetch unwraps internally, so bad credentials
   raise `ApiError` out of a `with_raw_response` call.
2. **Decode failures still raise.** A body that does not match the declared schema raises
   `ValidationError`/`ValueError` in *both* modes — a deserialization failure is not an API error.

So `with_raw_response` removes one exception path, not the need for a `try`. See
`python-error-handling`.

## Per-call overrides — `request_options`

Every operation accepts `request_options`, the single override channel, typed or dict-shaped:

```python
client.orders.get_order(order_id, request_options={"timeout": 5.0})
client.orders.get_order(order_id, request_options=RequestOptions(timeout=5.0))
```

Available keys are `timeout` (seconds, must be > 0) and `extra_headers`. It is validated with
`extra="forbid"`, so `{"timeuot": 5}` raises `ValidationError` rather than being ignored — and a type
checker catches it at the call site first, because the dict form is a closed `TypedDict`.

`extra_headers` wins over both the API's and the endpoint's own headers, which makes it the deliberate
way to override a header the SDK sets — including blanking an auth header for a call meant to go out
anonymous. That precedence is a footgun as much as a feature: setting `authorization` here overrides
the managed token.

Two mechanics to know:

- **Header names are lowercased on the way out**, at every layer including yours. So the merge is
  genuinely case-insensitive — `{"Authorization": …}`, `{"authorization": …}` and
  `{"AUTHORIZATION": …}` all override the managed token, and the request that reaches the transport
  is keyed `authorization`. Look up request headers in lowercase (`python-testing`).
- **`Cookie` is the one header that does not replace — it folds.** Every other field takes the later
  layer and discards the earlier; a cookie you add joins the jar alongside whatever the endpoint or
  a credential put there (RFC 6265 permits only one `Cookie` field). So you cannot blank a
  cookie-carried credential with `extra_headers` the way you can an `authorization` one. Not a
  concern for this SDK — its one scheme is a bearer header — but it is why "extra_headers overrides
  everything" is not quite the rule.

## A body that will not serialize fails *before* the request

Serialization happens when the body is built, not in the transport, so a model the SDK cannot dump
raises out of the call with **no request sent**. The one case you will actually hit is a field
annotated `Optional[Any]` left unset — `Patch(op="remove", path=…)` is the common one — which raises
`PydanticSerializationError`. See `python-models`; the fix is passing `value=None`.

## Async

The async client's operations are identical in name and parameters — you await them:

```python
order = await client.orders.create_order(body)

match await client.orders.with_raw_response.create_order(body):
    case Success(payload=order): ...
```

There is no `_async` suffix and no separate method list; the client class you hold decides the flavour.
Do not mix the two clients in one call path (`python-client-initialization`).

To run independent calls concurrently, gather them — this is the main reason to choose the async
client at all:

```python
orders = await asyncio.gather(*(client.orders.get_order(i) for i in ids))
```

Be deliberate about concurrency limits: `gather` over a large list opens as many concurrent requests
as the list is long, against a provider that rate-limits. Bound it with a semaphore or chunk the list.

## Cancellation and deadlines

There is no cancellation-token parameter. Python's mechanisms apply instead:

- **A per-call timeout** via `request_options={"timeout": ...}` — the direct equivalent for bounding
  one request.
- **`asyncio.timeout(...)`** (3.11+) or `asyncio.wait_for` around an async call, to bound a whole
  operation including your own surrounding work. Cancellation raises `CancelledError`/`TimeoutError`
  through the await, which will not be caught by an `except ApiError` clause.
- For sync code there is no external cancellation — the timeout *is* the mechanism, so set one.

## Paging

Whether an operation auto-pages is per-SDK and stated on the contract sheet. Where the generator emits
no pagination helper (the common case, and the case in this SDK), list operations expose their own
paging parameters — typically `page` and `page_size`, often already defaulted — and **you drive the
loop**:

```python
PAGE_SIZE = 20                                   # the default is 10 — set it deliberately

page = 1
while True:
    result = client.subscriptions.list_billing_plans(page=page, page_size=PAGE_SIZE)
    items = result.plans or []                   # UNSET is falsy, so `or []` is safe here
    if not items:
        break
    yield from items
    if len(items) < PAGE_SIZE:                   # short page = last page
        break
    page += 1
```

Do not assume a `for page in client...` iterator exists; check the return type. This SDK emits **no
pagination helper of any kind** — no iterator, no auto-paging, no cursor object — so the loop above
is the contract.

Whether you can know the total at all varies by operation, so do not build a `range(total_pages)`
loop without checking:

| Operation | `total_items` / `total_pages` |
|---|---|
| `list_billing_plans` | on `PlanCollection`, but `UNSET` unless you pass `total_required=True` (defaults to `False`) |
| `list_customer_payment_tokens` | on `CustomerVaultPaymentTokensResponse`, same opt-in |
| `search_transactions` | on `SearchResponse`, with **no** `total_required` parameter to pass |
| `list_subscriptions` | **absent** — `SubscriptionCollection` has no total fields at all |

The loop-until-short-page shape above is the one that works for all four.

## Verify the first call on the wire

On a successful response the SDK returns only the decoded body — never the URL or status — so a wrong
path parameter, a header you thought you set, or a query param that silently did not serialize
produces no in-band signal. The only symptom is a `404`/`422` that looks like an API problem.

The first time you run a new call, log it at the transport seam (`python-configuration-resilience`
shows the wrapper) and check: the method and path are what you expect, no `{placeholder}` survived
into the URL, path segments carry wire values rather than Python member names, and the query params
you set actually appear.

## Next

- Models, enums, `UNSET` → **python-models**
- Exceptions and error unions → **python-error-handling**
- Timeouts, retries, logging → **python-configuration-resilience**

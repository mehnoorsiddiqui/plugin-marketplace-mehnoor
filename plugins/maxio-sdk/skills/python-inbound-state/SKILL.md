---
name: python-inbound-state
description: State the provider originates rather than returns — callbacks and webhooks, replays, out-of-order delivery, and settling a write whose outcome is unknown. Load to FIND OUT whether this applies, not once you already know it does. The SDK surface is entirely outbound — there is no method that means "receive" — so nothing in it will raise the question, and it applies to every integration regardless, because any write can fail without answering and any status can settle after the call returns.
---

# Inbound state from a provider

An SDK call is a question you asked. This skill covers the facts that arrive **without** you asking: a
callback, a status that settles later, or the outcome of a write that never answered.

**Ask whether this applies before deciding it does not.** The row is easy to skip because the SDK surface
is entirely outbound. Three questions settle it:

1. Does any operation return a state that is **not final** (pending, processing, created, scheduled)?
2. Does the provider offer callbacks, webhooks, or subscriptions?
3. Can a write fail **without answering** — a reset connection, a timeout?

Any yes and this row is live. Question 3 is always yes.

> `{...}` is a placeholder for a name taken from the SDK — replace it with the concrete identifier.

## Receiving a callback in Django / DRF

Four things must be true of the endpoint, and the first is the one Python web frameworks make easy to get
wrong.

### 1 · Verify the signature against the *raw* body

Providers sign the exact bytes they sent. By the time a DRF serializer has parsed `request.data`, those
bytes are gone, and re-serializing the parsed dict does **not** reproduce them — key order, whitespace and
number formatting all differ. Read `request.body` first:

```python
@api_view(["POST"])
@authentication_classes([])          # the signature IS the authentication
@permission_classes([AllowAny])
def provider_webhook(request):
    raw = request.body                       # bytes, exactly as sent — before any parsing
    if not signature_is_valid(raw, request.headers):
        return Response(status=401)

    payload = json.loads(raw)
    ...
```

**Compare signatures in constant time** — `hmac.compare_digest(a, b)`, never `==`.

**Reject on failure; do not log the body and continue.** An unverified callback is an unauthenticated
request that happens to be well-formed.

**A webhook signing secret is a credential.** It belongs with the others — validated at startup, never
logged. See `python-authentication`.

**CSRF**: a provider cannot send your CSRF token. Exempt the view deliberately (`@csrf_exempt` or DRF's
`APIView`, which is exempt by default) and let the signature carry the authentication instead.

### 2 · Acknowledge fast, process after

Providers time out callbacks and retry on non-2xx. Work done inline — a database write, an outbound SDK
call, an email — happens inside the provider's timeout budget, and exceeding it turns a successful
delivery into a retry storm.

Persist the verified payload, return `200`, and process from there (a task queue, a management command, an
outbox row). The rule: **the response says "received", not "handled".**

### 3 · Treat every delivery as a possible replay

At-least-once is the norm. The same event will arrive twice, and a handler that is not idempotent will
double-apply it.

Key on the **provider's** event id, and make the database enforce it:

```python
class ProviderEvent(models.Model):
    provider = models.CharField(max_length=32)
    event_id = models.CharField(max_length=128)

    class Meta:
        constraints = [
            models.UniqueConstraint(fields=["provider", "event_id"], name="uniq_provider_event"),
        ]

# In the view:
try:
    ProviderEvent.objects.create(provider="x", event_id=payload["id"])
except IntegrityError:
    return Response(status=200)      # already seen — acknowledge and do nothing
```

An `if ProviderEvent.objects.filter(...).exists(): return` without the constraint behind it is a
check-then-act race, and two simultaneous deliveries pass it.

### 4 · Delivery order is not event order

Events are generated in order and delivered in whatever order the network allows. A `completed` event can
land before the `processing` event that preceded it, and applying them in arrival order leaves the record
in the earlier state permanently.

Carry the provider's own sequence — a version, a revision, or the event timestamp — and **refuse to move
backwards**:

```python
if payload_occurred_at <= record.last_event_at:
    return Response(status=200)      # stale or replayed — acknowledge, do not apply
```

Where the provider gives no ordering signal, treat the callback as a **hint to re-read** rather than as the
new state: fetch the current value with the SDK and store that. It costs a call and removes the class.

## When there is no callback: reconcile

A provider with no callbacks does not remove the row — it moves the work to your side.

**Poll on a bound, not on hope.** A status that settles asynchronously needs a re-read with an attempt cap,
a delay, and a terminal-status stop:

```python
for _ in range(MAX_ATTEMPTS):
    current = client.{group}.{fetch_operation}(id)
    if current.status in TERMINAL_STATUSES:
        return current
    time.sleep(POLL_DELAY)
# Fell through: report "not settled within budget" — NOT "failed".
```

The fall-through is the case that gets written wrong. "Still pending after N attempts" is not a failure;
reporting it as one tells the caller something untrue about provider state.

**A reconciliation report is this row, at rest.** Where the task asks you to line the provider's records up
against your own over a date range, that report *is* the reconciliation mechanism — and it must cover the
whole range, not the first page of it. A page loop that stops when the provider stops offering pages is
not a bound (`python-configuration-resilience`).

## Settling a write that never answered

**This applies to every integration, including ones with no callbacks and no asynchronous states.**

A transport failure on a write means the outcome is **unknown** (see `python-error-handling`). The request
may have reached the provider and been processed before the connection dropped; a failure thrown on the way
out is indistinguishable from one thrown on the way back.

Re-read provider state to establish what actually happened:

```python
except (httpx.TransportError, httpx.TimeoutException):
    # Do NOT report "failed" — that is a claim you cannot support.
    existing = find_by_client_reference(reference)
    return Outcome.APPLIED if existing else Outcome.NOT_APPLIED
```

The re-read needs something to look the record up **by**, and that has to be decided before the write, not
after: a client-supplied reference on the request, or a query narrow enough to identify the record from
what you already know. An integration that cannot find its own write has no way to settle this — which is
why row B and this row are decided together.

Where nothing can identify the record, say so and surface the outcome as **unknown**. An unknown outcome
reported honestly is usable; a guess is not.

## The three states, and why they must stay apart

| state | means | what the caller may do |
| --- | --- | --- |
| **applied** | established that it happened | proceed |
| **not applied** | established that it did not | retry safely |
| **unknown** | not established | do not retry blindly; settle first |

Most integrations have only the first two, so every unknown is forced into one of them — and both choices
are wrong in a way the code cannot see. Give the third state a name in your own result type.

## Next

- What "unanswered" means at the except → `python-error-handling`
- Preventing the duplicate rather than detecting it → `python-configuration-resilience`
- The row this skill closes → `python-integration-planning`

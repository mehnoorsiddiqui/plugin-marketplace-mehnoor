---
name: python-sdk-drift
description: Pinning a generated Python SDK and holding the runtime behaviour its types do not express — what retries, what a timeout bounds, whether an error union matches real traffic, what a model does with unknown fields. Load BEFORE finishing any integration, including a small one that has no "shipping" moment, and again before any upgrade or regeneration. A version left unpinned and a measured behaviour recorded only in prose are the two ways a working integration breaks with no code change.
---

# Change safety for a Python SDK integration

An integration is correct against **a version**. This skill is about making that version explicit and
making the assumptions underneath it fail loudly instead of silently.

**The failure mode is specific and it is not an import error.** A regenerated or upgraded SDK keeps the
same class names and method names while changing what happens at runtime: whether anything retries, whether
a timeout bounds a call or an attempt, whether an error union still matches the body the API sends, whether
a model keeps unknown fields. Python will not tell you at import time. The integration keeps running and
starts behaving differently.

> `{...}` is a placeholder for a name taken from the SDK — replace it with the concrete identifier.

## 1 · Pin the version

```
# requirements.txt — an exact pin, not a floor
{package}==1.4.2
```

`>=1.4.2` is a *floor*, and `pip install -U` will resolve past it — which is how an SDK changes underneath
an integration nobody touched. Where the project uses a lock file (`poetry.lock`, `uv.lock`,
`requirements.txt` compiled by `pip-compile`), commit it so an install is reproducible on CI.

Record two versions in the app's own configuration and startup log: the **SDK package version** and the
**API version** it targets. When behaviour changes, the first question is which of the two moved, and an
integration that logs neither cannot answer it.

## 2 · Name what you depend on that the types do not express

Write these down as part of the plan, and treat each as an assumption until measured:

| assumption | why it is invisible | how it bites |
| --- | --- | --- |
| **whether anything retries, and on what** | transport configuration, not a signature | a write is resent, or is not resent when you assumed it would be |
| **what a timeout bounds** | one value may bound a connect, a read, or a whole call | a bound you sized at 10s costs far more |
| **that a custom transport still honours `request.timeout`** | your code, not the SDK's | the client's `timeout=` silently stops reaching the wire |
| **that the operation's error union matches what the API sends** | generated from the spec, not from traffic | the typed `except` never fires and the status is lost |
| **that an enum covers the values the server sends** | generated from the spec's enumeration | an unknown value at runtime |
| **that unknown response fields are preserved** | depends on the model's config | a new field is silently dropped |
| **that the client is safe to share across workers/threads** | process model, not API shape | a forking server hands children a connection pool that was opened before the fork |

**A comment does not hold an assumption. A test does.** The line "verified 2026-08-25 against 1.4.2" ages
into a claim nobody rechecks; the same fact as an assertion fails the build the day it stops being true.

## 3 · Characterize the behaviour, do not describe it

A characterization test asserts what the SDK *currently* does, so a change to it is a failing test rather
than a production surprise. These are cheap — the transport seam is already there (`python-testing`) — and
they are the only durable form of the table above.

**The retry question, the highest-value one**, because a wrong belief here changes the architecture:

```python
def test_write_is_sent_once_under_a_transport_failure(monkeypatch):
    calls = []

    def handler(request):
        calls.append(request)
        raise httpx.ConnectError("reset", request=request)

    client = {Api}Client(base_url="http://test", custom_http_client=httpx.Client(
        transport=httpx.MockTransport(handler)))

    with pytest.raises(Exception):
        client.{group}.{write_operation}(body)

    assert len(calls) == 1          # pins that nothing resent the write
```

Assert **both directions** where the SDK does retry: a test that only pins "the write is not resent" passes
just as happily if retries stopped working entirely.

**Also worth pinning where you rely on it:**

- **The error union** — a mock returning the body the API *really* sends on a mapped error status,
  asserting the typed `except` fires and the expected member is present. This is the test that catches a
  spec/traffic disagreement before production does.
- **Unknown-field preservation** — a mock response with an extra field, asserting whether it survives on
  the model. Whichever way it goes, pin it: the guidance in `python-models` describes the general case,
  and your model is a specific one.
- **Timeout semantics** — a mock that delays past the configured timeout, asserting how long the call
  actually took and how many requests were made.

Keep these in a module named for what they are — `test_sdk_characterization.py` — so a failure reads as
"the SDK changed", not "our code broke".

## 4 · The upgrade checklist

Run this on any SDK bump or regeneration, **before** merging:

1. **Diff the generated surface.** New, removed and renamed operations; changed parameter names; changed
   enum members; changed return shapes. In Python a renamed keyword argument fails at **runtime**, not at
   import — so a call site not covered by a test will not tell you.
2. **Run the characterization tests.** A failure here is the point of the exercise: read it as new
   information about the SDK, not as a test to update reflexively.
3. **Re-run the wire verification** on at least one call per group: method, path, no unsubstituted
   placeholder, parameters in the part of the request the API expects.
4. **Re-check the rows that depend on runtime behaviour** — B (exactly-once) and E (bounds). Both are
   conclusions drawn from transport behaviour, and both are invalidated by a transport change.
5. **Update the recorded versions** in config and in the plan.

## 5 · Drift on the provider's side

The SDK is one of two things that move. The API behind it moves too, and regeneration carries the change
across.

- **A new required field** on a request surfaces as a validation error after regeneration. Fine.
- **A new value in an existing enum** does not, if the enum is open — the value arrives as a plain string
  and is not a known member. Guard anything you branch on. → `python-models`
- **A new response field** is dropped unless the model preserves unknowns.
- **A changed error body** on an existing status silently converts a typed `except` into a decode failure.
  → `python-error-handling`

Where the provider publishes API versions, pin the one you target explicitly rather than defaulting to
"current", and treat a version bump as a regeneration with this whole checklist attached.

## Next

- The seam these tests use → `python-testing`
- The behaviours being pinned → `python-configuration-resilience`
- The row this skill closes → `python-integration-planning`

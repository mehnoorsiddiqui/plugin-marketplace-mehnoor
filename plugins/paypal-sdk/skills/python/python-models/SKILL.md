---
name: python-models
description: Working with models in an APIMatic-generated Python SDK — pydantic models and their TypedDict companions, required members vs the UNSET sentinel, why Optional here is not typing.Optional, open enums, wire aliases, frozen instances, unknown-field preservation, and serializing with to_dict/to_json. Load before constructing request payloads or mapping SDK models onto your own types.
---

# Working with models in an APIMatic Python SDK

Models are **pydantic v2 models** built with keyword arguments. This skill covers the shapes that trip
integrations up; take the real type and member names from the contract sheet, never from a REPL poke
at the installed package.

> `{...}` is a placeholder for a name from your SDK.

## The base every model shares

Domain models subclass a generated base with a deliberate configuration. Four of its choices change
how you write code:

| Config | What it means for you |
|---|---|
| `frozen=True` | Instances are immutable — assigning to a field raises. Use `model_copy(update={...})` to derive a changed copy. |
| `extra="allow"` | **Unknown fields are preserved, not dropped** — see below. |
| `validate_by_name` + `validate_by_alias` | Input is accepted under **either** the Python name or the wire alias. |
| `serialize_by_alias` | Output uses the **wire alias** by default. |

`frozen` blocks rebinding an attribute; it does **not** deep-freeze. A `list` or `dict` field's
contents remain mutable in place, so `order.purchase_units.append(...)` may well succeed and is still
a bug — treat the whole graph as read-only.

## Required vs optional: `UNSET`, and the `Optional` that is not `typing.Optional`

```python
class OrderRequest(SdkBaseModel):
    intent: CheckoutPaymentIntentOrStr                       # required
    purchase_units: list[PurchaseUnitRequest]                # required
    payer: Optional[Payer] = UNSET                           # optional
    payment_source: Optional[PaymentSource] = UNSET          # optional
```

- A **bare annotation** is required. Omit it and pydantic raises `ValidationError` at construction —
  which is the good case: the failure is at the point of the mistake, not at the API.
- **`Optional[T] = UNSET` is optional.** Leave it out entirely and the key is omitted from the JSON.

**`Optional` here means `T | UnsetType` — present or absent, with no `None` arm.** It is *not*
`typing.Optional[T]` (`T | None`), and the difference is the arm that matters: a field the spec does
not declare nullable must not be able to reach the wire as `null`. So:

```python
OrderRequest(intent="CAPTURE", purchase_units=[...], payer=None)   # ✗ type error, and rejected
OrderRequest(intent="CAPTURE", purchase_units=[...])               # ✓ omitted
```

Never "clear" an optional field by passing `None`. Omit it, or pass `UNSET` explicitly if you are
building the kwargs dynamically.

A field annotated **`OptionalNullable[T]`** (`T | None | UnsetType`) is the three-state case: omitted,
explicitly `null`, or a value. There, `None` *is* meaningful — it sends `null`, which for a PATCH-style
API is how you erase a value rather than leave it unchanged. Read the annotation before deciding what
`None` means.

`UNSET` is falsy and identity-stable, so both of these work on a response:

```python
if order.payer:                      # falsy when UNSET (and when empty!)
if order.payer is not UNSET:         # precise: was it set at all?
```

Prefer the `is not UNSET` form when "set but empty" and "not set" are different facts.

## Dict companions

Every model has a `{Model}Dict` `TypedDict` companion mirroring it field for field, with optional
members marked `NotRequired`. Anywhere a model is accepted, the companion is too — including nested:

```python
OrderRequest(intent="CAPTURE", purchase_units=[{"amount": {"currency_code": "USD", "value": "10.00"}}])
```

The companion is a **closed** TypedDict, so a misspelled key is a type-checker error at the call site
rather than a runtime surprise. Three caveats:

- the companion is a typing construct only — it performs no runtime validation until the SDK validates
  the whole body;
- it cannot express `UNSET`, so a dict simply omits what it does not set;
- **the closed check only helps if a type checker actually runs.** At runtime the model base is
  `extra="allow"`, so a misspelled key that no checker saw is *accepted* and sent as an extra field
  rather than rejected (see *Unknown fields are preserved* below). The dict spelling is only as safe
  as your `mypy`/`pyright` gate.

## Enums are open

Enums are real `Enum` subclasses (`(str, Enum)` or `(int, Enum)`), each paired with an **open alias**
`{Enum}OrStr` = `Annotated[{Enum} | str, ...]`. A field typed with the open alias accepts either:

```python
from {root_package}.models.enums import CheckoutPaymentIntent

intent = CheckoutPaymentIntent.CAPTURE     # preferred: checked, discoverable
intent = "CAPTURE"                         # also valid
```

The point of the open alias is forward compatibility: **a value the server adds after this SDK was
generated survives as a plain string** instead of raising. That cuts both ways, and it is the thing to
get right when *reading* a response:

```python
status = order.status                      # may be OrderStatus OR str

if status == OrderStatus.COMPLETED:        # ✓ works for the known member
    ...

match order.status:                        # handle the open arm explicitly
    case OrderStatus.COMPLETED: ...
    case OrderStatus.CREATED: ...
    case str() as unknown:                 # a value newer than this SDK
        log.warning("unknown status %s", unknown)
```

An `if/elif` chain over members with no final `else` silently does nothing for an unknown value.
Because these are `(str, Enum)` with `__str__ = str.__str__`, `str(member)` and f-string interpolation
give the **wire value** (`"CAPTURE"`), not `"CheckoutPaymentIntent.CAPTURE"` — so logging and
comparison against raw strings both behave.

## Wire aliases

Where a JSON field name is not a valid or idiomatic Python identifier, the model declares an alias.
The Python name is what you write in code; the alias is what crosses the wire. Both are accepted on
input, and output uses the alias. When a sheet lists a member as `some_field (wire someField)`, use
`some_field` in code and expect `someField` in a captured request body — do not "fix" a test that
asserts the alias.

**In this SDK the aliases are exactly the Python-keyword collisions**, and the rule is a trailing
underscore — 19 models are affected and there is no other alias in the package:

| Python name | Wire name | Where |
|---|---|---|
| `type_` | `type` | every card / shipping / token model that carries a `type` (`CardResponse`, `ShippingDetails`, `Token`, `VaultTokenRequest`, …) |
| `from_` | `from` | `Patch` (the JSON-Patch `move`/`copy` source pointer) |

So `card.type_` in code, `"type"` in the JSON. Everything else in this SDK is `snake_case` on both
sides — PayPal's own wire format is snake_case, so there is no `camelCase` translation happening at
all, and a `someField` appearing in a body you built is a bug in your code, not an alias.

**The `…Dict` companion is keyed by the PYTHON name, not the alias.** `PatchDict` declares `from_`,
not `from` — a `TypedDict` cannot declare a keyword as a key through class syntax, and the base
config's `validate_by_name` is what makes it work:

```python
client.orders.patch_order(order_id, body=[{"op": "replace", "path": "/intent", "value": "CAPTURE"}])
client.orders.patch_order(order_id, body=[{"op": "move", "from_": "/a", "path": "/b"}])   # from_, not from
```

The serialized body still carries `"from"`. Passing `{"from": ...}` type-checks as nothing (it is an
unknown key to the `TypedDict`) but *does* validate at runtime via `validate_by_alias` — so both
happen to work, and only one is checked. Write `from_`.

## Serializing

```python
order.to_dict()                     # JSON-safe values, wire aliases, json.dumps-able
order.to_json()                     # the same content as text, indented 2 by default
order.to_dict(mode="python")        # keep Python objects (datetime stays datetime)
order.to_dict(by_alias=False)       # key by Python name instead
order.to_dict(exclude_none=True)    # drop nulls, round-trip safe
```

`to_dict`/`to_json` are the front door; `model_dump`/`model_dump_json` remain available for options
the wrappers do not surface. Two behaviours worth knowing:

- A never-touched `OptionalNullable` field is **omitted** by `to_dict`, even though a plain
  `model_dump` renders it as `null`. That is the tri-state being honoured; it is the one documented
  place the wrapper differs from the underlying dump.
- **`exclude_unset=True` is a trap on a locally built model.** It drops defaulted discriminator
  fields, after which the result no longer validates back against a discriminated union. Use
  `exclude_none=True` if your goal is just to suppress nulls.

## The `Optional[Any]` trap — a real serialization failure

**A field annotated `Optional[Any]` cannot be left unset.** Leaving it at `UNSET` makes the whole
model unserializable — `to_dict()`, `to_json()` *and the request path* all raise:

```
pydantic_core._pydantic_core.PydanticSerializationError:
    Unable to serialize unknown type: <class '…core.optionality.UnsetType'>
```

`UnsetType` carries its own serializer, but `Any` absorbs the union arm before that serializer is
reached, so the sentinel arrives at pydantic's generic any-serializer, which has never heard of it.
Every other `Optional[T]` is fine; this is specific to `Any`.

In this SDK three fields are affected — `Patch.value`, `BankRequest.ach_debit` and
`CardVerificationDetails.three_d_secure`:

```python
Patch(op="remove", path="/x").to_dict()                    # ✗ PydanticSerializationError
client.orders.patch_order(order_id, body=[Patch(op="remove", path="/x")])   # ✗ same, before sending
client.orders.patch_order(order_id, body=[{"op": "remove", "path": "/x"}])  # ✗ the dict form too
```

`Patch.value` is the one that bites, because **`remove`, `move` and `copy` are exactly the ops that
take no value** — the documented normal path for three of the five JSON-Patch operations. And the
dict spelling is no escape: it validates into the same model, sentinel included.

**Workaround: pass `None` explicitly.**

```python
client.orders.patch_order(order_id, body=[Patch(op="remove", path="/x", value=None)])
# request body -> [{"op": "remove", "path": "/x", "value": null}]
```

It type-checks (`Any` admits `None`) and it serializes. Note the cost: the request now carries
`"value": null` where it should have carried nothing. Two consequences worth stating on any sheet
that touches `patch_order`:

- an explicit `null` is not the same wire message as an omitted key, so a strict JSON-Patch validator
  could reject it — confirm against sandbox before shipping a `remove`;
- this is the one place in this SDK where `None` on an `Optional[...]` field is correct. Everywhere
  else it is a type error (see above). Do not generalise it.

If a future SDK version widens these annotations, drop the `value=None`.

## Unknown fields are preserved

Unlike SDKs that drop unmodelled JSON, `extra="allow"` keeps them — at every nesting level, readable
via `model_extra`, and **re-emitted under the key they arrived with**:

```python
extra = order.model_extra or {}
new_field = extra.get("some_new_paypal_field")
```

This makes it safe to read an object, change one member and send it back without silently discarding
server-side state you never modelled. The symmetry also means an unknown key *you* supply is sent
rather than rejected — convenient for a field newer than the SDK, and a silent no-op when you simply
misspell a real one. A misspelled optional member does not error; it becomes an extra. If a value you
set is not taking effect, check it against the sheet's member list before suspecting the API.

## Dates and numbers

- Date/time fields use the SDK's converter types (`Date`, `RFC3339DateTime`, `RFC1123DateTime`,
  `UnixSecondsDateTime`) so the wire format is handled for you. Assign a `datetime`/`date` and read one
  back; do not format strings by hand.
- **Money is usually a `str`, not a `Decimal` or `float`.** PayPal amounts are strings scaled to the
  currency (`"10.00"`). Build them with `Decimal` and format explicitly — never with `%f`/`round()`,
  and never from a locale-dependent conversion that can produce `"10,00"`:

  ```python
  from decimal import Decimal
  value = f"{Decimal('10.00'):.2f}"
  ```

- Currency codes are typically plain `str` with no enum — pass `"USD"`.

## ValidationError is your friend, not an error to suppress

Constructing a model wrong raises `pydantic.ValidationError` (a `ValueError`) with the exact field
path. That is the SDK catching your mistake at the point you made it. Let it propagate in development;
in production, catch it at the boundary where *you* assemble a payload from external input, and treat
it as a 4xx on your own API rather than an upstream failure.

See [reference.md](reference.md) for the mechanics — `UNSET` semantics, model_copy, and enum helpers.

## Next

- Calls and bodies → **python-calling-endpoints**
- Error payload models → **python-error-handling**

# Model mechanics — reference

Supporting detail for `python-models`. Take type and member names from the contract sheet.

## `UNSET` semantics

```python
from {root_package}.core import UNSET, UnsetType
```

- A singleton: `UnsetType()` returns the existing instance, and `copy`/`deepcopy`/unpickle all
  preserve identity. So `field is UNSET` is reliable, including on a deep copy of a model.
- Falsy: `bool(UNSET) is False`. Convenient, but it collapses "unset" with "empty list" and
  "empty string" — use `is UNSET` when you need to tell them apart.
- `repr(UNSET)` is `"UNSET"`.
- It is only ever a **default**. Never construct `UnsetType()` yourself; pass `UNSET` if you need to
  spell "omitted" explicitly while building kwargs.

Building a payload conditionally, without a `None` ever reaching a non-nullable field:

```python
body = OrderRequest(
    intent="CAPTURE",
    purchase_units=units,
    payment_source=source if source is not None else UNSET,
)
```

## Deriving a changed copy

Models are frozen, so mutate by copying:

```python
updated = body.model_copy(update={"intent": "AUTHORIZE"})
```

`update=` bypasses validation for the updated keys — pydantic does not re-validate a `model_copy`.
For values from an untrusted source, rebuild the model instead so validation actually runs.

## Enum helpers

```python
from {root_package}.models.enums import OrderStatus

OrderStatus("COMPLETED")            # wire value -> member; ValueError if unknown
OrderStatus.COMPLETED.value         # -> "COMPLETED"
str(OrderStatus.COMPLETED)          # -> "COMPLETED"  (not "OrderStatus.COMPLETED")
list(OrderStatus)                   # every member
```

Coercing an unknown value with `OrderStatus(...)` raises `ValueError` — that is the *closed* lookup.
The **open** alias used on model fields does not raise; it passes the unknown value through as a
string. Do not reimplement that coercion at your boundary; read the field and handle the `str` arm.

Tolerating an unknown value when you must map to your own enum:

```python
def to_domain(status: OrderStatus | str) -> MyStatus:
    match status:
        case OrderStatus.COMPLETED: return MyStatus.DONE
        case OrderStatus.CREATED:   return MyStatus.PENDING
        case _:                     return MyStatus.UNKNOWN     # covers new wire values
```

## Reading nested optional structures

**Response models declare almost *everything* optional — and in this SDK, literally everything.**
16 of the 17 return types have **no required member at all**, so `Order.model_validate({})`
succeeds and every field reads back `UNSET`. (The exception is `SubscriptionTransactionDetails` from
`capture_subscription`, which requires `id`, `amount_with_breakdown` and `time`.)

Two consequences for reading a response:

- **A truncated body is not an error**, it is a model full of `UNSET`. Nothing raises. Check the
  members you actually depend on — see `python-error-handling`.
- A chain of `?`-style access is the normal shape. Python has no `?.`, so guard or use a walrus:

```python
amount = None
if (units := order.purchase_units) and units[0].amount:
    amount = f"{units[0].amount.currency_code} {units[0].amount.value}"
```

A member that is **required on the request model and optional on the response model** is common and
intentional — the same concept, two schemas. Never assume the response shape from the request shape.

## Unknown fields

```python
order.model_extra            # dict of preserved unknown keys, or None
order.model_fields_set       # which declared fields were explicitly set
```

Unknown keys are also reachable by attribute access **unless** the name collides with the model API
(`json`, `copy`, `dict`, `schema`, …). Prefer `model_extra["key"]` — it never collides.

## Type-checking notes

The SDK ships `py.typed`, so `mypy`/`pyright` check your calls against it fully.

- `Optional[T]` from the SDK and `typing.Optional[T]` are **different types**. If you import both into
  one module you will confuse yourself and your reader; the SDK's generated modules never import
  `typing.Optional`, and yours should not shadow the name either.
- Passing `None` to an `Optional[T]` field is a type error the checker reports. Believe it — at
  runtime it is also a `ValidationError`.
- A `{Model}Dict` literal is checked structurally, so an unknown key is an error there too. That check
  is what makes the dict spelling safe to use at all.

---
name: python-integration-planning
description: FIRST STEP for any Python work that talks to an external API through a generated SDK — load before planning, before choosing an architecture, before the first line of code. Applies whenever a task involves calling, integrating, wrapping, or extending anything that reaches a provider over HTTP: taking a payment, saving a card, syncing records, a webhook, a reconciliation report, a background job. Load it EVEN IF you have not chosen an approach yet, EVEN IF this looks like a small addition to an existing app, and EVEN IF you already know how to call a REST API from Python — the SDK's actual shape (what retries, what a timeout bounds, which errors are typed, what the provider does after the call returns) changes which plan is correct, and a plan built on a guessed shape produces code that passes a happy-path test and then double-charges someone. Routes to every other python-* skill in the order they are needed.
---

# Planning a Python SDK integration

**Load this first, while planning — not while reviewing.** Every question below changes the shape of the
code. Answering them afterwards means rewriting.

## Read order

This one decides *what must be true*; the others say *how*. Open them as the work reaches them:

| # | skill | open it when |
|---|---|---|
| 1 | **this skill** | before any plan or architecture decision |
| 2 | `python-getting-started` | before any contract lookup — confirms the package and names the module owning each fact |
| 3 | `python-client-initialization` | constructing the client, choosing sync vs async, deciding where it lives |
| 4 | `python-authentication` | supplying credentials — and deciding what happens when they are absent |
| 5 | `python-calling-endpoints` | writing the first call |
| 6 | `python-models` | building a request payload or mapping a response onto your own types |
| 7 | `python-error-handling` | any try/except, translation layer, or error middleware |
| 8 | `python-configuration-resilience` | timeouts, retries you build yourself, paging |
| 9 | `python-inbound-state` | the provider calls back, settles later, or a write goes unanswered |
| 10 | `python-sdk-drift` | before shipping, and again after any upgrade or regeneration |
| 11 | `python-testing` | before claiming it works |

**9 and 10 are the two that get skipped**, because nothing in the code looks wrong without them.

## How to use this

Write the answers down **before** implementing — a section per row in a design note, a YAML file, or the
plan you present. An answer is one of three things:

- **Answered** — a concrete decision, naming the mechanism and where it is enforced in the code.
- **Waived** — `not_applicable`, *with the reason*.
- **Open** — you do not know. Say which; do not let a placeholder read as an answer.

Never leave a row unmentioned. An unmentioned row cannot be told apart from a forgotten one, and rows D and
F are the ones that get forgotten. Cite where each answer came from: the **spec**, the **SDK source**, the
**provider's docs**, or an **assumption**. An assumption is legitimate; an unlabelled one is not.

## The seven rows

### A · Correctness — does it call the right things, the right way?

Every operation used, whether each one **mutates**, the ordering constraints between them, and the value
constraints on the fields you populate — units, scale, allowed ranges, opaque id types that must not be
interchanged. Take signatures, wire aliases, enum members and return shapes from a lookup, never from
recall. → `python-calling-endpoints`, `python-models`

### B · Exactly-once — can a call happen twice, and does that matter?

For **each mutating operation**: what stops it, or makes it safe, when the same logical action is attempted
twice. Two independent sources:

- **Anything that retries** — the SDK's own behaviour, plus any retry you add yourself, plus the web
  framework or job runner above you.
- **Your own callers** — a double-clicked button, a retried task, a redelivered queue message.

Then name the mechanism per operation: a provider idempotency key, natural repeatability, a uniqueness
constraint, or a deliberate acceptance that a duplicate is harmless.

**Where the provider offers an idempotency key, derive it from the logical action, never per attempt.** A
fresh `uuid4()` on every call is syntactically present and semantically useless. And note that the
parameter existing is not proof the provider enforces it — verify, or say you did not.
→ `python-configuration-resilience`

### C · Failure — what can fail, and does the caller learn the right thing?

The failure families that reach your code, and what each **means**:

| family | means | consequence |
| --- | --- | --- |
| API error (non-2xx) | **rejected** | it did not happen; the same input will not succeed |
| auth failure | **rejected**, before dispatch | fix configuration, not the request |
| transport failure / timeout | **unanswered** | it *may* have happened — settle it, do not assume |
| decode failure | **local** | depends on whether the status was 2xx |

Then: **caller translation.** How each family becomes something the caller can act on. Your credentials and
your quota are not the caller's fault and must not surface as their 401 or 429. → `python-error-handling`

### D · Inbound state — what does the provider tell you, and when?

**The most-missed row.** Ask it even when the integration looks purely outbound. Does the provider change
state after your call returns? Does it call you back? If so: how the callback is authenticated, what
happens on a replay or an out-of-order delivery, and how the two views are reconciled.

If the provider offers no callback, the row is still live: **an unanswered write is inbound state you have
to go and fetch.** → `python-inbound-state`

### E · Operability — can it be run, and diagnosed, by someone who did not write it?

- **Secrets**: where they come from, never on argv, never logged in any form — not masked, not by length.
- **Startup validation**: a missing credential refuses to boot, naming the key. It is a deployment fault,
  not a request fault. → `python-authentication`
- **Logging**: what is recorded, and what correlates a log line to a provider-side record.
- **Bounds**: every call has a total budget, and every loop over pages has a cap that does not depend on
  the provider's cooperation. → `python-configuration-resilience`
- **Limits**: what happens at the provider's rate limit.

### F · Change safety — what breaks silently when something upstream moves?

**The second most-missed row.** Pin the SDK version. Then name the behaviours you depend on that are *not*
in the type system — what retries, what a timeout bounds, whether an error union matches what the API
really sends — and hold each with a test that fails on change rather than a comment that does not.
→ `python-sdk-drift`

### G · Proof — what was actually executed, and how would anyone know?

**A clean import and a passing unit test that never crossed the transport are not verification.**

Name the operations actually executed, the failure paths actually exercised, and the method — a fake
transport, a local server the SDK reaches over a real socket, a sandbox account. Say what was *not*
covered. → `python-testing`

**G cannot be waived.** Every operation named in row A appears here, or is listed as unexecuted.

## The check before you start writing

Rows **A, C, E, G** are the ones a careful implementation tends to reach on its own. Rows **B, D, F**
require a deliberate decision at design time and cannot be retrofitted cheaply:

- **B** decides whether you need a key, a guard, or nothing — and that changes the call site.
- **D** decides whether there is an inbound endpoint at all — and that changes the architecture.
- **F** decides what your tests assert — and that changes the test suite.

If the plan does not mention B, D and F by name, it is not finished.

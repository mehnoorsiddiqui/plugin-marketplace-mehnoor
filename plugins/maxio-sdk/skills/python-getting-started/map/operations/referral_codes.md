<!-- Generated file — do not edit; regenerated with the SDK. -->

# ReferralCodes — operations

Accessor: `client.referral_codes` · Source: `maxio_advanced_billing/apis/referral_codes.py` · 1 operation

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.referral_codes.validate_referral_code

- **Route**: `GET /referral_codes/validate.json`
- **Server**: `production`
- **Signature**: `def validate_referral_code(code: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `code`
- **Params**: `code` — query
- **Returns (parsed)**: `ReferralValidationResponse`
- **Returns (raw)**: `ApiResult[ReferralValidationResponse, ValidateReferralCodeErrorBody]`
- **Error**: `ValidateReferralCodeErrorBody` — **Case A (typed)**
- **Error arms**: `SingleStringErrorResponse1` [404] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `ReferralValidationResponse` | `maxio_advanced_billing/models/referral_validation_response.py` |
| `ValidateReferralCodeErrorBody` | `maxio_advanced_billing/errors/validate_referral_code_error.py` |
| `SingleStringErrorResponse1` | `maxio_advanced_billing/models/single_string_error_response1.py` |


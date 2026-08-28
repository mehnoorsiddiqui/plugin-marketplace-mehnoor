<!-- Generated file — do not edit; regenerated with the SDK. -->

# BillingPortal — operations

Accessor: `client.billing_portal` · Source: `maxio_advanced_billing/apis/billing_portal.py` · 4 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.billing_portal.enable_billing_portal_for_customer

- **Route**: `POST /portal/customers/{customer_id}/enable.json`
- **Server**: `production`
- **Signature**: `def enable_billing_portal_for_customer(customer_id: int, *, auto_invite: AutoInviteOrInt | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `customer_id`
- **Params**: `customer_id` — path · `auto_invite` — query
- **Returns (parsed)**: `CustomerResponse`
- **Returns (raw)**: `ApiResult[CustomerResponse, EnableBillingPortalForCustomerErrorBody]`
- **Error**: `EnableBillingPortalForCustomerErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `AutoInviteOrInt` | `maxio_advanced_billing/models/enums/auto_invite.py` |
| `CustomerResponse` | `maxio_advanced_billing/models/customer_response.py` |
| `EnableBillingPortalForCustomerErrorBody` | `maxio_advanced_billing/errors/enable_billing_portal_for_customer_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.billing_portal.read_billing_portal_link

- **Route**: `GET /portal/customers/{customer_id}/management_link.json`
- **Server**: `production`
- **Signature**: `def read_billing_portal_link(customer_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `customer_id`
- **Params**: `customer_id` — path
- **Returns (parsed)**: `PortalManagementLink`
- **Returns (raw)**: `ApiResult[PortalManagementLink, ReadBillingPortalLinkErrorBody]`
- **Error**: `ReadBillingPortalLinkErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `TooManyManagementLinkRequestsError1` [429] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `PortalManagementLink` | `maxio_advanced_billing/models/portal_management_link.py` |
| `ReadBillingPortalLinkErrorBody` | `maxio_advanced_billing/errors/read_billing_portal_link_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |
| `TooManyManagementLinkRequestsError1` | `maxio_advanced_billing/models/too_many_management_link_requests_error1.py` |

### client.billing_portal.resend_billing_portal_invitation

- **Route**: `POST /portal/customers/{customer_id}/invitations/invite.json`
- **Server**: `production`
- **Signature**: `def resend_billing_portal_invitation(customer_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `customer_id`
- **Params**: `customer_id` — path
- **Returns (parsed)**: `ResentInvitation`
- **Returns (raw)**: `ApiResult[ResentInvitation, ResendBillingPortalInvitationErrorBody]`
- **Error**: `ResendBillingPortalInvitationErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `ResentInvitation` | `maxio_advanced_billing/models/resent_invitation.py` |
| `ResendBillingPortalInvitationErrorBody` | `maxio_advanced_billing/errors/resend_billing_portal_invitation_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.billing_portal.revoke_billing_portal_access

- **Route**: `DELETE /portal/customers/{customer_id}/invitations/revoke.json`
- **Server**: `production`
- **Signature**: `def revoke_billing_portal_access(customer_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `customer_id`
- **Params**: `customer_id` — path
- **Returns (parsed)**: `RevokedInvitation`
- **Returns (raw)**: `ApiResult[RevokedInvitation, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `RevokedInvitation` | `maxio_advanced_billing/models/revoked_invitation.py` |


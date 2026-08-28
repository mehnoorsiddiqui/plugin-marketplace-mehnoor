<!-- Generated file — do not edit; regenerated with the SDK. -->

# SubscriptionGroups — operations

Accessor: `client.subscription_groups` · Source: `maxio_advanced_billing/apis/subscription_groups.py` · 9 operations

Each `###` block is one operation and assumes `sdk-map.md` is loaded: blocks omit what its invariants table covers and are otherwise self-contained, so chunk at block level. Signatures are the sync parsed spelling; the async and raw spellings take the same parameters (see sdk-map.md). **Type sources** names the module declaring each type an operation mentions, so resolving a body, return or error payload is a lookup rather than a search; the runtime types `RawError` and `ApiResult` are excluded.

### client.subscription_groups.add_subscription_to_group

- **Route**: `POST /subscriptions/{subscription_id}/group.json`
- **Server**: `production`
- **Signature**: `def add_subscription_to_group(subscription_id: int, *, body: AddSubscriptionToAGroup | AddSubscriptionToAGroupDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionGroupResponse`
- **Returns (raw)**: `ApiResult[SubscriptionGroupResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `AddSubscriptionToAGroup` | `maxio_advanced_billing/models/add_subscription_to_a_group.py` |
| `AddSubscriptionToAGroupDict` | `maxio_advanced_billing/models/add_subscription_to_a_group.py` |
| `SubscriptionGroupResponse` | `maxio_advanced_billing/models/subscription_group_response.py` |

### client.subscription_groups.create_subscription_group

- **Route**: `POST /subscription_groups.json`
- **Server**: `production`
- **Signature**: `def create_subscription_group(*, body: CreateSubscriptionGroupRequest | CreateSubscriptionGroupRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `SubscriptionGroupResponse`
- **Returns (raw)**: `ApiResult[SubscriptionGroupResponse, CreateSubscriptionGroupErrorBody]`
- **Error**: `CreateSubscriptionGroupErrorBody` — **Case A (typed)**
- **Error arms**: `SubscriptionGroupCreateErrorResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `CreateSubscriptionGroupRequest` | `maxio_advanced_billing/models/create_subscription_group_request.py` |
| `CreateSubscriptionGroupRequestDict` | `maxio_advanced_billing/models/create_subscription_group_request.py` |
| `SubscriptionGroupResponse` | `maxio_advanced_billing/models/subscription_group_response.py` |
| `CreateSubscriptionGroupErrorBody` | `maxio_advanced_billing/errors/create_subscription_group_error.py` |
| `SubscriptionGroupCreateErrorResponse1` | `maxio_advanced_billing/models/subscription_group_create_error_response1.py` |

### client.subscription_groups.delete_subscription_group

- **Route**: `DELETE /subscription_groups/{uid}.json`
- **Server**: `production`
- **Signature**: `def delete_subscription_group(uid: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path
- **Returns (parsed)**: `DeleteSubscriptionGroupResponse`
- **Returns (raw)**: `ApiResult[DeleteSubscriptionGroupResponse, DeleteSubscriptionGroupErrorBody]`
- **Error**: `DeleteSubscriptionGroupErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `DeleteSubscriptionGroupResponse` | `maxio_advanced_billing/models/delete_subscription_group_response.py` |
| `DeleteSubscriptionGroupErrorBody` | `maxio_advanced_billing/errors/delete_subscription_group_error.py` |

### client.subscription_groups.find_subscription_group

- **Route**: `GET /subscription_groups/lookup.json`
- **Server**: `production`
- **Signature**: `def find_subscription_group(subscription_id: str, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — query
- **Returns (parsed)**: `FullSubscriptionGroupResponse`
- **Returns (raw)**: `ApiResult[FullSubscriptionGroupResponse, FindSubscriptionGroupErrorBody]`
- **Error**: `FindSubscriptionGroupErrorBody` — **Case A (typed)**
- **Error arms**: `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `FullSubscriptionGroupResponse` | `maxio_advanced_billing/models/full_subscription_group_response.py` |
| `FindSubscriptionGroupErrorBody` | `maxio_advanced_billing/errors/find_subscription_group_error.py` |

### client.subscription_groups.list_subscription_groups

- **Route**: `GET /subscription_groups.json`
- **Server**: `production`
- **Signature**: `def list_subscription_groups(*, page: int | None = 1, per_page: int | None = 20, include: list[SubscriptionGroupsListIncludeOrStr] | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `page` — query · `per_page` — query · `include` — query
- **Returns (parsed)**: `ListSubscriptionGroupsResponse`
- **Returns (raw)**: `ApiResult[ListSubscriptionGroupsResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SubscriptionGroupsListIncludeOrStr` | `maxio_advanced_billing/models/enums/subscription_groups_list_include.py` |
| `ListSubscriptionGroupsResponse` | `maxio_advanced_billing/models/list_subscription_groups_response.py` |

### client.subscription_groups.read_subscription_group

- **Route**: `GET /subscription_groups/{uid}.json`
- **Server**: `production`
- **Signature**: `def read_subscription_group(uid: str, *, include: list[SubscriptionGroupIncludeOrStr] | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path · `include` — query
- **Returns (parsed)**: `FullSubscriptionGroupResponse`
- **Returns (raw)**: `ApiResult[FullSubscriptionGroupResponse, RawError]`
- **Error**: `RawError` — **Case B**

| Type | Source |
| --- | --- |
| `SubscriptionGroupIncludeOrStr` | `maxio_advanced_billing/models/enums/subscription_group_include.py` |
| `FullSubscriptionGroupResponse` | `maxio_advanced_billing/models/full_subscription_group_response.py` |

### client.subscription_groups.remove_subscription_from_group

- **Route**: `DELETE /subscriptions/{subscription_id}/group.json`
- **Server**: `production`
- **Signature**: `def remove_subscription_from_group(subscription_id: int, *, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `subscription_id`
- **Params**: `subscription_id` — path
- **Returns (parsed)**: `None`
- **Returns (raw)**: `ApiResult[None, RemoveSubscriptionFromGroupErrorBody]`
- **Error**: `RemoveSubscriptionFromGroupErrorBody` — **Case A (typed)**
- **Error arms**: `ErrorListResponse1` [422] · `RawError` [404, anything unmapped]

| Type | Source |
| --- | --- |
| `RemoveSubscriptionFromGroupErrorBody` | `maxio_advanced_billing/errors/remove_subscription_from_group_error.py` |
| `ErrorListResponse1` | `maxio_advanced_billing/models/error_list_response1.py` |

### client.subscription_groups.signup_with_subscription_group

- **Route**: `POST /subscription_groups/signup.json`
- **Server**: `production`
- **Signature**: `def signup_with_subscription_group(*, body: SubscriptionGroupSignupRequest | SubscriptionGroupSignupRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
- **Params**: `body` — JSON body
- **Returns (parsed)**: `SubscriptionGroupSignupResponse`
- **Returns (raw)**: `ApiResult[SubscriptionGroupSignupResponse, SignupWithSubscriptionGroupErrorBody]`
- **Error**: `SignupWithSubscriptionGroupErrorBody` — **Case A (typed)**
- **Error arms**: `SubscriptionGroupSignupErrorResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `SubscriptionGroupSignupRequest` | `maxio_advanced_billing/models/subscription_group_signup_request.py` |
| `SubscriptionGroupSignupRequestDict` | `maxio_advanced_billing/models/subscription_group_signup_request.py` |
| `SubscriptionGroupSignupResponse` | `maxio_advanced_billing/models/subscription_group_signup_response.py` |
| `SignupWithSubscriptionGroupErrorBody` | `maxio_advanced_billing/errors/signup_with_subscription_group_error.py` |
| `SubscriptionGroupSignupErrorResponse1` | `maxio_advanced_billing/models/subscription_group_signup_error_response1.py` |

### client.subscription_groups.update_subscription_group_members

- **Route**: `PUT /subscription_groups/{uid}.json`
- **Server**: `production`
- **Signature**: `def update_subscription_group_members(uid: str, *, body: UpdateSubscriptionGroupRequest | UpdateSubscriptionGroupRequestDict | None = None, request_options: RequestOptionsOrDict | None = None)`
  - required, positional: `uid`
- **Params**: `uid` — path · `body` — JSON body
- **Returns (parsed)**: `SubscriptionGroupResponse`
- **Returns (raw)**: `ApiResult[SubscriptionGroupResponse, UpdateSubscriptionGroupMembersErrorBody]`
- **Error**: `UpdateSubscriptionGroupMembersErrorBody` — **Case A (typed)**
- **Error arms**: `SubscriptionGroupUpdateErrorResponse1` [422] · `RawError` [anything unmapped]

| Type | Source |
| --- | --- |
| `UpdateSubscriptionGroupRequest` | `maxio_advanced_billing/models/update_subscription_group_request.py` |
| `UpdateSubscriptionGroupRequestDict` | `maxio_advanced_billing/models/update_subscription_group_request.py` |
| `SubscriptionGroupResponse` | `maxio_advanced_billing/models/subscription_group_response.py` |
| `UpdateSubscriptionGroupMembersErrorBody` | `maxio_advanced_billing/errors/update_subscription_group_members_error.py` |
| `SubscriptionGroupUpdateErrorResponse1` | `maxio_advanced_billing/models/subscription_group_update_error_response1.py` |


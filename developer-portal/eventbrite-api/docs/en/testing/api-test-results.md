# Eventbrite Event Order API Test Results

Before integrating the Eventbrite Event Order API, use this document to check where the official documentation and the actual API behavior diverge. It records parameter values that the official documentation lists as valid but the API rejects, and response field differences across endpoints — so you can rule these out before they cause a runtime error. It covers 15 test cases across single order retrieval, order lists by event, user, and organization, five filter parameters, and error responses, along with 2 findings.

> **This document tests the Event Order use cases the official Eventbrite API documentation describes, and records the results.**

<br>

**Prerequisites:**

- A valid Private Token — see the [Authentication Guide](../guides/authentication.md).
- Valid `order_id`, `event_id`, and `organization_id` values — look these up in your Eventbrite account.

<br>
<br>

## Contents

- [Test Environment](#test-environment)
- [Results Summary](#results-summary)
- [Findings](#findings)
    - [F-001 — `status=both` Documentation Mismatch](#f-001--statusboth-documentation-mismatch)
    - [F-002 — `costs` Field Inconsistency Across Endpoints](#f-002--costs-field-inconsistency-across-endpoints)
- [Test Cases and Results](#test-cases-and-results)
    - [TC-001 — Retrieve a Single Order](#tc-001--retrieve-a-single-order)
    - [TC-002 — List Orders by Event](#tc-002--list-orders-by-event)
    - [TC-003 — Verify the status Filter](#tc-003--verify-the-status-filter)
    - [TC-004 — List My Orders](#tc-004--list-my-orders)
    - [TC-005 — Verify Email Filters](#tc-005--verify-email-filters)
    - [TC-006 — Verify the refund_request_statuses Filter](#tc-006--verify-the-refund_request_statuses-filter)
    - [TC-007 — List Orders by Organization and Verify time_filter](#tc-007--list-orders-by-organization-and-verify-time_filter)
    - [TC-008 — Retrieve a Nonexistent Order](#tc-008--retrieve-a-nonexistent-order)

<br>
<br>

## Test Environment

| Item | Value |
| --- | --- |
| API version | v3 |
| Base URL | `https://www.eventbriteapi.com/v3` |
| Authentication | Bearer Token (Private Token) |
| Test tool | cURL |

<br>
<br>

## Results Summary

| TC ID | Target | Verdict |
| --- | --- | --- |
| TC-001 | Retrieve a single order | ✅ PASS |
| TC-002 | List orders by event | ✅ PASS |
| TC-003 | `status=active` | ✅ PASS |
| TC-003 | `status=inactive` | ✅ PASS |
| TC-003 | `status=both` | ❌ Documentation mismatch |
| TC-003 | `status=all_not_deleted` | ✅ PASS |
| TC-003 | `status=invalid_value` | ✅ PASS |
| TC-004 | List my orders | ✅ PASS |
| TC-005 | `only_emails` filter | ✅ PASS |
| TC-005 | `exclude_emails` filter | ✅ PASS |
| TC-006 | `refund_request_statuses` filter (5 values) | ✅ PASS (conditional) |
| TC-007 | List orders by organization — default | ✅ PASS |
| TC-007 | `time_filter=current_future` | ✅ PASS |
| TC-007 | `time_filter=past` | ✅ PASS |
| TC-008 | Nonexistent order ID | ✅ PASS |

- Total test cases: 15
- PASS: 13
- PASS (conditional): 1 — TC-006 (positive case not verified)
- Documentation mismatch: 1 — TC-003 `status=both`
- Not tested: 1 — `time_filter=all`

<br>
<br>

## Findings

<br>

### F-001 — `status=both` Documentation Mismatch

**Severity:** High

**Target endpoint:** `GET /events/{event_id}/orders?status=both`

The official documentation lists four valid values for the `status` parameter: `active`, `inactive`, `both`, and `all_not_deleted`. A request with `status=both` is rejected with a `400 ARGUMENTS_ERROR` stating the value is invalid. The cause could not be determined from testing.

| Item | Detail |
| --- | --- |
| Official documentation | Lists `both` as a valid value |
| Actual API response | `400 ARGUMENTS_ERROR` — `"both" is not a valid choice` |
| Cause | Could not be determined |
| Impact | Any implementation that relies on `both` fails at runtime |

Use `all_not_deleted` when you need to retrieve all orders. See [TC-003](#tc-003--verify-the-status-filter) for the full test.

<br>

### F-002 — `costs` Field Inconsistency Across Endpoints

**Severity:** Low

**Target endpoint:** `GET /organizations/{organization_id}/orders`

The `costs` object returned by single order retrieval, order lists by event, and order lists for the current user includes the following fields:

```
display_fee · display_price · has_gts_tax · fee_components · shipping_components · tax_components
```

Order lists by organization do not return these fields. They return only `base_price`, `eventbrite_fee`, `gross`, `payment_fee`, and `tax`. The official documentation does not state a reason for the difference.

| Endpoint | `display_fee` | `display_price` | `has_gts_tax` | `fee_components` | `shipping_components` | `tax_components` |
| --- | --- | --- | --- | --- | --- | --- |
| `GET /orders/{id}/` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `GET /events/{event_id}/orders` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `GET /users/me/orders` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `GET /organizations/{org_id}/orders` | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

If you need full cost detail from an organization-level order list, call `GET /orders/{order_id}/` for each order individually.

<br>
<br>

## Test Cases and Results

<br>

### TC-001 — Retrieve a Single Order

**Endpoint:** `GET /orders/{order_id}/`

**Purpose:** Retrieve the full record for a single order using a valid `order_id`.

**Request:**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/orders/{order_id}/"
```

**Expected result:** `200 OK`. Returns the order object with the full `costs` field set.

**Actual response:**

```json
{
  "id": "155064580...",
  "name": "Summer Lee",
  "status": "placed",
  "event_id": "1998401665...",
  "costs": {
    "base_price":    { "display": "$0.00", "currency": "USD", "value": 0, "major_value": "0.00" },
    "display_fee":   { "display": "$0.00", "currency": "USD", "value": 0, "major_value": "0.00" },
    "display_price": { "display": "$0.00", "currency": "USD", "value": 0, "major_value": "0.00" },
    "gross":         { "display": "$0.00", "currency": "USD", "value": 0, "major_value": "0.00" },
    "has_gts_tax": false,
    "fee_components": [],
    "shipping_components": [],
    "tax_components": []
  },
  "time_remaining": 0
}
```

**Verdict:** ✅ PASS — `200 OK`. Returns the order object with the full `costs` field set.

<br>

### TC-002 — List Orders by Event

**Endpoint:** `GET /events/{event_id}/orders`

**Purpose:** Retrieve the order list and pagination object for a valid `event_id`.

**Request:**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/orders"
```

**Expected result:** `200 OK`. Returns a `pagination` object and an `orders` array.

**Actual response:**

```json
{
  "pagination": {
    "object_count": 1,
    "page_number": 1,
    "page_size": 50,
    "page_count": 1,
    "has_more_items": false
  },
  "orders": [
    {
      "id": "15506458...",
      "name": "Summer Lee",
      "status": "placed",
      "event_id": "1998401665..."
    }
  ]
}
```

**Verdict:** ✅ PASS — `200 OK`. Returns a `pagination` object and an `orders` array.

<br>

### TC-003 — Verify the status Filter

**Endpoint:** `GET /events/{event_id}/orders?status={value}`

**Purpose:** Verify the behavior of the four documented `status` values and one invalid value.

Valid values: `active`, `inactive`, `both`, `all_not_deleted`

| Parameter value | Expected result | Actual result | Verdict |
| --- | --- | --- | --- |
| `active` | Returns active orders | `object_count: 1`, 1 order returned | ✅ PASS |
| `inactive` | Returns inactive orders | `object_count: 0`, 0 orders returned | ✅ PASS |
| `both` | Returns all orders | `400 ARGUMENTS_ERROR` returned | ❌ Documentation mismatch |
| `all_not_deleted` | Returns all non-deleted orders | `object_count: 1`, 1 order returned | ✅ PASS |
| `invalid_value` | Returns a `400` error | `400 ARGUMENTS_ERROR` returned | ✅ PASS |

**Actual error response for `both`:**

```json
{
  "error": "ARGUMENTS_ERROR",
  "status_code": 400,
  "error_description": "There are errors with your arguments: status - \"both\" is not a valid choice."
}
```

> **The `both` value is documented as valid, but the API rejects it.** See [Findings — F-001](#f-001--statusboth-documentation-mismatch) for details.

<br>

### TC-004 — List My Orders

**Endpoint:** `GET /users/me/orders`

**Purpose:** Retrieve the full order list for the authenticated user.

**Request:**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/users/me/orders"
```

**Expected result:** `200 OK`. Returns the current user's order list.

**Actual response:**

```json
{
  "orders": [
    {
      "id": "15506458...",
      "name": "Summer Lee",
      "status": "placed",
      "event_id": "1998401665..."
    }
  ],
  "pagination": {
    "object_count": 1,
    "page_number": 1,
    "page_size": 50,
    "page_count": 1,
    "has_more_items": false
  }
}
```

**Verdict:** ✅ PASS — `200 OK`. Returns the current user's order list.

<br>

### TC-005 — Verify Email Filters

**Endpoint:** `GET /events/{event_id}/orders?only_emails={email}` / `?exclude_emails={email}`

**Purpose:** Verify that `only_emails` and `exclude_emails` correctly filter orders by email address.

| Parameter | Expected result | Actual result | Verdict |
| --- | --- | --- | --- |
| `only_emails={email}` | Returns only orders for that email | `object_count: 1`, 1 order returned | ✅ PASS |
| `exclude_emails={email}` | Excludes orders for that email | `object_count: 0`, 0 orders returned | ✅ PASS |

**Verdict:** ✅ PASS — Both parameters correctly filter orders by email address.

<br>

### TC-006 — Verify the refund_request_statuses Filter

**Endpoint:** `GET /events/{event_id}/orders?refund_request_statuses={value}`

**Purpose:** Verify the behavior of the five documented `refund_request_statuses` values.

Valid values: `completed`, `pending`, `outside_policy`, `disputed`, `denied`

| Parameter value | Actual result | Verdict |
| --- | --- | --- |
| `completed` | `object_count: 0`, 0 orders returned | ✅ PASS |
| `pending` | `object_count: 0`, 0 orders returned | ✅ PASS |
| `outside_policy` | `object_count: 0`, 0 orders returned | ✅ PASS |
| `disputed` | `object_count: 0`, 0 orders returned | ✅ PASS |
| `denied` | `object_count: 0`, 0 orders returned | ✅ PASS |

> **Test data limitation:** The tested orders had no refund requests. All five values returned an empty array without error, but this test cannot confirm whether each value correctly returns orders in that specific state. Further verification with orders that have refund requests is needed.

**Verdict:** ✅ PASS (conditional) — The filter parameter itself works without error, but has not been verified against a positive case.

<br>

### TC-007 — List Orders by Organization and Verify time_filter

**Endpoint:** `GET /organizations/{organization_id}/orders`

**Purpose:** Retrieve an organization's order list and verify the behavior of the `time_filter` parameter.

Valid values: `all`, `past`, `current_future`

| Condition | Expected result | Actual result | Verdict |
| --- | --- | --- | --- |
| No filter (default) | Returns all orders | `object_count: 1`, 1 order returned | ✅ PASS |
| `time_filter=current_future` | Returns orders for current/future events | `object_count: 1`, 1 order returned | ✅ PASS |
| `time_filter=past` | Returns orders for past events | `object_count: 0`, 0 orders returned | ✅ PASS |
| `time_filter=all` | Returns all orders | Not tested | — |

**Verdict:** ✅ PASS — Correctly filters by event time for the values that were tested.

> **Note:** The `costs` object in the organization-level order list response returns fewer fields than the other endpoints. See [Findings — F-002](#f-002--costs-field-inconsistency-across-endpoints) for details.

<br>

### TC-008 — Retrieve a Nonexistent Order

**Endpoint:** `GET /orders/{order_id}/`

**Purpose:** Verify that the API returns a `404` error for an invalid `order_id`.

**Request:**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/orders/00000000000/"
```

**Expected result:** `404 NOT_FOUND` error.

**Actual response:**

```json
{
  "status_code": 404,
  "error": "NOT_FOUND",
  "error_description": "The path you requested does not exist."
}
```

**Verdict:** ✅ PASS — `404 NOT_FOUND`. Responds in the standard error format.

<br>
<br>

## Next Steps

- [Error Reference](../api/error-reference.md)
- [Response Handling Guide](../guides/response-handling.md)
- [API Reference](../api/api-reference.md)

<br>
<br>
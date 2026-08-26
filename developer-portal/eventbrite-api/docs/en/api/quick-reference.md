# Quick Reference

Fast lookup for Eventbrite API essentials — base URL, authentication,
endpoints, headers, response fields, status codes, and rate limits. Use
this page when you already know the API and need a quick reminder.

For full parameter definitions, request body schemas, and response
field descriptions, see the [API Reference](../api/api-reference.md).

<br>
<br>

## Table of Contents

- [Base URL](#base-url)
- [Authentication](#authentication)
- [Event Endpoints](#event-endpoints)
- [Request Headers](#request-headers)
- [Response Fields](#response-fields)
- [HTTP Status Codes](#http-status-codes)
- [Rate Limits](#rate-limits)
- [Quick Request Examples](#quick-request-examples)

<br>
<br>

## Base URL

```
https://www.eventbriteapi.com/v3
```

- API version: `v3`
- All endpoints are prefixed with `/v3/`
- Request and response format: `application/json`

👉 [API Reference](../api/api-reference.md#base-url-and-versioning)

<br>
<br>

## Authentication

Include your token in the `Authorization` header of every request:

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
```

- Store your token in an environment variable — never in source code.
- Generate or regenerate tokens at
[Account Settings → API Keys](https://www.eventbrite.com/account-settings/apps).

👉 [Authentication Guide](../guides/authentication.md)

<br>
<br>

## Event Endpoints

| Action | Method | Endpoint |
| --- | --- | --- |
| List Events by Organization | `GET` | `/organizations/{organization_id}/events/` |
| Create an Event | `POST` | `/organizations/{organization_id}/events/` |
| Retrieve an Event | `GET` | `/events/{event_id}/` |
| Update an Event | `POST` | `/events/{event_id}/` |
| Publish an Event | `POST` | `/events/{event_id}/publish/` |
| Delete an Event | `DELETE` | `/events/{event_id}/` |

👉 [API Reference](../api/api-reference.md)

<br>
<br>

## Request Headers

| Header | Value |
| --- | --- |
| `Authorization` | `Bearer YOUR_PRIVATE_TOKEN` |
| `Content-Type` | `application/json` |

Include `Content-Type` for POST requests. It's optional for GET and
DELETE requests.

<br>
<br>

## Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `id` | String | Unique event identifier |
| `name.text` | String | Event title as plain text |
| `name.html` | String | Event title in HTML format |
| `description.text` | String | Event description as plain text. `null` for drafts with no description. |
| `description.html` | String | Event description in HTML format. `null` for drafts with no description. |
| `status` | String | Event status — `draft`, `live`, `started`, `ended`, `completed`, `canceled` |
| `start.utc` | String | Start time in UTC — format: `YYYY-MM-DDTHH:MM:SSZ` |
| `start.local` | String | Start time in the event's local timezone |
| `end.utc` | String | End time in UTC — format: `YYYY-MM-DDTHH:MM:SSZ` |
| `end.local` | String | End time in the event's local timezone |
| `capacity` | Integer | Maximum number of attendees. `null` when not set. |
| `currency` | String | ISO 4217 currency code (for example, `USD`, `EUR`) |
| `url` | String | Public URL of the event on Eventbrite |

👉 [Response Handling Guide](../guides/response_handling.md)

<br>
<br>

## HTTP Status Codes

| Code | Error code | Meaning | Action |
| --- | --- | --- | --- |
| `200` | — | Request successful | — |
| `400` | `FIELD_INVALID` | Invalid or missing field | Check request body against API Reference |
| `400` | `VENUE_AND_ONLINE` | Venue and `online_event` both set | Remove venue or set `online_event` to false |
| `400` | `BAD_CONTINUATION_TOKEN` | Pagination token malformed or expired | Restart pagination from page 1 |
| `401` | `NO_AUTH` | Missing or invalid token | Check Authorization header format and token value |
| `403` | `NOT_AUTHORIZED` | Insufficient permissions | Verify the resource belongs to your account |
| `404` | `NOT_FOUND` | Resource not found | Verify the `event_id` is correct |
| `429` | — | Rate limit exceeded | Check `X-Apiary-RateLimit-Remaining` header |

👉 [Error Reference](../api/error-reference.md)

<br>
<br>

## Rate Limits

| Limit type | Limit |
| --- | --- |
| All requests | 2,000 / hour |
| All requests | 48,000 / day |
| Create or Publish Event | 200 / hour |

### Rate limit headers

| Header | Description |
| --- | --- |
| `X-Apiary-RateLimit-Limit` | Maximum requests allowed in the current window |
| `X-Apiary-RateLimit-Remaining` | Requests remaining before the limit resets |

👉 [Error Reference — 429](../api/error-reference.md#429-too-many-requests)

<br>
<br>

## Quick Request Examples

### Retrieve an event

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

### List events by organization

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

👉 [Code Examples](../examples/code-examples.md)

<br>
<br>

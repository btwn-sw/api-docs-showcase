# Quick Reference Guide

This Quick Reference Guide provides a concise overview of the most commonly used Eventbrite API information. It is intended for developers who are already familiar with the API and need fast access to essential details.

### Table of Contents

- [Base Information](#base-information)
- [Authentication](#authentication)
- [Common Event Endpoints](#common-event-endpoints)
- [Common Request Headers](#common-request-headers)
- [Common Response Fields](#common-response-fields)
- [Common HTTP Status Codes](#common-http-status-codes)
- [Rate Limiting](#rate-limiting)
- [Code Example](#code-example)
- [Common Troubleshooting Shortcuts](#common-troubleshooting-shortcuts)
- [When to Use This Guide](#when-to-use-this-guide)

<br>

## Base Information

### Base URL

```arduino
https://www.eventbriteapi.com/v3
```

### API Version

- `v3`

### Content-Type

```bash
application/json
```

<br>

## Authentication

All Eventbrite API requests require authentication using a **Private OAuth Token**. 

### Header Format

```bash
Authorization: Bearer PERSONAL_OAUTH_TOKEN
```

- Tokens should be stored securely (e.g., environment variables).
- Do not expose tokens in client-side code.

See [Authentication Guide](../guides/authentication.md) for details.

<br>

## Common Event Endpoints

### Endpoints

| Action | Method | Endpoint |
| --- | --- | --- |
| Create an Event | POST | `/organizations/{organization_id}/events/` |
| Retrieve an Event | GET | `/events/{event_id}` |
| Update an Event | POST | `/events/{event_id}` |
| Delete an Event | DELETE | `/events/{event_id}` |

See [API Reference](../api/api-reference.md) for details.

<br>

## Common Request Headers

```bash
Authorization: Bearer PERSONAL_OAUTH_TOKEN
Content-Type: application/json
```

<br>

## Common Response Fields

| Field | Description |
| --- | --- |
| `id` | Event identifier |
| `name.text` | Event title (plain text) |
| `description.html` | HTML-formatted description |
| `status` | Event lifecycle state |
| `start.utc` | Event start time |
| `end.utc` | Event end time |

See [Response Handling Guide](../guides/response_handling.md) for details.

<br>

## Common HTTP Status Codes

| Code | Meaning |
| --- | --- |
| `200` | Request successful |
| `400` | Invalid request |
| `401` | Unauthorized (authentication issue) |
| `403` | Forbidden (permission issue) |
| `404` | Resource not found |

See [Troubleshooting](../guides/troubleshooting.md) for error handling details.

<br>

## Rate Limiting

### Default Limits

- 2,000 requests/hour
- 48,000 requests/day

### Rate Limit Headers

```bash
X-Apiary-RateLimit-Limit
X-Apiary-RateLimit-Remaining
```

See API Reference - [Rate Limiting](../api/api-reference.md/#rate-limiting) for details.

<br>

## Code Example

### Retrieve Event

```bash
curl --request GET \
  --header "Authorization: Bearer PERSONAL_OAUTH_TOKEN" \
  https://www.eventbriteapi.com/v3/events/{event_id}/
```

See [Code Examples](../examples/code-examples.md) for more details.

<br>

## Common Troubleshooting Shortcuts

| Code | Error | Description |
| --- | --- | --- |
| `400` | BAD_CONTINUATION_TOKEN | Invalid request or conflicting parameters |
| `401` | NO_AUTH | Authentication not provided or invalid |
| `403` | NOT_AUTHORIZED | Insufficient permissions |
| `404`  | NOT_FOUND | Event not found |

See [Troubleshooting Guide](../guides/troubleshooting.md) for full diagnostics.

<br>

## When to Use This Guide

### Use this guide when you:

- Need a quick reminder of endpoints or headers.
- Debug a request quickly.
- Already know the API but forgot exact syntax.

<br>

---

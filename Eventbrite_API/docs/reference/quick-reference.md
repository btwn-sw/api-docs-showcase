# Quick Reference Guide

---

This Quick Reference Guide provides a concise overview of the most commonly used Eventbrite API information. It is intended for developers who are already familiar with the API and need fast access to essential details.

### Table of Contents

- Base Information
- Authentication
- Common Event Endpoints
- Common Request Headers
- Common Response Fields
- Common HTTP Status Codes
- Rate Limiting
- Code Example
- Common Troubleshooting Shortcuts
- When to Use This Guide

---

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

---

## Authentication

All Eventbrite API requests require authentication using a **Private OAuth Token**. 

### Header Format

```bash
Authorization: Bearer PERSONAL_OAUTH_TOKEN
```

- Tokens should be stored securely (e.g., environment variables).
- Do not expose tokens in client-side code.

See Authentication Guide for details.

---

## Common Event Endpoints

### Endpoints

| Action | Method | Endpoint |
| --- | --- | --- |
| Create an Event | POST | `/organizations/{organization_id}/events/` |
| Retrieve an Event | GET | `/events/{event_id}` |
| Update an Event | POST | `/events/{event_id}` |
| Delete an Event | DELETE | `/events/{event_id}` |

See API Reference for details.

---

## Common Request Headers

```bash
Authorization: Bearer PERSONAL_OAUTH_TOKEN
Content-Type: application/json
```

---

## Common Response Fields

| Field | Description |
| --- | --- |
| `id` | Event identifier |
| `name.text` | Event title (plain text) |
| `description.html` | HTML-formatted description |
| `status` | Event lifecycle state |
| `start.utc` | Event start time |
| `end.utc` | Event end time |

See Response Handling Guide for details.

---

## Common HTTP Status Codes

| Code | Meaning |
| --- | --- |
| `200` | Request successful |
| `400` | Invalid request |
| `401` | Unauthorized (authentication issue) |
| `403` | Forbidden (permission issue) |
| `404` | Resource not found |

See Troubleshooting for error handling details.

---

## Rate Limiting

### Default Limits

- 2,000 requests/hour
- 48,000 requests/day

### Rate Limit Headers

```bash
X-Apiary-RateLimit-Limit
X-Apiary-RateLimit-Remaining
```

See API Reference - Rate Limiting for details.

---

## Code Example

### Retrieve Event

```bash
curl --request GET \
  --header "Authorization: Bearer PERSONAL_OAUTH_TOKEN" \
  https://www.eventbriteapi.com/v3/events/{event_id}/
```

See Code Examples for more details.

---

## Common Troubleshooting Shortcuts

| Code | Error | Description |
| --- | --- | --- |
| `400` | BAD_CONTINUATION_TOKEN | Invalid request or conflicting parameters |
| `401` | NO_AUTH | Authentication not provided or invalid |
| `403` | NOT_AUTHORIZED | Insufficient permissions |
| `404`  | NOT_FOUND | Event not found |

See Troubleshooting Guide for full diagnostics.

---

## When to Use This Guide

### Use this guide when you:

- Need a quick reminder of endpoints or headers.
- Debug a request quickly.
- Already know the API but forgot exact syntax.

---

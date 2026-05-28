# Response Handling Guide

Eventbrite API responses contain nested JSON objects, optional fields, and
HTML-formatted content. This guide shows you how to read the response
structure, handle nullable and optional fields safely, and process
HTML content correctly.

> **Note:** This guide focuses on response consumption. For endpoint
schemas and parameter definitions, see the
[API Reference](../api/api-reference.md).
> 

<br>

## Table of Contents

- [Response Structure](#response-structure)
- [Handling HTML-Formatted Content](#handling-html-formatted-content)
- [Handling Optional and Nullable Fields](#handling-optional-and-nullable-fields)
- [Error Responses](#error-responses)
- [Best Practices](#best-practices)

<br>

## Response Structure

### Event Object

Most Event-related endpoints return an Event object. The object contains
four categories of fields:

| Category | Fields |
| --- | --- |
| Identification | `id` |
| Text content | `name`, `description` — each contains both `text` and `html` variants |
| Scheduling | `start`, `end` — each contains `utc`, `local`, and `timezone` |
| Metadata | `status`, `currency`, `capacity`, `url` |

**Example response:**

```json
{
  "id": "12345",
  "name": {
    "text": "My Event",
    "html": "<p>My Event</p>"
  },
  "description": {
    "text": "Event description here",
    "html": "<p>Event description here</p>"
  },
  "start": {
    "timezone": "America/Los_Angeles",
    "utc": "2026-06-01T09:00:00Z",
    "local": "2026-06-01T02:00:00"
  },
  "end": {
    "timezone": "America/Los_Angeles",
    "utc": "2026-06-01T17:00:00Z",
    "local": "2026-06-01T10:00:00"
  },
  "status": "live",
  "currency": "USD",
  "capacity": 100,
  "url": "<https://www.eventbrite.com/e/12345>"
}
```

### Key Fields

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | String | Yes | Unique identifier for the event |
| `name` | Object | Yes | Event name — contains `text` (plain text) and `html` (HTML-formatted) variants |
| `description` | Object | No | Event description — contains `text` and `html` variants. May be `null` for draft events. |
| `status` | String | Yes | Event status — `draft`, `live`, `started`, `ended`, `completed`, or `canceled` |
| `start` | Object | Yes | Start time — contains `utc`, `local`, and `timezone` |
| `end` | Object | Yes | End time — contains `utc`, `local`, and `timezone` |
| `hide_start_date` | Boolean | No | When `true`, the start date is hidden from the public event page |
| `hide_end_date` | Boolean | No | When `true`, the end date is hidden from the public event page |

<br>

## Handling HTML-Formatted Content

Eventbrite returns HTML content in two patterns.

### Pattern 1 — Full HTML response

Some endpoints return a raw HTML string as the entire response body.

**Endpoint:**

```
GET /events/{event_id}/description/
```

**Response:**

```json
{
  "description": "<div>Example summary!</div>\\n<div><p>My <em>event's</em> description goes <strong>here</strong>.</p></div>"
}
```

Use an HTML parser to render this content. Do not display the raw string
directly to users.

### Pattern 2 — Dual text/html fields

Some endpoints return objects that contain both a plain text variant
and an HTML variant for the same content.

**Example — Ticket Buyer Settings:**

```
GET /events/{event_id}/ticket_buyer_settings/
```

**Response:**

```json
{
  "confirmation_message": {
    "text": "Confirmation Message",
    "html": "<h1>Confirmation Message</h1>"
  },
  "instructions": {
    "text": "Instructions",
    "html": "<h1>Instructions</h1>"
  }
}
```

Use `text` when you need plain string content — for example, in
notifications or metadata. Use `html` when rendering content in a
web interface.

<br>

## Handling Optional and Nullable Fields

Not all fields are present in every response. Fields may be missing or
`null` in the following cases:

- Draft events with incomplete data
- Fields the token does not have permission to access
- Fields the event organizer has not filled in

Check for field existence before accessing nested properties.

**JavaScript example:**

```jsx
const name = event?.name?.text ?? 'Untitled Event';
const description = event?.description?.text ?? '';
const capacity = event?.capacity ?? null;
```

**Python example:**

```python
name = event.get('name', {}).get('text', 'Untitled Event')
description = event.get('description', {}).get('text', '')
capacity = event.get('capacity')
```

<br>

## Error Responses

All Event endpoints return a consistent error format:

```json
{
  "error": "ERROR_CODE",
  "error_description": "Human-readable explanation",
  "status_code": 400
}
```

### Common Error Codes

| Status code | Error code | Cause | Action |
| --- | --- | --- | --- |
| `400` | `FIELD_INVALID` | The request contains an invalid or conflicting parameter | Check the request body and parameters against the [API Reference](../api/api-reference.md) |
| `401` | `NO_AUTH` | No token provided, or the token is invalid | Include a valid Private Token in the `Authorization` header |
| `403` | `NOT_AUTHORIZED` | The token does not have permission to access this resource | Check token scopes or use a token with the correct permissions |
| `404` | `NOT_FOUND` | The requested event does not exist | Verify the `event_id` is correct and the event has not been deleted |

<br>

## Best Practices

### Extract only the fields you need

Avoid storing the full response object. Access only the fields your
application uses.

```jsx
// Extract only what you need
const { id, status } = response;
const name = response.name?.text;
const startUtc = response.start?.utc;
```

### Separate HTML from plain text

Never use `html` fields where plain text is expected, and never pass
`text` fields to an HTML renderer.

```jsx
// Display in UI — use html
container.innerHTML = event.name.html;

// Pass to notification system — use text
sendNotification({ title: event.name.text });
```

### Convert errors to user-facing messages

Map API error codes to messages your users understand.

```jsx
const ERROR_MESSAGES = {
  NO_AUTH:        'Authentication failed. Check your API token.',
  NOT_AUTHORIZED: 'You do not have permission to access this event.',
  NOT_FOUND:      'This event could not be found.',
  FIELD_INVALID:  'The request contained an invalid value.',
};

function handleApiError(error) {
  return ERROR_MESSAGES[error.error] ?? 'An unexpected error occurred.';
}
```

<br>

## Next Steps

- [API Reference]()
- [Authentication Guide]()
- [Code Examples]()

<br>

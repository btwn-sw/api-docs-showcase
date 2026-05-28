# Response Handling Guide

The Eventbrite API returns structured JSON responses that often include nested objects, optional fields, and HTML-formatted content. This guide explains how to safely and consistently consume Eventbrite API responses in real-world applications.

***Note**: This guide complements the API Reference and focuses on response consumption rather than schema definition.* 

### Table of Contents

- [Eventbrite API Responses](#eventbrite-api-responses)
- [Common Response Structure](#common-response-structure)
- [Handling HTML-Formatted Content](#handling-html-formatted-content)
- [Error Responses](#error-responses)
- [Best Practices](#best-practices)

<br>

## Eventbrite API Responses

- Responses are structured as nested JSON objects.
- Not all fields are guaranteed to be present.
- Some fields contain pre-formatted HTML content.

<br>

## Common Response Structure

### Event Object

Most Event-related endpoints return an Event object. An event object typically includes: 

- Identification fields: `id`
- Text and HTML content: `name`, `description`
- Scheduling information: `start`, `end`
- Status and metadata fields: `currency`, `capacity`, `source`

```json
{
  "id": "12345",
  "name": {
    "text": "Some text",
    "html": "<p>Some text</p>"
  },
  "description": {
    "text": "Some text",
    "html": "<p>Some text</p>"
  },
  "start": {
    "timezone": "America/Los_Angeles",
    "utc": "2018-05-12T02:00:00Z",
    "local": "2018-05-11T19:00:00"
  },
  "end": {
    "timezone": "America/Los_Angeles",
    "utc": "2018-05-12T02:00:00Z",
    "local": "2018-05-11T19:00:00"
  },
  "url": "https://www.eventbrite.com/e/45263283700",
  "vanity_url": "https://testevent.eventbrite.com",
  "created": "2017-02-19T20:28:14Z",
  "changed": "2017-02-19T20:28:14Z",
  "published": "2017-02-19T20:28:14Z",
  "status": "live",
  "currency": "USD",
  "show_remaining": true,
  "password": "12345",
  "capacity": 100,
  "source": "api",
  "version": "null",
  },
    ...
    
 }
```

### Key Fields

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | multipart-text | Yes | Event name. |
| `description`  | multipart-text | optional | Event description. |
| `status`  | string | Yes | Event status which can be `draft`, `live`, `started`, `ended`, `completed`, and `canceled`. |
| `hide_start_date`  | boolean | Yes | Start date for event scheduling (UTC timestamps). |
| `hide_end_date` | boolean | Yes | End date for event scheduling (UTC timestamps). |

<br>

## Handling HTML-Formatted Content

Sometimes, Eventbrite API fields contain HTML-formatted content.

### Fully HTML-Formatted Content

Some endpoint returns raw HTML content.

**Event Description Endpoint**

```bash
**GET** /events/{event_id}/description/
```

**Response Body**

```html
{
 "description: <div>Example summary!<\/div>\n<div><P>My <EM>event's<\/EM> description would go <STRONG>here<\/STRONG>.<\/P><\/div>"
}
```

### Partially HTML-formatted Content

Some endpoints return objects containing both plain text and HTML variants.

**Ticket Buyer Settings Endpoint**

```bash
**GET** /events/{event_id}/ticket_buyer_settings/
```

**Response Body**

```json
{
    "confirmation_message": {
        "text": "<H1>Confirmation Message</H1>",
        "html": "Confirmation Message"
    },
    "instructions": {
        "text": "<H1>Instructions</H1>",
        "html": "Instructions"
    },
    "sales_ended_message": {
        "text": "<H1>Sales Ended Message</H1>",
        "html": "Sales Ended Message"
    },
     "survey_info": {
        "text": "<b>Filling in this survey is required to attend the event</b>",
        "html": "Filling in this survey is required to attend the event"
    },
    "event_id": "43253626762",
    "survey_name": "Registration Page Title",
    "refund_request_enabled": true,
    "allow_attendee_update": true,
    "survey_time_limit": 15,
    "redirect_url": null,
    "survey_respondent": "attendee",
    "survey_ticket_classes": ["125", "374"]
}
```

### Handling Partial or Nullable Fields

The Eventbrite API responses may include optional or conditional fields. Check field existence before accessing nested properties.

**Common scenarios**:

- Draft events with incomplete data
- Fields set to `null`
- Fields omitted due to permission constraints.

<br>

## Error Responses

Event-related endpoints may return error responses when a request fails.

### 400 Bad Request

- Response: The token is invalid or missing. Include a valid private token.
    
    ```json
    {
      "error_description": "",
      "error_detail": {},
      "status_code": 400,
      "error": "FIELD_INVALID"
    }
    ```
    

### **403 Forbidden**

- Response: The token is valid, but no permission to access the content.
    
    ```json
    {
      "error_description": "",
      "error_detail": {},
      "status_code": 403,
      "error": "NOT_AUTHORIZED"
    }
    ```
    

### **404 Not Found**

- Response: The requested resource does not exist or is no longer accessible.
    
    ```json
    {
      "error_description": "",
      "error_detail": {},
      "status_code": 404,
      "error": "NOT_FOUND"
    }
    ```

<br>

## Best Practices

- Extract only the fields required for use cases.
- Validate field existence before accessing properties.
- Treat HTML fields separately from plain text fields.
- Convert API error responses into user-friendly messages.

<br>

## Next steps

- [API Reference](../api/api-reference.md)
- [Authentication Guide](../guides/authentication.md)
- [Code Examples](../examples/code-examples.md)

<br>

---
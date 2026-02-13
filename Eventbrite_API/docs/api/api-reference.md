# Eventbrite API Reference

---

The Eventbrite API provides programmatic access to Eventbrite data and functionality available at the [API Reference](https://www.eventbrite.com/platform/api). This reference documents a focused subset of **Event-related endpoints**. Eventbrite refers to API resources as **objects**.

## Table of Contents

- Overview
- Authentication
- Rate Limiting
- Error Handling
- Event Endpoints

---

## Overview

### Base URL

```arduino
https://www.eventbriteapi.com/v3
```

### API Version

- Eventbrite API version: `v3`
- All endpoints are prefixed with `/v3/`

### Content-Type

- Request: `application/json`
- Response: `application/json`

### **Try-it-out**

Eventbrite API provides try-it-out features in the right pane to call requests routed via *Apiary*. You can see the detailed information about endpoints and try out a request by clicking the “*Blocked title*” for endpoints (e.g., **Retrieve an Event**). 

![image.png](attachment:a677808d-48ee-4973-8847-cff6659162da:image.png)

---

## Authentication

### OAuth 2.0 Authentication

**Authentication Header (Recommended)**

- Include your private token in place of `PERSONAL_OAUTH_TOKEN`.
    
    ```bash
    Authorization: Bearer PERSONAL_OAUTH_TOKEN
    ```
    
- This reference assumes the use of a **Private token**.

**API Authentication**

- All Eventbrite API requests require authentication.
- See the Authentication Guide for the detailed authentication flow.

---

## Rate Limiting

Eventbrite enforces rate limits on API calls across all integrated applications to ensure platform stability.

### Default Limits

- 2,000 calls/hour
- 48,000 calls/day

### Action-Specific Limits

- Create and Copy Event: 200 calls/hour
- Publish Event: 200 calls/hour

### Rate Limit Headers

- Some APIs indicate a short-term or endpoint-level rate limit in the response headers:
    
    ```bash
    X-Apiary-RateLimit-Limit
    X-Apiary-RateLimit-Remaining
    ```
    

---

## Error Handling

Event-related endpoints may return the following error responses. For error handling strategies, see the Response Handling Guide.

### HTTP Status Codes

| Code | Error | Description |
| --- | --- | --- |
| `400` | BAD_CONTINUATION_TOKEN | Invalid request or conflicting parameters |
| `401` | NO_AUTH | Authentication not provided or invalid |
| `403` | NOT_AUTHORIZED | Insufficient permissions |
| `404`  | NOT_FOUND | Event not found |

### Error Response Example

```json
{
    "error": "VENUE_AND_ONLINE",
    "error_description": "You cannot both specify a venue and set online_event",
    "status_code": 400
}
```

---

## Event Endpoints

### **Create an Event**

Creates a new event under an organization.

**Endpoint**

```bash
**POST** /organizations/{organization_id}/events/
```

**Path Parameters**

| Name | Type | Required | Description | Example |
| --- | --- | --- | --- | --- |
| `organization_id` | String | Yes | ID for an organization owns the event | `12345` |

**Request Body**

```json
{
  "event": {
    "name": {
      "html": "<p>Some text</p>"
    },
    "description": {
      "html": "<p>Some text</p>"
    },
    "start": {
      "timezone": "UTC",
      "utc": "2018-05-12T02:00:00Z"
    },
    "end": {
      "timezone": "UTC",
      "utc": "2018-05-12T02:00:00Z"
    },
    "currency": "USD",
    "online_event": false,
    "organizer_id": "",
    "listed": false,
    "shareable": false,
    "invite_only": false,
    "show_remaining": true,
    "password": "12345",
    "capacity": 100,
    "is_reserved_seating": true,
    "is_series": true,
    "show_pick_a_seat": true,
    "show_seatmap_thumbnail": true,
    "show_colors_in_seatmap_thumbnail": true,
    "locale": "de_AT"
  }
}
```

**Response Example**

200 OK: 

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
  "version": "null"
  },
    ...
    
 }
```

### **Retrieve an Event**

Retrieves details for a specific event.

**Endpoint**

```bash
**GET** /events/{event_id}
```

**Parameters**

| Name | Type | Required | Description | Example |
| --- | --- | --- | --- | --- |
| `event_id` | String | Required | Attendee’s Event | `12345` |

**Request**

```bash
curl --request GET \
     --header "Authorization: Bearer PERSONAL_OAUTH_TOKEN" \
  'https://www.eventbriteapi.com/v3/events/{event_id}/'
```

**Response Example**

200 OK: 

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
  "version": "null"
  },
    ...
    
 }
```

### **Update an Event**

Updates an existing event.

**Endpoint**

```bash
**POST** /events/{event_id}
```

**Path Parameters**

| Name | Type | Required | Description | Example |
| --- | --- | --- | --- | --- |
| `event_id` | String | Required | Attendee’s Event | `12345` |

**Request Body**

```json
{
  "event": {
    "name": {
      "html": "<p>Some text</p>"
    },
    "description": {
      "html": "<p>Some text</p>"
    },
    "start": {
      "timezone": "UTC",
      "utc": "2018-05-12T02:00:00Z"
    },
    "end": {
      "timezone": "UTC",
      "utc": "2018-05-12T02:00:00Z"
    },
    "currency": "USD",
    "online_event": false,
    "organizer_id": "",
    "listed": false,
    "shareable": false,
    "invite_only": false,
    "show_remaining": true,
    "password": "12345",
    "capacity": 100,
    "source": "api",
	  "version": "null"
    "is_reserved_seating": true,
    "is_series": true,
    "show_pick_a_seat": true,
    "show_seatmap_thumbnail": true,
    "show_colors_in_seatmap_thumbnail": true
  }
}
```

**Response Example**

200 OK: 

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
  "version": "null"
  },
    ...
    
 }
```

### **Delete an Event**

Deletes an event.

**Endpoint**

```bash
**DELETE** /events/{event_id}
```

**Path Parameters**

| Name | Type | Required | Description | Example |
| --- | --- | --- | --- | --- |
| `event_id` | String | Required | Attendee’s Event | `12345` |

**Request**

```bash
curl --include \
     --request DELETE \
     --header "Authorization: Bearer PERSONAL_OAUTH_TOKEN" \
     --header "Content-Type: application/json" \
  'https://www.eventbriteapi.com/v3/events/12345/'
```

**Response Example**

200 OK: 

```json
{
  "deleted": true
}
```

---

## Nest steps

- Authentication Guide
- Response Handling Guide
- Code Examples
- SDKs
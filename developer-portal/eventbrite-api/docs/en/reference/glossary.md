# Glossary

Look up definitions for the key terms used across the Eventbrite API
documentation, so you don't have to dig through multiple guides to
understand a term. Terms are listed alphabetically; when a term first
appears in another document, it links back to its definition here.

<br>
<br>

## Table of Contents

- [A](#a)
    - [Access Token](#access-token)
    - [API Key](#api-key)
    - [Authorization Header](#authorization-header)
- [B](#b)
    - [Base URL](#base-url)
- [C](#c)
    - [Client Secret](#client-secret)
    - [Continuation Token](#continuation-token)
    - [Currency Code](#currency-code)
- [D](#d)
    - [Draft](#draft)
- [E](#e)
    - [Event](#event)
    - [Event ID](#event-id-event_id)
- [H](#h)
    - [has_more_items](#has_more_items)
- [I](#i)
    - [IANA Timezone](#iana-timezone)
- [L](#l)
    - [Live](#live)
- [M](#m)
    - [Multipart Text](#multipart-text)
- [O](#o)
    - [OAuth 2.0](#oauth-20)
    - [Organization](#organization)
    - [Organization ID](#organization-id-organization_id)
- [P](#p)
    - [Pagination](#pagination)
    - [Private Token](#private-token)
- [R](#r)
    - [Rate Limit](#rate-limit)
- [S](#s)
    - [Status (Event)](#status-event)
- [U](#u)
    - [UTC (Coordinated Universal Time)](#utc-coordinated-universal-time)

<br>
<br>

## A

### Access Token
A credential returned by the server-side OAuth flow that authorizes API
requests on behalf of a specific user. Used in the `Authorization` header
in the same way as a Private Token. See [Authentication Guide](../guides/authentication.md).

### API Key
An application-level credential issued from the Eventbrite Developer
Dashboard. Used together with a Client Secret in the server-side OAuth
flow. Not the same as a Private Token. See
[Authentication Guide](../guides/authentication.md).

### Authorization Header
The HTTP request header used to authenticate Eventbrite API requests.
Must be formatted as `Authorization: Bearer YOUR_PRIVATE_TOKEN`.
Requests without a valid Authorization header return `401 Unauthorized`.

<br>

## B

### Base URL
The root URL that all Eventbrite API endpoints are prefixed with:
`https://www.eventbriteapi.com/v3`. All endpoint paths begin with `/v3/`.

<br>

## C

### Client Secret
A private credential paired with an API Key. Used together in the
server-side OAuth flow to exchange an authorization code for an Access
Token. Keep it confidential and never expose it in client-server code.

### Continuation Token
A string value returned in paginated responses when additional pages are
available. Pass this value as the `continuation` query parameter in the
next request to retrieve the following page of results. When all records
have been retrieved, the API returns an empty list instead of a new token.
See [Pagination Guide](../guides/pagination.md).

### Currency Code
A three-letter code that identifies a currency, following the
ISO(International Organization for Standardization) 4217 standard. Example: `USD` for US Dollar, `KRW` for South Korean Won. Required when creating an event.

<br>

## D

### Draft
The initial status of a newly created event. A draft event is not publicly
visible on Eventbrite. Use the Publish endpoint to make it live.
See [API Reference — Publish an Event](../api/api-reference.md).

<br>

## E

### Event
The core resource in the Eventbrite API. An event is owned by one
organization and can be in one of the following statuses: `draft`, `live`,
`started`, `ended`, `completed`, or `canceled`.

### Event ID (`event_id`)
The unique numeric identifier for an event. Found at the end of the
event's Eventbrite URL:
`https://www.eventbrite.com/e/my-event-name-{event_id}`.
Required for all single-event operations (Retrieve, Update, Publish, Delete).

<br>

## H

### `has_more_items`
A boolean field in the pagination object. When `true`, additional pages
of results are available and the response includes a `continuation` token.
When `false`, the current page is the last one. Always check this field
before requesting the next page. See [Pagination Guide](../guides/pagination.md).

<br>

## I

### IANA Timezone
A timezone identifier from the IANA(International Assigned Numbers Authority) Time Zone Database, used to specify the local time of an event. Example: `America/Los_Angeles`, `Asia/Seoul`,
`UTC`. Required alongside UTC time fields when creating or updating events.

<br>

## L

### Live
An event status indicating the event has been published and is publicly
visible on Eventbrite. Transitions from `draft` via the Publish endpoint.

<br>

## M

### Multipart Text
A field type used for event names and descriptions. Returns two variants
in the API response: `text` (plain text, for use in notifications and
logs) and `html` (HTML-formatted, for rendering in a web interface).
Never pass the `html` variant where plain text is expected.
See [Response Handling Guide](../guides/response_handling.md).

<br>

## O

### OAuth 2.0
The authorization framework used by the Eventbrite API. Allows an
application to access the API on behalf of a user. Eventbrite supports
both Personal Tokens and the full server-side OAuth flow. See [Authentication Guide](../guides/authentication.md).

### Organization
The Eventbrite account entity that owns events. All events are created under an organization. The `organization_id` is required when creating an event or listing events.

### Organization ID (`organization_id`)
The unique identifier for an Eventbrite organization. Required for
Create Event and List Events by Organization. Retrieve your
organization ID from `GET /v3/users/me/organizations/`.

<br>

## P

### Pagination
The mechanism the Eventbrite API uses to return large sets of results
across multiple pages. Each paginated response includes a `pagination`
object containing `has_more_items`, `continuation`, `page_number`,
`page_size`, `page_count`, and `object_count`.
See [Pagination Guide](../guides/pagination.md).

### Private Token
A long-lived personal access token tied to a specific Eventbrite account.
Used in the `Authorization` header to authenticate API requests. Suitable
for testing and server-side integrations where only one account is
involved. Generate from the
[Eventbrite API Keys page](https://www.eventbrite.com/account-settings/apps).
See [Authentication Guide](../guides/authentication.md).

<br>

## R

### Rate Limit
A cap on the number of API requests allowed within a time window.
Eventbrite enforces 2,000 requests/hour and 48,000 requests/day for all
requests, with a separate limit of 200 requests/hour for Create Event and
Publish Event. Exceeding a rate limit returns `429 Too Many Requests`.
See [Error Reference](../api/error-reference.md).

<br>

## S

### Status (Event)
The current state of an event. Possible values:

| Status | Description |
| --- | --- |
| `draft` | Created but not yet published. Not publicly visible. |
| `live` | Published and publicly visible on Eventbrite. |
| `started` | The event start time has passed. |
| `ended` | The event end time has passed. |
| `completed` | The event has been marked as completed. |
| `canceled` | The event has been canceled. |

<br>

## U

### UTC (Coordinated Universal Time)
The time standard used for all date and time fields in the Eventbrite API.
All `utc` fields follow the format `YYYY-MM-DDTHH:MM:SSZ`.
Always provide a corresponding IANA timezone alongside UTC fields so
Eventbrite can calculate the correct local time for attendees.

<br>
<br>

## Next Steps

- [API Reference](../api/api-reference.md)
- [Authentication Guide](../guides/authentication.md)
- [Response Handling Guide](../guides/response_handling.md)
- [Pagination Guide](../guides/pagination.md)

<br>
<br>

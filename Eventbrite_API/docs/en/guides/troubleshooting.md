# Troubleshooting Guide

Find the cause of a failing Eventbrite API request and fix it. Each section
below covers a specific error or symptom, its most common causes, and the
steps to resolve it.

<br>

## Table of Contents

- [401 Authentication Errors](#401-authentication-errors)
- [403 Permission Errors](#403-permission-errors)
- [404 Resource Not Found](#404-resource-not-found)
- [Missing or Unexpected Response Data](#missing-or-unexpected-response-data)
- [HTML Content Issues](#html-content-issues)
- [Rate Limiting](#rate-limiting)
- [Common Code Mistakes](#common-code-mistakes)
- [General Debugging Tips](#general-debugging-tips)

<br>

## 401 Authentication Errors

### Symptom

- API returns `401 Unauthorized`.
- Requests work in the Eventbrite console but fail in code.

### Possible Causes

- `Authorization` header is missing or formatted incorrectly.
- Token was revoked or regenerated after being copied.
- Token is hard-coded with a typo or extra whitespace.

### How to Fix

1. Confirm your request includes the `Authorization` header in this exact
format — including the `Bearer` prefix and a single space:

```
   Authorization: Bearer YOUR_PRIVATE_TOKEN
```

1. Copy a fresh token from your [API Keys page](https://www.eventbrite.com/account-settings/apps) and replace the existing one.
2. Store your token in an environment variable to avoid hard-coding errors:

```bash
   export EVENTBRITE_TOKEN=your_token_here
```

### Verify

Send this request. A `200 OK` response with your account details confirms
your token is valid.

```bash
curl -H "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  <https://www.eventbriteapi.com/v3/users/me/>
```

### Related Documentation

- [Authentication Guide](../guides/authentication.md)

<br>

## 403 Permission Errors

### Symptom

- API returns `403 NOT_AUTHORIZED`.
- Request is authenticated but access is denied.
- Event exists but cannot be retrieved or modified.

### Possible Causes

- The event belongs to a different Eventbrite account or organization.
- The token does not have permission to perform this operation.
- The event's current status does not allow this action — for example,
modifying a published event.

### How to Fix

1. Confirm the `event_id` belongs to the account that issued your token.
Log in to Eventbrite and verify the event appears under your account.
2. If the request uses an `organization_id`, confirm it matches the
organization that owns the event:

```bash
   curl -H "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
     <https://www.eventbriteapi.com/v3/users/me/organizations/>
```

1. Check the `status` field in the event response. Some operations are
restricted based on event status.

### Verify

Retry the original request. A `200 OK` response confirms the permission
issue is resolved.

### Related Documentation

- [API Reference](../api/api-reference.md)

<br>

## 404 Resource Not Found

### Symptom

- API returns `404 NOT_FOUND`.
- Event ID appears valid but the request fails.

### Possible Causes

- `event_id` is incorrect, malformed, or copied from the wrong URL.
- Event was deleted or unpublished.
- Event belongs to a different account.

### How to Fix

1. Find the correct `event_id` from the event URL in your Eventbrite
account. The ID is the number at the end of the URL:

```
https://www.eventbrite.com/e/my-event-name-12345678901
																					 ^^^^^^^^^^^
																 This is your event_id
```

2. Confirm you are using the correct base URL and API version:

```
https://www.eventbriteapi.com/v3
```

1. Confirm the event is still accessible by opening it in your
Eventbrite account.

### Verify

Run this request with your corrected `event_id`. A `200 OK` response
confirms the event is accessible.

```bash
curl -H "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  <https://www.eventbriteapi.com/v3/events/{event_id}/>
```

### Related Documentation

- [Step-by-Step Tutorial](../getting-started/step-by-step.md)
- [API Reference](../api/api-reference.md)

<br>

## Missing or Unexpected Response Data

### Symptom

- Fields such as `description.html` are empty or `null`.
- Expected fields are missing from the response.
- Draft events return incomplete data.

### Possible Causes

- Event is in `draft` status — some fields are only populated after
the event is published.
- Field is optional and was not filled in by the event organizer.
- Token permissions restrict which fields are returned.

### How to Fix

1. Check the `status` field in the response:

```bash
   curl -H "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
     <https://www.eventbriteapi.com/v3/events/{event_id}/>
```

If `"status": "draft"`, publish the event or use a published event
for testing.

1. Add null checks before accessing nested fields in your code:

```jsx
   const description = event?.description?.text ?? '';
   const capacity = event?.capacity ?? null;
```

### Verify

Re-run the request after publishing the event or switching to a published
test event. All expected fields should now be present.

### Related Documentation

- [Response Handling Guide](../guides/response_handling.md)

<br>

## HTML Content Issues

### Symptom

- HTML tags appear as raw text in the UI.
- HTML output breaks the page layout.
- Security review flags unsanitized HTML rendering.

### Possible Causes

- `html` field is passed to a context that expects plain text.
- HTML content is rendered without sanitization in an untrusted context.

### How to Fix

1. Use `name.text` or `description.text` when plain string content
is required — for example, in notifications or log output:

```jsx
   sendNotification({ title: event.name.text });
```

1. Use `name.html` or `description.html` only when rendering to a
trusted web interface:

```jsx
   container.innerHTML = event.description.html;
```

1. If rendering HTML from an untrusted source, sanitize it first:

```jsx
   import DOMPurify from 'dompurify';
   container.innerHTML = DOMPurify.sanitize(event.description.html);
```

### Verify

Inspect the rendered output in your UI. HTML tags should not appear as
raw text, and the page layout should remain intact.

### Related Documentation

- [Response Handling Guide](../guides/response_handling.md)
- [Code Examples](../examples/code-examples.md)

<br>

## Rate Limiting

### Symptom

- API returns `429 Too Many Requests`.
- Requests succeed individually but fail during high-volume operations.

### Possible Causes

- Hourly limit of 2,000 requests exceeded.
- Daily limit of 48,000 requests exceeded.
- Create or Publish Event endpoint limit of 200 requests/hour exceeded.

### How to Fix

1. Check the rate limit headers in the API response to see how many
requests remain:
    
    
    | Header | Description |
    | --- | --- |
    | `X-Apiary-RateLimit-Limit` | Maximum requests allowed in this window |
    | `X-Apiary-RateLimit-Remaining` | Requests remaining before the limit resets |
2. Add a delay between requests if you are making bulk calls:

```jsx
   async function fetchWithDelay(ids, delayMs = 500) {
     const results = [];
     for (const id of ids) {
       const res = await fetch(
         `https://www.eventbriteapi.com/v3/events/${id}/`,
         { headers: { Authorization: `Bearer ${process.env.EVENTBRITE_TOKEN}` } }
       );
       results.push(await res.json());
       await new Promise(resolve => setTimeout(resolve, delayMs));
     }
     return results;
   }
```

### Verify

Monitor `X-Apiary-RateLimit-Remaining` in subsequent responses. The
value should stay above zero.

### Related Documentation

- [API Reference](../api/api-reference.md)

<br>

## Common Code Mistakes

### Mistake 1 — Token in source code

**Symptom:** Token is exposed in a public repository or build log.

**Fix:** Move the token to an environment variable:

```bash
# Set the environment variable
export EVENTBRITE_TOKEN=your_token_here
```

```jsx
// Read it in code
const token = process.env.EVENTBRITE_TOKEN;
```

**Verify:** Search your codebase for the token string. It should not
appear in any source file.

<br>

### Mistake 2 — Missing Content-Type header

**Symptom:** POST requests return `400 Bad Request` with no clear error.

**Fix:** Include `Content-Type: application/json` in all POST requests:

```bash
curl --request POST \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  --header "Content-Type: application/json" \\
  --data '{ "event": { ... } }' \\
  "<https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/>"
```

**Verify:** Retry the request. A `200 OK` response confirms the header
was the issue.

<br>

### Mistake 3 — Wrong base URL

**Symptom:** All requests return connection errors or `404`.

**Fix:** Use `eventbriteapi.com`, not `eventbrite.com`:

```
✗  https://www.eventbrite.com/v3/events/
✓  https://www.eventbriteapi.com/v3/events/
```

**Verify:** Retry with the corrected URL.

<br>

## General Debugging Tips

Follow these steps in order when a request fails and the cause is unclear.

1. **Test in the Eventbrite console first.** If the request works in the
console but not in code, the issue is in your headers or token handling.
2. **Import the request into Postman.** Use the console's
**Show Code Example** → **cURL** output. Postman shows you exactly which
headers are being sent.
3. **Log the full response during development** — including status code,
headers, and body. Error descriptions are in the `error_description`
field of the response body.
4. **Compare against a working request.** Copy a working cURL example
from the [API Reference](../api/api-reference.md) and diff it against
your failing request line by line.

<br>

## Next Steps

- [Authentication Guide]()
- [API Reference]()
- [Step-by-Step Tutorial]()
- [Response Handling Guide]()
- [Code Examples]()
- [SDKs]()

<br>

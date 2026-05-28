# Code Examples

This document provides practical code examples for interacting with the Eventbrite API. The examples fucus on illustrating common request patterns, authentication, and basic response handling. They are designed to complement the API Reference and tutorials, not to serve as complete application implementations.

### Table of Contents

- [Authentication](#authentication)
- [cURL](#curl)
- [JavaScript](#javascript)
- [Response Handling](#response-handling)

<br>

## Authentication

All examples assume the use of a private token issued by Eventbrite.

**Authorization Header**
```bash
Authorization: Bearer YOUR_OAUTH_TOKEN
```
- Replace `YOUR_OAUTH_TOKEN` with your own token.
- For details on obtaining tokens, see the [Authentication Guide](../guides/authentication.md).

<br>

## cURL

The cURL example demonstrates the raw HTTP request structure.

**Retrieve an Event**

```bash
curl --request GET \
     --header "Authorization: **************" \
     --header "Content-Type: application/json" \
  'https://www.eventbriteapi.com/v3/events/12345/'
```

<br>

## JavaScript

It uses the Fetch API that is commonly supported in modern browsers. 

**Fetch API**

```jsx
fetch("https://www.eventbriteapi.com/v3/events/12345/", {
  method: "GET",
  headers: {
    "Authorization": "************",
    "Content-Type": "application/json"
  }
})
  .then(response => {
    if (!response.ok) {
      throw new Error(`Request failed with status ${response.status}`);
    }
    return response.json();
  })
  .then(event => {
    console.log(event.name.text);
    console.log(event.description.html);
  })
  .catch(error => {
    console.error(error);
  });
```

<br>

## Node.js

It illustrates how a backend service might retrieve event data.

**Node.js Server-side**

```cpp
import fetch from "node-fetch";

async function getEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      headers: {
        Authorization: "************",
        "Content-Type": "application/json"
      }
    }
  );

  if (!response.ok) {
    throw new Error(`Failed to fetch event: ${response.status}`);
  }

  const event = await response.json();

  return {
    title: event.name?.text,
    description: event.description?.html,
    status: event.status
  };
}
```

<br>

## Response Handling

- Extract only the fields required for your use cases.
- Validate responses and handle errors defensively.
- Treat HTML fields such as `description.html` separately from plain text.

For detailed guidance, see the [Response Handling Guide](../guides/response_handling.md).

<br>

## Next steps

- [API Reference](../api/api-reference.md)
- [Step-by-step Tutorial](../getting-started/step-by-step.md)
- [Authentication Guide](../guides/authentication.md)
- [SDKs](../sdks/sdks.md)

<br>

---
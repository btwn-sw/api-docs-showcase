# Quick Start Guide

---

This guide helps you make your first successful request to the Eventbrite API. It will guide you to prepare the required prerequisites, authenticate using a private token, make your first API request, and verify the response using common tools. It designs to get you from zero to your first API response as quickly as possible.

### Table of Contents

- Prerequisites
- Step 1: Get Your Authentication Token
- Step 2: Choose an Endpoint
- Step 3: Make Your First API Request
- Step 4: Verify the Response
- Step 5: Develop Your Integration
- Step 6: Prepare for Deployment

---

## Prerequisites

- An active Eventbrite account
- A valid private token
- A basic understanding of HTTP requests
- One of the following tools:
    - Web browser (Eventbrite API console)
    - Postman
    - Command-line tool (for cURL)

For detailed token setup, see the Authentication Guide.

---

## Step 1: Get Your Authentication Token

All Eventbrite API requests require authentication.

- Get your **Private Token**, which is suitable for testing and server-side integrations.
- If you have not had a token, follow the steps in the Authentication Guide to create one.

---

## Step 2: Choose an Endpoint

You can find various API Endpoints from the Eventbrite [API Reference](https://www.eventbrite.com/platform/api) under the **Reference** section. For the first request, you will retrieve details for an existing event.

### Event Endpoint

**Retrieve an Event**

The endpoint returns an Event object containing basic event information.

```bash
**GET** /events/{event_id}
```

---

## Step 3: Make Your First API Request

Make your first request using any of the following methods.

### Eventbrite API Console

Use an Eventbrite API console. 

1. Open the Eventbrite [API Reference](https://www.eventbrite.com/platform/api).
2. Navigate to **Reference > Event > Retrieve an Event**.
3. Click **Try** to enable the interactive console.
4. Enter a valid `event_id` in URI Parameters.
5. Add the Authorization header:
    
    ```bash
    Authorization: Bearer PERSONAL_OAUTH_TOKEN
    ```
    
6. Click the `Call Resource` button.

### cURL

1. Click **Show Code Example** in the Eventbrite API Console.
2. Replace `{event_id}` with an actual event ID.
    
    ```bash
    curl --request GET \
      --header "Authorization: Bearer PERSONAL_OAUTH_TOKEN" \
      https://www.eventbriteapi.com/v3/events/{event_id}/
    ```
    

---

## Step 4: Verify the Response

### `200 OK`

- If the request is successful, you should receive a `200 OK` response.
- The response body will contain a JSON object with event details such as :
    - Event name
    - Description
    - Start and end time
    - Status

---

## Step 5: Develop Your Integration

Now, you can begin integrating the API into your application.

### Development Steps

1. Extract only the fields required for your use case.
2. Handle optional and nullable fields defensively.
3. Separate HTML content from plain text.

For practical examples, see the Code Examples.

---

## Step 6: Prepare for Deployment

### Best Practices

Before deploying your integration, review the following best practices.

- Store tokens securely using environment variables.
- Use separate tokens for development and production environments.
- Monitor rate limits to avoid request failures.

### Related Documents

- SDKs
- Troubleshooting

---

## Next steps

- Step-by-Step Tutorial
- API Reference
- Response Handling Guide
- Troubleshooting
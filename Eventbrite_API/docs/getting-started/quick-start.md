# Quick Start Guide

<br>

This guide helps you make your first successful request to the Eventbrite API. It will guide you to prepare the required prerequisites, authenticate using a private token, make your first API request, and verify the response using common tools. It designs to get you from zero to your first API response as quickly as possible.

### Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Get Your Authentication Token](#step-1-get-your-authentication-token)
- [Step 2: Choose an Endpoint](#step-2-choose-an-endpoint)
- [Step 3: Make Your First API Request](#step-3-make-your-first-api-request)
- [Step 4: Verify the Response](#step-4-verify-the-response)
- [Step 5: Develop Your Integration](#step-5-develop-your-integration)
- [Step 6: Prepare for Deployment](#step-6-prepare-for-deployment)

<br>

## Prerequisites

- An active Eventbrite account
- A valid private token
- A basic understanding of HTTP requests
- One of the following tools:
    - Web browser (Eventbrite API console)
    - Postman
    - Command-line tool (for cURL)

For detailed token setup, see the [Authentication Guide](../guides/authentication.md).

<br>

## Step 1: Get Your Authentication Token

All Eventbrite API requests require authentication.

- Get your **Private Token**, which is suitable for testing and server-side integrations.
- If you have not had a token, follow the steps in the [Authentication Guide](../guides/authentication.md) to create one.

<br>

## Step 2: Choose an Endpoint

You can find various API Endpoints from the Eventbrite [API Reference](https://www.eventbrite.com/platform/api) under the **Reference** section. For the first request, you will retrieve details for an existing event.

### Event Endpoint

**Retrieve an Event**

The endpoint returns an Event object containing basic event information.

```bash
**GET** /events/{event_id}
```

<br>

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
    

<br>

## Step 4: Verify the Response

### `200 OK`

- If the request is successful, you should receive a `200 OK` response.
- The response body will contain a JSON object with event details such as :
    - Event name
    - Description
    - Start and end time
    - Status

<br>

## Step 5: Develop Your Integration

Now, you can begin integrating the API into your application.

### Development Steps

1. Extract only the fields required for your use case.
2. Handle optional and nullable fields defensively.
3. Separate HTML content from plain text.

For practical examples, see the [Code Examples](../examples/code-examples.md).

<br>

## Step 6: Prepare for Deployment

### Best Practices

Before deploying your integration, review the following best practices.

- Store tokens securely using environment variables.
- Use separate tokens for development and production environments.
- Monitor rate limits to avoid request failures.

### Related Documents

- [SDKs](../sdks/sdks.md)
- [Troubleshooting](../guides/troubleshooting.md)

<br>

## Next steps

- [Step-by-Step Tutorial](../getting-started/step-by-step.md)
- [API Reference](../api/api-reference.md)
- [Response Handling Guide](../guides/response_handling.md)
- [Troubleshooting](../guides/troubleshooting.md)

<br>

---
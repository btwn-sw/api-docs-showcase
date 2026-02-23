# Authentication Guide

This guide describes how to authenticate requests to the Eventbrite API, using OAuth 2.0. All Eventbrite API endpoints require authentication. This guide focuses on token types used by Eventbrite, how to obtain tokens, and how to include tokens in API requests.

### Table of Contents

- [OAuth 2.0](#oauth-20)
- [Eventbrite API Keys and Token](#eventbrite-api-keys-and-token)
- [Authentication Methods](#authentication-methods)
- [Environment Configuration](#environment-configuration)
- [Best Practices](#best-practices)

<br>

## OAuth 2.0

Eventbrite uses OAuth 2.0 for token-based authorization. OAuth tokens are used to authenticate API requests and determine what actions a client is allowed to perform. 

### Private Token

Eventbrite uses multiple token concepts that are often confused but Private (OAuth) Token will be only used for further authorization. 

- Issued from the Eventbrite Developer Dashboard.
- Commonly used for testing and server-side integrations.
- Long-lived token.

***Note**: This documentation primarily uses **Private Token** for simplicity.*

<br>

## Eventbrite API Keys and Token

To use the Eventbrite API, you must first create an application. If you have not yet registered for Eventbrite, create your account [here](https://www.eventbrite.com/signin/). 

### Get a Token

1. Log in to [Eventbrite](https://www.eventbrite.com/signin/).
2. Go to **Account settings** under your profile.
3. Navigate to **Developer Links > API Keys**.
4. Click **Create API Key**.
5. Fill in the required information and create the app.

``![image.png](attachment:77c6513b-91cf-42a5-8358-d349a170b87c:image.png)

6. Click the `Create Key` button. 
7. Copy your **Private token**.

### Existing API Keys

**App Existed**

- If you have already created a API key, visit your [API Key Management page](https://www.eventbrite.com/account-settings/apps). The page lists existed apps. In the following example, it shows an existed app, *Practice*. Click ‘Show API key, client secret and tokens' to see the details.
``image``

**No App Existed**

- In such a case that you cannot find the app listed on the page, click `Create API Key` to create a new app and generate a new API key and a token.

<br>

## Authentication Methods

### Authentication Endpoints

Authentication is required for all Eventbrite API endpoints. To call API requests, you need a private token.

### 2-Way to Authenticate API Request

**Header Parameter (Recommended)**

- Replace `Bearer PERSONAL_OAUTH_TOKEN` with your private token in the `Authorization` header.

```bash
{Authorization: Bearer PERSONAL_OAUTH_TOKEN}
```

**Query Parameter**

- Replace `PERSONAL_OAUTH_TOKEN` with your private token at the end of the URL. This method is supported but not recommended for production use.

```bash
/v3/users/me/?token=PERSONAL_OAUTH_TOKEN
```

<br>

## Environment Configuration

### Server-Side Authorization (Recommended)

**Method 1. Redirect User**

Redirect users to the Eventbrite authorization URL, including the following information:

- API key

```bash
https://www.eventbrite.com/oauth/authorize?response_type=code&client_id=YOUR_API_KEY&redirect_uri=YOUR_REDIRECT_URI
```

- Redirect URI as query parameters

```bash
http://localhost:8080/oauth/redirect?code=YOUR_ACCESS_CODE
```

You can find your API key and adjust OAuth Redirect URI details in **Account Settings > Key Info**.

**Method 2. POST Request**

Send a POST request to `http://www.eventbrite.com/oauth/token`, specifying the following information:

- Access code
- Client secret
- API key

```bash
curl --request POST --url 'https://www.eventbrite.com/oauth/token' --header 'content-type: application/x-www-form-urlencoded' --data grant_type=authorization_code --data 'client_id=API_KEY --data client_secret=CLIENT_SECRET --data code=ACCESS_CODE --data 'redirect_uri=REDIRECT_URI'
```

### Client-Side Authorization

Redirect users to the Eventbrite authorization URL, including the following information:

- API key
- Redirect URI as query parameters

```bash
https://www.eventbrite.com/oauth/authorize?response_type=token&client_id=YOUR_API_KEY&redirect_uri=YOUR_REDIRECT_URI
```

<br>

## Best Practices

### Environment Variables

- Use separate tokens for development and production.

### Token Security

- Never expose tokens in public client-side code.
- Store tokens in environment variables.
- Do not use API keys in your client-side code before making them publicly available.
- Monitor API key usage and delete API keys and any private tokens that are no longer needed.

<br>

## Next steps

- [API Reference](../api/api-reference.md)
- [Code Examples](../examples/code-examples.md)
- [SDKs](../sdks/sdks.md)

<br>

---
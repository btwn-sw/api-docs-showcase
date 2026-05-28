# Eventbrite API SDKs

Eventbrite does not provide official SDKs. You can integrate with the
Eventbrite API directly using standard HTTP clients — no SDK required.
This guide explains what is available, what to avoid, and how to get
started with direct integration.

<br>

## Table of Contents

- [SDK Availability](#sdk-availability)
- [Direct HTTP Integration](#direct-http-integration)
- [Client Code Generation](#client-code-generation)

<br>

## SDK Availability

Eventbrite does not maintain official SDKs for any programming language.
The API is a standard RESTful HTTP interface, and Eventbrite expects
developers to handle authentication, requests, and responses directly.

### Community SDKs

Some community-maintained libraries wrap the Eventbrite API, but
Eventbrite does not support them. Before using a community SDK in
production, consider the following:

- Coverage may be incomplete — not all endpoints or features are
guaranteed to be supported.
- Maintenance may lag behind API changes, which can cause silent
failures after an API update.
- Debugging is harder when an abstraction layer sits between your
code and the API response.

For production integrations, direct HTTP is the safer and more
predictable choice.

<br>

## Direct HTTP Integration

Direct HTTP integration is the recommended approach. It gives you full
control over request construction, error handling, and response parsing
without any external dependency.

### Recommended tools

| Context | Tool |
| --- | --- |
| Browser-based JavaScript | `fetch` (built-in) |
| Node.js / server-side JavaScript | `node-fetch` or `axios` |
| Testing and exploration | Postman |
| Other backend languages | Standard HTTP library for your language |

For working request examples in cURL, JavaScript, and Node.js, see
[Code Examples]().

<br>

## Client Code Generation

If an OpenAPI specification is available, you can use code generation
tools to produce a typed HTTP client for your language. Generated clients
reduce boilerplate but still require validation and customization before
use in production.

The Eventbrite API OpenAPI specification is available at:
[openapi.yaml]()

<br>

## Next Steps

- [Authentication Guide]()
- [Code Examples]()
- [Response Handling Guide]()

<br>

# Eventbrite API SDKs

---

Eventbrite does not officially provide maintained SDKs for specific programming languages. Instead, the Eventbrite API is designed to be consumed directly using standard HTTP clients. This guide explains how to integrate with the Eventbrite API in the absence of official SDKs, and outlines recommended alternatives.

### Table of Contents

- SDK Availability
- Recommended Integration

---

## SDK Availability

### Official SDK

- Eventbrite does not offer officially supported SDKs for languages such as JavaScript, Python, or Java.
- The API is exposed as a RESTful HTTP interface.
- Developers are expected to manage authentication, requests, and responses directly.

### Community and Third-Party SDKs

- Some community-maintained wrappers or libraries offer the Eventbrite API, but Eventbrite does not officially support them.
- They may not cover all endpoints or features.
- They may lag behind API changes or updates.
- Users need to be careful when adopting third-party SDKs in production environments.

---

## Recommended Integration

In the absence of official SDKs, direct HTTP integration is the recommended approach.

### Standard HTTP Clients

- Use `fetch` or `axios` in JavaScript-based applications.
- Use server-side HTTP libraries in backend services.
- Use API tools such as Postman for testing and exploration.

### Key Features

- Common model for APIs that prioritize flexibility and broad compatibility.
- Full control over request and response handling.
- No dependency on external SDK maintenance.
- Easier debugging and customization.

### Client Code Generation

- Users may choose to generate client code, using automated tools if an OpenAPI specification is available.
- Users generate client code using automated tools.
- Generated clients help reduce boilerplate code, but still require validation and customization.

---

## Next steps

- Authentication Guide
- Code Examples
- Response Handling Guide
# Troubleshooting Guide

---

This troubleshooting guide helps you diagnose and resolve common issues encountered when working with the Eventbrite API. It focuses on real-world problems related to authentication, permissions, response data, and rate limiting. 

### Table of Contents

- Authentication Issues
- Authorization and Permission Errors
- Resource Not Found Errors
- Unexpected or Missing Response Data
- Handling HTML Content Issues
- Rate Limiting Issues
- Common Code Mistakes
- General Debugging Tips

---

## Authentication Issues

### Symptom

- API requests return `401 Unauthorized`.
- Requests work in the Eventbrite console but fail in code.
- Authentication header is ignored.

### Possible Causes

- Missing or invalid private token.
- `Authorization` header is missing the `Bearer` prefix.
- Token was revoked or regenerated.
- Token is hard-coded incorrectly in the application.

### How to Fix

- Verify that a valid private token is being used.
- Ensure the header format is correct:
    
    ```bash
    Authorization: Bearer PERSONAL_OAUTH_TOKEN
    ```
    
- Regenerate the token if necessary.
- Store tokens securely using environment variables.

### Related Documentation

- Authentication Guide
- API Reference

---

## Authorization and Permission Errors

### Symptom

- API returns `403 NOT_AUTHORIZED`.
- Request is authenticated but access is denied.
- Event exists but cannot be retrieved or modified.

### Possible Causes

- Token does not have permission to access the resource.
- Event does not belong to the authenticated user or organization.
- Attempting to modify a published or restricted event.

### How to Fix

- Confirm the event belongs to the authenticated account.
- Verify the correct `organization_id` is used.
- Check whether the operation is allowed for the event’s current status.

### Related Documentation

- API Concepts
- API Reference

---

## Resource Not Found Errors

### Symptom

- API returns `404 NOT_FOUND`.
- Event ID appears valid but request fails.

### Possible Causes

- Incorrect or malformed `event_id`.
- Event was deleted or unpublished.
- Event belongs to another account.
- URL copied from the wrong Eventbrite page.

### How to Fix

- Verify the `event_id` directly from the Eventbrite event URL.
- Confirm the event exists and is accessible.
- Ensure the correct API base URL and version are used:
    
    ```arduino
    https://www.eventbriteapi.com/v3
    ```
    

### Related Documentation

- API Concepts
- Step-by-Step Tutorial

---

## Unexpected or Missing Response Data

### Symptom

- Fields such as `description.html` are empty or `null`.
- Expected fields are missing from the response.
- Draft events return incomplete data.

### Possible Causes

- Event is in `draft` state.
- Field is optional or conditionally included.
- Token permissions limit available fields.

### How to Fix

- Check `status` field of the event.
- Implement defensive checks before accessing nested fields.
- Extract only the fields required for your use case.

### Related Documentation

- Response Handling Guide
- API Concept

---

## Handling HTML Content Issues

### Symptom

- HTML content is rendered as plain text.
- HTML output breaks UI layout.
- Sanitization concerns when rendering content.

### Possible Causes

- HTML fields treated as plain strings.
- Rendering HTML in an untrusted context.
- Missing sanitization logic in client application.

### How to Fix

- Use `description.html` only in trusted rendering contexts.
- Treat HTML fields separately from text fields.
- Apply sanitization if required by your security model.

### Related Documentation

- Response Handling Guide
- Code Examples

---

## Rate Limiting Issues

### Symptom

- Exceeded hourly or daily rate limits
- Burst requests hitting endpoint-level limits.

### How to Fix

- Inspect rate limit headers in API responses:
    
    ```bash
    X-Apiary-RateLimit-Limit
    X-Apiary-RateLimit-Remaining
    ```
    
- Reduce request frequency.
- Implement request batching or backoff strategies.

### Related Documentation

- API Reference

---

## Common Code Mistakes

### Symptom

- Requests fail unexpectedly.
- Security risks detected during review.
- Inconsistent behaviour between environments.

### Possible Causes

- Tokens hard-coded in source code.
- Deprecated libraries used for HTTP requests.
- Missing headers or incorrect content type.

### How to Fix

- Store tokens in environment variables.
- Use modern HTTP clients (`fetch`, server-side equivalents).
- Validate headers before sending requests.

### Related Documentation

- Code Examples
- SDKs

---

## General Debugging Tips

- Test requests in the Eventbrite console before implementing in code.
- Use Postman to inspect headers and response payloads.
- Log full responses during development.
- Compare failing requests with working examples.

---

## Next steps

- Authentication Guide
- API Reference
- Step-by-Step Tutorial
- Response Handling Guide
- Code Examples
- SDKs
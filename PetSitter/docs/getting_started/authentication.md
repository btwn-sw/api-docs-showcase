# PetSitter Authentication Setup Guide

This guide describes how to set up and use PetSitter authentication system with Bearer Authorization.

**Note: PetSitter API Documentation has independently purposed.** The PetSitter API Documentation thoroughly reflects the content of its original source files in [Designing APIs with Swagger and OpenAPI](https://github.com/designapis/petsitter/tree/main). Each document serves an independent purpose and may not be perfectly aligned or closely coupled with one another. This PetSitter authentication setup guide serves implementation and handlings based on the provided [auth.js](https://github.com/designapis/petsitter/blob/main/backend/utils/auth.js) file. 

## Table of Contents

---

- Overview
- Environment Configuration
- Basic Auth Setup
- API Endpoints
- Security Best Practices
- Example Usage

## Overview

---

PetSitter supports an authentication method:

- Basic Auth: `username:password` login which is `Base64-encoded` with `Bearer` token.
- `Base64(userId:password)`

## Environment Configuration

---

### Authorization Header

```jsx
Authorization: Bearer <token>
```

### Token Format

```jsx
Token: base64(userId:password)
```

### Basic Auth Configuration

Set up the following environment variable for authorization header:

```jsx
const authHeader = req.headers['authorization'].split(" ");
const credentials = base64.decode(authHeader[1]).split(":");
```

## Basic Auth Setup

---

### PetSitter Authentication Setup

1. The header splits by space and requires 2 parts.
    - Part 1: `"Bearer"`
    - Part 2: `token`(string)
2. It `Base64-decodes` the token and splits the decoded string by `:` into 2 parts:
    - `credentials[0]` = user id (used in `UserModel.findById`)
    - `credentials[1]` = password
3. Matching the user and password sets `req.user = user` and call `next()`.

## API Endpoints

---

### Authentication Endpoints

```bash
# Login
POST /sessions
{
  "userId": "string",
  "password": "string"
}

# Register
POST /users
{
  "password": "password",
  "full_name": "full_name",
  "updated_at": "2025-11-23T04:56:07.000+00:00",
  "roles": [
    "PetOwner",
    "PetOwner"
  ],
  "created_at": "2025-11-23T04:56:07.000+00:00",
  "id": "id",
  "email": "user@example.com"
}
```

## Security Best Practices

---

### Environment Variables

- Never commit token to version control
- Use strong, random token for Basic Auth
- Rotate token regularly
- Use different tokens for different environments

### Base64

- Use real encryption in transit
- Require TLS (HTTPS) always
- Use opaque tokens or signed tokens

### Bearer Tokens

- Use short expiration times for access tokens
- Validate token signatures

## Error

---

### Response Format

```jsx
{
		"code": "400"
    "message": "Invalid Authorization token."
}
```

### Error Handling

**`Authorization` Header Error**

- Issue: Missing space, wrong prefix, invalid token fomat
- Solution:

**`Authorization` Header Format Error**

```jsx
{
		"code": "400"
    "message": "Invalid Authorization token."
}
```

**Invalid Credentials**

```jsx
{
		"code": "401"
    "message": "Malformed Authorization token."
}
```

## Example Usage

---

## Troubleshooting

---

### Common Issues

- **Invalid Token**: Ensure it must match Base64(`userId` : `password`)
- Expired
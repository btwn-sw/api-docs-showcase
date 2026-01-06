# PetSitter API Reference

This API (Application Programming Interface) reference provides API specifications and API integration instructions for the PetSitter API as described in the book ‘*Designing APIs with Swagger and OpenAPI’.*

**Note: PetSitter API Documentation has independently purposed.** The PetSitter API Documentation thoroughly reflects the content of its original source files in [Designing APIs with Swagger and OpenAPI](https://github.com/designapis/petsitter/tree/main). Each document serves an independent purpose and may not be perfectly aligned or closely coupled with one another. This PetSitter API reference serves implementation and handlings based on the provided [openapi.yaml](https://github.com/designapis/petsitter/blob/main/backend/api/openapi.yaml) file. 

## Table of Contents

---

- Overview
- Authentication
- Response Format
- Error Handling
- Rate Limiting
- REST API Endpoints
    - Users POST /users
    - Users GET /users/{id}
    - Users PUT /users/{id}
    - Users DELETE /users/{id}
    - Users GET /users/{id}/jobs
    - Users GET /users/{id}/job-applications
    - Users POST /sessions
    - Jobs GET /users/{id}/jobs
    - Jobs GET /users/{id}/job-applications
    - Jobs GET /jobs
    - Jobs POST /jobs
    - Jobs GET /jobs/{id}
    - Jobs PUT /jobs/{id}
    - Jobs DELETE /jobs/{id}
    - Jobs GET /jobs/{id}/job-applications
    - Jobs POST /jobs/{id}/job-applications
    - Jobs PUT /job-applications/{id}
    - Jobs DELETE /job-applications/{id}
- Examples
    - Complete Authentication Flow
    - PetSitter Job Workflow

## Overview

---

The PetSitter API provides programmatic access to all fundamental functionality, covering mostly enabled components and hallmarks of OpenAPI. It also includes cases from naive to complex scaffolds and features.

### Base URL

Development: 

```bash
mongodb://petsitter-db/petsitter_db
```

Production: 

```bash
https://petsitter.designapis.com
```

Demo:

```bash
 https://api.exmple.com
```

### API Version

- Pet sitter service: `v1.3.0`

### Content Types

- Request: `application/json`
- Response: `application/json`

## Authentication

---

PetSitter uses an API key generated from a session token based on `openapi.yaml` file.

### API Key Authentication

Include the API key in the API key header:

```jsx
X-API-Key: SessionToken <your_API_Key>
```

### Authentication Endpoints

**Register User Account**

Endpoint:

```jsx
POST /users
```

Request Body:

```jsx
{
  "password": "",
  "full_name": "full_name",
  "updated_at": "2000-01-23T04:56:07.000+00:00",
  "roles": [
    "PetOwner",
    "PetOwner"
  ],
  "created_at": "2000-01-23T04:56:07.000+00:00",
  "id": "id",
  "email": ""
}
```

**Authentication Header Example** 

Endpoint:

```jsx
GET /users/{id}
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `User` schema |

Response:

```json
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

**Start Session (Login)**

Endpoint:

```jsx
POST /sessions
```

Request Body:

```jsx
{
  "email": "string",
  "password": "string"
}
```

Response”

```jsx
{
  "user_id": "string",
  "auth_header": "string"
}
```

## Response Format

---

Every API endpoint has discrete response formats in terms of relative schema and property.

### Success Response

```jsx
{
  "user_id": "user_id",
  "job_id": "job_id",
  "id": "id",
  "status": "APPLYING"
}
```

```jsx
[
  {
    "user_id": "user_id",
    "job_id": "job_id",
    "id": "id",
    "status": "APPLYING"
  }
]
```

### Error Response

```jsx
{
		"code": "ERROR_CODE"
    "message": "Human readable error message"
}
```

## Error Handling

---

### HTTP Status Code

| Code | Description |
| --- | --- |
| 200 | OK - Request successful |
| 201 | Created - Resource created successfully |
| 204 | No Content - Resource deleted successfully |
| 403 | Forbidden |
| 404 | Not Found |
| 500 | Internal Server Error - Server error |

### Error Codes

| Code | Description |
| --- | --- |
| 500 | Internal server error |

## Rate Limiting

---

Rate limiting for API requests is not currently available.

## REST API Endpoints

---

### Users

**Register User Account**

```jsx
POST /users
```

Request Body:

```json
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

**Retrieve User Information**

```jsx
GET /users/{id}
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `User` schema |

Response:

```json
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

**Update User Information**

```jsx
PUT /users/{id}
```

Request Body:

```json
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

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `User` schema |

**Delete User Information**

```jsx
DELETE /users/{id}
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `User` schema |

**Retrieve List of Jobs for User**

```jsx
GET /users/{id}/jobs
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `User` schema |

Response:

```json
[
  {
    "creator_user_id": "creator_user_id",
    "starts_at": "2025-11-23T04:56:07.000+00:00",
    "updated_at": "2025-11-23T04:56:07.000+00:00",
    "worker_user_id": "worker_user_id",
    "activities": [
      "walk",
      "walk"
    ],
    "created_at": "2025-11-23T04:56:07.000+00:00",
    "description": "description",
    "id": "id",
    "ends_at": "2025-11-23T04:56:07.000+00:00",
    "dog": {
      "size": "small",
      "name": "name",
      "years_old": 0,
      "breed": "breed"
    },
    "applications": [
      {
        "user_id": "user_id",
        "job_id": "job_id",
        "id": "id",
        "status": "APPLYING"
      },
      {
        "user_id": "user_id",
        "job_id": "job_id",
        "id": "id",
        "status": "APPLYING"
      }
    ]
  }
]
```

**Retrieve List of Job Applications for User**

```jsx
GET /users/{id}/job-applications
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `User` schema |

Response:

```json
[
  {
    "user_id": "user_id",
    "job_id": "job_id",
    "id": "id",
    "status": "APPLYING"
  }
]
```

**Start Session (Login)**

```jsx
POST /sessions
```

Response:

```json
{
  "email": "string",
  "password": "string"
}
```

### Jobs

**Retrieve List of Jobs for User**

```jsx
GET /users/{id}/jobs
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `User` schema |

Response:

```json
[
  {
    "creator_user_id": "creator_user_id",
    "starts_at": "2025-11-23T04:56:07.000+00:00",
    "updated_at": "2025-11-23T04:56:07.000+00:00",
    "worker_user_id": "worker_user_id",
    "activities": [
      "walk",
      "walk"
    ],
    "created_at": "2025-11-23T04:56:07.000+00:00",
    "description": "description",
    "id": "id",
    "ends_at": "2025-11-23T04:56:07.000+00:00",
    "dog": {
      "size": "small",
      "name": "name",
      "years_old": 0,
      "breed": "breed"
    },
    "applications": [
      {
        "user_id": "user_id",
        "job_id": "job_id",
        "id": "id",
        "status": "APPLYING"
      },
      {
        "user_id": "user_id",
        "job_id": "job_id",
        "id": "id",
        "status": "APPLYING"
      }
    ]
  }
]
```

**Retrieve List of Job Applications for User**

```jsx
GET /users/{id}/job-applications
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `User` schema |

Response:

```json
[
  {
    "user_id": "user_id",
    "job_id": "job_id",
    "id": "id",
    "status": "APPLYING"
  }
]
```

**Retrieve List of Available Jobs**

```jsx
GET /jobs
```

Query Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `limit` | optional | integer | Limits the number of results the endpoint returns.
`default: 20`, `max: 50`, `min: 0` |
| `offset` | optional | integer | Skips items from the response.
`default: 0`  |

Response:

```json
{
  "has_more": true,
  "items": [
    {
      "creator_user_id": "creator_user_id",
      "starts_at": "2025-11-23T04:56:07.000+00:00",
      "updated_at": "2025-11-23T04:56:07.000+00:00",
      "worker_user_id": "worker_user_id",
      "activities": [
        "walk",
        "walk"
      ],
      "created_at": "2025-11-23T04:56:07.000+00:00",
      "description": "description",
      "id": "id",
      "ends_at": "2025-11-23T04:56:07.000+00:00",
      "dog": {
        "size": "small",
        "name": "name",
        "years_old": 0,
        "breed": "breed"
      },
      "applications": [
        {
          "user_id": "user_id",
          "job_id": "job_id",
          "id": "id",
          "status": "APPLYING"
        },
        {
          "user_id": "user_id",
          "job_id": "job_id",
          "id": "id",
          "status": "APPLYING"
        }
      ]
    },
    {
      "creator_user_id": "creator_user_id",
      "starts_at": "2025-11-23T04:56:07.000+00:00",
      "updated_at": "2025-11-23T04:56:07.000+00:00",
      "worker_user_id": "worker_user_id",
      "activities": [
        "walk",
        "walk"
      ],
      "created_at": "2025-11-23T04:56:07.000+00:00",
      "description": "description",
      "id": "id",
      "ends_at": "2025-11-23T04:56:07.000+00:00",
      "dog": {
        "size": "small",
        "name": "name",
        "years_old": 0,
        "breed": "breed"
      },
      "applications": [
        {
          "user_id": "user_id",
          "job_id": "job_id",
          "id": "id",
          "status": "APPLYING"
        },
        {
          "user_id": "user_id",
          "job_id": "job_id",
          "id": "id",
          "status": "APPLYING"
        }
      ]
    }
  ],
  "total_items": 0
}
```

**Create a New Job**

```jsx
POST /jobs
```

Request Body:

```json
{
  "creator_user_id": "creator_user_id",
  "starts_at": "2025-11-23T04:56:07.000+00:00",
  "updated_at": "2025-11-23T04:56:07.000+00:00",
  "worker_user_id": "worker_user_id",
  "activities": [
    "walk",
    "walk"
  ],
  "created_at": "2025-11-23T04:56:07.000+00:00",
  "description": "description",
  "id": "id",
  "ends_at": "2025-11-23T04:56:07.000+00:00",
  "dog": {
    "size": "small",
    "name": "name",
    "years_old": 0,
    "breed": "breed"
  },
  "applications": [
    {
      "user_id": "user_id",
      "job_id": "job_id",
      "id": "id",
      "status": "APPLYING"
    },
    {
      "user_id": "user_id",
      "job_id": "job_id",
      "id": "id",
      "status": "APPLYING"
    }
  ]
}
```

Response:

```json
{
  "creator_user_id": "creator_user_id",
  "starts_at": "2025-11-23T04:56:07.000+00:00",
  "updated_at": "2025-11-23T04:56:07.000+00:00",
  "worker_user_id": "worker_user_id",
  "activities": [
    "walk",
    "walk"
  ],
  "created_at": "2025-11-23T04:56:07.000+00:00",
  "description": "description",
  "id": "id",
  "ends_at": "2025-11-23T04:56:07.000+00:00",
  "dog": {
    "size": "small",
    "name": "name",
    "years_old": 0,
    "breed": "breed"
  },
  "applications": [
    {
      "user_id": "user_id",
      "job_id": "job_id",
      "id": "id",
      "status": "APPLYING"
    },
    {
      "user_id": "user_id",
      "job_id": "job_id",
      "id": "id",
      "status": "APPLYING"
    }
  ]
}
```

**Retrieve Job Details**

```jsx
GET /jobs/{id}
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `Job` schema |

Response:

```json
{
  "creator_user_id": "creator_user_id",
  "starts_at": "2025-11-23T04:56:07.000+00:00",
  "updated_at": "2025-11-23T04:56:07.000+00:00",
  "worker_user_id": "worker_user_id",
  "activities": [
    "walk",
    "walk"
  ],
  "created_at": "2025-11-23T04:56:07.000+00:00",
  "description": "description",
  "id": "id",
  "ends_at": "2025-11-23T04:56:07.000+00:00",
  "dog": {
    "size": "small",
    "name": "name",
    "years_old": 0,
    "breed": "breed"
  },
  "applications": [
    {
      "user_id": "user_id",
      "job_id": "job_id",
      "id": "id",
      "status": "APPLYING"
    },
    {
      "user_id": "user_id",
      "job_id": "job_id",
      "id": "id",
      "status": "APPLYING"
    }
  ]
}
```

**Update Job Details**

```jsx
PUT /jobs/{id}
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `Job` schema |

Request Body:

```json
{
  "creator_user_id": "creator_user_id",
  "starts_at": "2025-11-23T04:56:07.000+00:00",
  "updated_at": "2025-11-23T04:56:07.000+00:00",
  "worker_user_id": "worker_user_id",
  "activities": [
    "walk",
    "walk"
  ],
  "created_at": "2025-11-23T04:56:07.000+00:00",
  "description": "description",
  "id": "id",
  "ends_at": "2025-11-23T04:56:07.000+00:00",
  "dog": {
    "size": "small",
    "name": "name",
    "years_old": 0,
    "breed": "breed"
  }
```

**Delete Job** 

```jsx
DELETE /jobs/{id}
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `Job` schema |

**Retrieve All Applications for a Job**

```jsx
GET /jobs/{id}/job-applications
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `Job` schema |

Response:

```json
[
  {
    "user_id": "user_id",
    "job_id": "job_id",
    "id": "id",
    "status": "APPLYING"
  }
]
```

**Create a Job Application**

```jsx
POST /jobs/{id}/job-applications
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `Job` schema |

Request Body:

```json
{
  "user_id": "user_id",
  "job_id": "job_id",
  "id": "id",
  "status": "APPLYING"
}
```

Response:

```json
{
  "user_id": "user_id",
  "job_id": "job_id",
  "id": "id",
  "status": "APPLYING"
}
```

**Update Application Details**

```jsx
PUT /job-applications/{id}
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `JobApplication` schema |

Request Body:

```json
{
  "user_id": "user_id",
  "job_id": "job_id",
  "id": "id",
  "status": "APPLYING"
}
```

Response:

```json
[
  {
    "user_id": "user_id",
    "job_id": "job_id",
    "id": "id",
    "status": "APPLYING"
  }
]
```

**Delete Application**

```jsx
DELETE /job-applications/{id}
```

Path Parameters:

| Name | Required | Type | Description |
| --- | --- | --- | --- |
| `id` | required | string | Identification for `JobApplication` schema |

## Examples

---

### Complete Authentication Flow

```bash
# 1. Register a new user
curl -X 'POST' \
  'https://api.exmaple.com/users' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
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
}'

# 2. Login to get a session token
curl -X 'POST' \
  'https://api.exmaple.com/sessions' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "email": "user@example.com",
  "password": "password"
}'

# 3. Use the session token for authenticated requests
curl -H  "Authorization: sessionToken <x-api-key>" \
  'https://api.exmaple.com/sessions' 
```

### PetSitter Workflow

```bash
# 1. Post a new job
curl -X 'POST' \
  'https://api.exmaple.com/jobs' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "creator_user_id": "creator_user_id",
  "starts_at": "2025-11-23T04:56:07.000+00:00",
  "updated_at": "2025-11-23T04:56:07.000+00:00",
  "worker_user_id": "worker_user_id",
  "activities": [
    "walk",
    "walk"
  ],
  "created_at": "2025-11-23T04:56:07.000+00:00",
  "description": "description",
  "id": "id",
  "ends_at": "2025-11-23T04:56:07.000+00:00",
  "dog": {
    "size": "small",
    "name": "name",
    "years_old": 0,
    "breed": "breed"
  },
  "applications": [
    {
      "user_id": "user_id",
      "job_id": "job_id",
      "id": "id",
      "status": "APPLYING"
    },
    {
      "user_id": "user_id",
      "job_id": "job_id",
      "id": "id",
      "status": "APPLYING"
    }
  ]
}'

# 2. Retrieve job details
curl -X 'GET' \
  'https://api.exmaple.com/jobs/id' \
  -H 'accept: application/json'
	
# 3. Update job information
curl -X 'PUT' \
  'https://api.exmaple.com/jobs/id' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "creator_user_id": "creator_user_id",
  "starts_at": "2025-11-23T04:56:07.000+00:00",
  "updated_at": "2025-11-23T04:56:07.000+00:00",
  "worker_user_id": "worker_user_id",
  "activities": [
    "walk",
    "walk"
  ],
  "created_at": "2025-11-23T04:56:07.000+00:00",
  "description": "description",
  "id": "id",
  "ends_at": "2025-11-23T04:56:07.000+00:00",
  "dog": {
    "size": "small",
    "name": "name",
    "years_old": 0,
    "breed": "breed"
  },
  "applications": [
    {
      "user_id": "user_id",
      "job_id": "job_id",
      "id": "id",
      "status": "APPLYING"
    },
    {
      "user_id": "user_id",
      "job_id": "job_id",
      "id": "id",
      "status": "APPLYING"
    }
  ]
}'

# 4. Retrieve all applications for this job
curl -X 'GET' \
  'https://api.exmaple.com/jobs/id/job-applications' \
  -H 'accept: application/json'

# 5. Remove posted job
curl -X 'DELETE' \
  'https://api.exmaple.com/jobs/id' \
  -H 'accept: */*'
```

### Error Handling

Not available from the code
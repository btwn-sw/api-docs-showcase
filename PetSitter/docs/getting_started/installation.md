# PetSitter Getting Started Guide

This document outlines the installation steps required to set up the environment and implement the server.

**Note: PetSitter API Documentation has been independently purposed.** The PetSitter API Documentation thoroughly reflects the content of its original source files in [Designing APIs with Swagger and OpenAPI](https://github.com/designapis/petsitter/tree/main). Each document serves an independent purpose and may not be perfectly aligned or closely coupled with the others. This PetSitter API reference serves implementation and handlings based on the provided [index.js](https://github.com/designapis/petsitter/blob/main/backend/index.js) and [openapi.yaml](https://github.com/designapis/petsitter/blob/main/backend/api/openapi.yaml) file. 

## Setup

---

### Prerequisites

- Node.js 18 or higher
- npm (installed with Node.js)
- MongoDB 8.0 or use default (reachable via `DB_URL`)

### Base URL

The base URL for all requests to the PetSitter API is:

```bash
https://localhost:8080/
```

**Local development**

```bash
Base API URL: http://localhost:8080/api
```

**Production development**

```bash
https://api.example.com/
```

## Installation

---

### **Step 1. Installation**

1. Install all JavaScript dependencies defined in `package.json`.
    
    ```bash
    npm install
    ```
    
2. Check your toolchain versions.
    - Node.js version: `node -v`
    - npm version: `npm -v`

### Step 2. Configuration

1. Connect MongoDB from the `DB_URL` environment variable.
    
    ```bash
    export DB_URL="mongodb://127.0.0.1:27017/petsitter_db"
    ```
    
2. If not set, it defaults to `mongdb://petsitter-db/petsitter_db`.
3. On Windows (PowerShell):
    
    ```bash
    $env:DB_URL = "mongodb://127.0.0.1:27017/petsitter_db"
    ```
    

### Step 3. Start the Server

1. Start the Node.js server on port `8080`.
    
    ```bash
    npm start
    ```
    
2. To verify the server open the following URLs in your browser:
    - Swagger UI: `http://localhost:8080/docs`
    - Optional frontend: `http://localhost:8080/app`

## Authentication

---

### Registration `POST /users`

1. Set the base URL.
    
    ```bash
    http://localhost:8080/api
    ```
    
2. Create a user account, sending a `POST /users` request. The example of the request body as follows:
    
    ```json
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
    

### Login

1. Login and start a session. The `POST /sessions` endpoint with `email` and `password` returns `token=<auth_header>`. 
    
    ```json
    curl -X 'POST' \
      'https://api.exmaple.com/sessions' \
      -H 'accept: application/json' \
      -H 'Content-Type: application/json' \
      -d '{
      "id": "string",
      "password": "string"
    }'
    ```
    
2. Response example:
    
    ```json
    {
      "user_id": "string",
      "auth_header": "string"
    }
    ```
    
3. Call an endpoint. For example:
    
    ```bash
    PUT /jobs/{id}
    ```
    

## Select an API endpoint

---

### Make your first API request

**Request**

Call an endpoint. The following is a sample `POST /jobs/{id}/job-applications` request that sends some sample data to the `jobs` endpoint using cURL:

```bash
curl -X 'POST' \
  'https://api.exmaple.com/jobs/id/job-applications' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "user_id": "user_id",
  "job_id": "job_id",
  "id": "id",
  "status": "APPLYING"
}'
```

**Response**

The following is a sample JSON response from `POST /jobs/{id}/job-applications` from the API:

```json
{
  "user_id": "your_user_id",
  "job_id": "your_job_id",
  "id": "id",
  "status": "APPLYING"
}
```

## Next steps

---

- Implementation
- Use Cases
- Troubleshooting
# PetSitter Development Guide

---

This document covers the development workflow, architecture, and best practices for contributing to PetSitter.

**Note: PetSitter API Documentation has been independently purposed.** The PetSitter API Documentation thoroughly reflects the content of its original source files in [Designing APIs with Swagger and OpenAPI](https://github.com/designapis/petsitter/tree/main). Each document serves an independent purpose and may not be perfectly aligned or closely coupled with the others. This PetSitter Getting Started Guide serves implementation and handling based on the provided [index.js](https://github.com/designapis/petsitter/blob/main/backend/index.js) and [openapi.yaml](https://github.com/designapis/petsitter/blob/main/backend/api/openapi.yaml) file. 

## Table of Contents

---

- Getting Started
- Project Structure
- Development Environment
- Architecture Overview
- Code Style & Standards
- SDK Guidance
- Building & Running
- Contributing
- Debugging
- Performance Optimization

## Getting Started

---

### Prerequisites

- Node.js 18 or higher
- npm (usually installed with Node.js)
- MongoDB 4.4 or higher (reachable via `DB_URL`)

### Quick Setup

```bash
# Change into a new directory and install all prerequisites 
cd backend

# Install JS dependencies
npm install

# Configure local MongoDB URL 
export DB_URL="mongodb://127.0.0.1/petsitter_db"

# Start the development server
npm start
```

After the server starts, PetSitter listens on `port 8080` and the auto-generated Swagger UI is available at:

- API base: `http://localhost:8080/api`
- API docs: `http://localhost:8080/docs`

## Project Structure

---

```jsx
petsitter/
├── .github/workflows/                          # Add action checkout
│   └── docker.yml                            # Add action checkout
├── backend/                   
│   ├── api/                                    # OpenAPI description
│   │   └── open.yaml
│   ├── controllers/                            # Mongoose controllers
│   │   ├── Jobs.js                           
│   │   └── Users.js
│   ├── models/                                 # Data models
│   │   ├── Job.js                            
│   │   ├── JobApplication.js                 
│   │   └── User.js
│   ├── service/                                # Business logic layer
│   │   ├── JobsService.js                    
│   │   └── UsersService.js                   
│   ├── utils/                                  # Utilities
│   │   ├── auth.js                             # Auth middleware
│   │   ├── problem.js                          # Error responses
│   │   └── writer.js                           # JSON response helper
│   ├── README.md                               # 
│   ├── index.js                                # Entry point(Express + oas3-tools)
│   ├── package-lock.json                       
│   └── package.json                            
├── frontend/                   
│   ├── public/                                 # React app
│   │   ├── favicon.ico                       
│   │   ├── index.html                        
│   │   └── …
│   ├── src/                                    # Sources
│   │   ├── App.css                           
│   │   ├── index.css                          
│   │   └── …
│   └── …       
├── .dockerignore                              
├── .gitignore                                  
├── .strest.yml                                 # Stress/contract testing config
├── Dockerfile                                  # Docker image definition
├── docker-compose.yml                          # Multi-contrainer setup
└── run-in-docker.sh                            # Helper for running in Docker
```

## Development Environment

---

### Node.js

- Use **Node.js 18+** for local development.
- Global tools such as `openapi-generator-cli` can be used to generate SDK.

### MongoDB

- **Local development**: run a MongoDB instance and ensure it matches your `DB_URL`.
- **Docker setup**: `DB_URL` defaults to `mongodb://petsitter -db/petsitter_db`.

### Environment Variables

- `DB_URL`: MongoDB connection string.
- If it is not set, defaults to:
    
    ```json
    mongodb://petsitter-db/petsitter_db
    ```
    
- Example of local environment:
    
    ```bash
    export DB_URL="mongodb://127.0.0.1/petsitter_db"
    npm start
    ```
    

### Database Setup

PetSitter does not include migrations; collections and documents are created on demand when you:

- Register a user via `POST /users`.
- Create jobs via `POST /jobs`.
- Create job applications via `POST /jobs/{id}/job-applications`.

## Architecture Overview

---

### High-Level Architecture

```
┌───────────────────────┐
│   Client / Frontend   │
└───────────────────────┘
           │
┌───────────────────────┐
│  Express + oas3-tools │
└───────────────────────┘
           │
┌───────────────────────┐                 ┌─────────────────────────────────────┐
│      Controllers      │ ──────────────  │         (Users.js, Jobs.js)         │
└───────────────────────┘                 └─────────────────────────────────────┘
           │                                                │
┌───────────────────────┐                 ┌─────────────────────────────────────┐
│       Services        │ ──────────────  │  (UsersService.js, JobsService.js)  │
└───────────────────────┘                 └─────────────────────────────────────┘
           │                                                │           
┌───────────────────────┐                 ┌─────────────────────────────────────┐
│    Mongoose Models    │ ──────────────  │ (User.js, Job.js, JobApplication.js)│
└───────────────────────┘                 └─────────────────────────────────────┘                
           │    
┌───────────────────────┐
│        MongoDB        │
└───────────────────────┘  
```

### **Authentication Flow**

- `auth.basicUserAuth` runs before the router, decoding the `Authorization` header and attaching `req.user` if valid.

## Code Style & Standards

---

### Node.js Code Style

- Use ‘use strict;’ at the top of modules.
    
    ```bash
    'use strict';
    ```
    
- Prefer **async/await** in services for clarity.

### Error Handling

- Use `Problem` from `utils/problem.js` for all error cases.
- Map different error types to HTTP status codes automatically (`400`, `401`, `403`, `404`, and `500`).

### Response Formatting

### Standards

## SDK Guidance

---

### Source of Truth

- The canonical API contract is `/backend/api/openapi.yaml`.
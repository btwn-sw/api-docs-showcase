# PetSitter Deployment Guide

---

This document describes how to deploy the PetSitter backend, from a simple single-server setup to production-ready considerations such as configuration, health checks, and graceful shutdown.

**Note: PetSitter API Documentation has been independently purposed.** The PetSitter API Documentation thoroughly reflects the content of its original source files in [Designing APIs with Swagger and OpenAPI](https://github.com/designapis/petsitter/tree/main). Each document serves an independent purpose and may not be perfectly aligned or closely coupled with the others. This PetSitter Getting Started Guide serves implementation and handling based on the provided [index.js](https://github.com/designapis/petsitter/blob/main/backend/index.js) file. 

## Table of Contents

---

- Overview
- Requirements
- Single Server Deployment
- Production Considerations
- Troubleshooting

## Overview

---

PetSitter is a Node.js + Express application that exposes a REST API described by an `openapi.yaml`. It uses MongoDB as its data store and listens on `serverPort=8080`.

- **Production**: Single PetSitter server connected to a MongoDB instance.
- **HTTP server**: created via `http.createServer(app)` in `index.js` and bound to port `8080`.
- **Database**: MongoDB connection string is read from `process.env.DB_URL` and falls back to `mongodb://petsitter-db/petsitter_db` if not set.
- **API contract**: routing and validation are driven by `openapi.yaml` via `oas3-tools`.
- **Graceful shutdown**: the server handles termination singals (e.g., `SIGINT`, `SIGTERM`) and closes gracefully.

## Requirements

---

These requirements are not mandatory. They are deployment recommendations. The code itself does not enforce a specific Node.js/MongoDB version.

### System Requirements

**Lightweight Service**

- No strict hardware requirements
- Any modern 64-bit server
- Any cloud instance

**Minimum Recommendations (Not requirements)**

- **CPU**: 1 core
- **RAM**: 512 MB - 1 GB
- **Storage**: 10 GB (including OS, Node.js, MongoDB logs/data, and PetSitter code)

### Software Dependencies

- **Node.js**: 18 or later
- **npm**: installed with Node.js
- **MongoDB**: 6.0 or later

### Network Requirements

- **Port**: 8080 (HTTP)/TCP
- **Host**: If PetSitter and MongoDB run on different hosts,
    
    ```jsx
    const databaseUrl = 'mongodb://127.0.0.1/petsitter_db';
    ```
    

### Configuration

**Main Runtime Configuration**

- `DB_URL`: MongoDB connection string used by Mongoose.

**Default Configuration**

- Default: `mongodb://petsitter-db/petsitter_db` when `DB_URL` is not set.

**Example Environment Configuration**

- Change the port requiring a code change or environment-based refactoring

```bash
export DB_URL="mongodb://username:password@mongo-host:27017/petsitter_db"
```

## Single Server Deployment

---

### Step 1. Prepare the Server

```bash
# Update package index
sudo apt-get update

# Install curl and Git
sudo apt-get install -y curl git

# Install Node.js
sudo apt-get install -y nodejs

# (Optional) Create a system user for PetSitter
sudo useradd --system --create-home --shell /bin/bash petsitter
sudo mkdir -p /opt/petsitter
sudo chown -R petsitter:petsitter /opt/petsitter
```

### Step 2. Database Setup

```bash
# Install MongoDB
sudo apt-get install -y mongodb

# Connect to Mongo shell and create DB + user
mongosh <<'EOF'
use petsitter_db
db.createUser({
  user: "petsitter_user",
  pwd: "strong_password_here",
  roles: [{ role: "readWrite", db: "petsitter_db" }]
})
EOF

# Set DB_URL on the PetSitter server
export DB_URL="mongodb://petsitter_user:passsword@localhost:27017/petsitter_db"

```

### Step 3. Install PetSitter

```bash
# Clone the PetSitter backend code into '/opt/petsitter'
sudo -u petsitter -H bash -c '
  cd /opt/petsitter
  git clone https://github.com/designapis/petsitter.git .
  
# Install dependencies
cd backend
npm install
```

### Step 4. Configure and Run

```bash
# Run PetSitter directly for testing
cd /opt/petsitter/backend
export DB_URL="mongodb://petsitter_user:strong_password_here@localhost:27017/petsitter_db"
npm start

# Configure PetSitter as a 'systemd' service for production
sudo tee /etc/systemd/system/petsitter.service >/dev/null <<'EOF'
[Unit]
Description=PetSitter API Service
After=network.target mongod.service

[Service]
Type=simple
User=petsitter
WorkingDirectory=/opt/petsitter/backend
Environment=DB_URL=mongodb://petsitter_user:strong_password_here@localhost:27017/petsitter_db
ExecStart=/usr/bin/npm start
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

# Reload 'systemd' and start the service
sudo systemctl daemon-reload
sudo systemctl enable petsitter
sudo systemctl start petsitter

```

### Step 5. Verify the Deployment

```bash
# Check service status
sudo systemctl status petsitter

# Verify running the API on port 8080
curl http://localhost:8080/docs
```

## Production Consideration

---

### Documentation

- `/docs` reachable from the internal network or via a reverse proxy.

### Authentication

1. Call `POST /sessions` with a valid user’s email and password.
    
    ```bash
    curl -X POST "http://your-host:8080/sessions" \
      -H "Content-Type: application/json" \
      -d '{
        "email": "user@example.com",
        "password": "your_password"
      }'
    
    ```
    
2. Confirm that the response includes an `auth_header`.
    
    ```json
    auth_header: <base64(userId:password)>
    ```
    
3. Use this `auth_header` as the `Authorization` header on a protected endpoint, such as `GET /users/{id}`.

### Security

- Protect and rotate the MongoDB credentials used in `DB_URL`.
- Restrict network access so only trusted systems can reach MongoDB and the PetSitter API.

## Troubleshooting

---

### Service Won’t Start

- Check `systemctl status petsitter` for error messages.
- Verify Node.js installation and on the PATH for the `petsitter` user.
- Ensure `/petsitter/backend` exists
- Make sure the installed project and `node_modules` in the `/backend`.

### **Database Connection Issues**

- Confirm `DB_URL` is set correctly in the environment or the service file.
- Verify MongoDB is running and reachable from `telnet mongo-host 27017` server.
- Check the existing specified database and the user who has `readWrite` privileges.

### **Performance Issues**

- Make sure MongoDB and PetSitter are running on appropriate OS for your workload.
- Monitor MongoDB and Node.js CPU/memoery usage and scale up or out as needed.

## Next steps

---

- Quick Start
- Use Cases
- Troubleshooting
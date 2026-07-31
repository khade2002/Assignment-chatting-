# 🚀 Real-Time WebSocket Chat Application Deployment

A production-style deployment of a containerized real-time WebSocket chat application using **Docker**, **Docker Compose**, **NGINX Reverse Proxy**, **AWS EC2**, and **GitHub Actions CI/CD**.

This project focuses on infrastructure, deployment, container networking, and automation rather than backend development.

---

# Project Overview

The objective of this assignment was to debug and deploy a deliberately misconfigured containerized WebSocket application.

The backend application was already provided. The task involved identifying infrastructure issues, fixing deployment configurations, deploying the application to AWS EC2, and automating deployments using GitHub Actions.

---

# Tech Stack

- FastAPI
- WebSockets
- Docker
- Docker Compose
- NGINX
- AWS EC2
- GitHub Actions
- Ubuntu Linux

---

# Project Structure

```
.
├── app/
│   ├── main.py
│   └── requirements.txt
├── frontend/
│   └── index.html
├── Dockerfile
├── docker-compose.yml
├── nginx.conf
├── README.md
└── .github
    └── workflows
        └── deploy.yml
```

---

# Architecture

```
                 User Browser
                      │
                      │
             HTTP / WebSocket
                      │
                      ▼
          AWS EC2 (Ubuntu Server)
                      │
                      ▼
        Docker Compose Network
              │            │
              │            │
              ▼            ▼
        NGINX Container   FastAPI Container
              │
              ▼
      Static Frontend Files
```

---

# Docker Container Setup

The application consists of two containers.

## Backend Container

- FastAPI Application
- WebSocket Server
- Port 8000

## Frontend Container

- NGINX
- Serves static frontend
- Reverse proxies WebSocket requests
- Port 80

Docker Compose creates a dedicated bridge network allowing containers to communicate using service names.

---

# Docker Networking

Docker Compose automatically creates a bridge network.

Container communication happens using service names.

Example:

```
backend:8000
```

instead of

```
localhost:8000
```

This enables NGINX to communicate with the backend container correctly.

---

# NGINX Reverse Proxy

NGINX performs two responsibilities.

## 1. Serves Frontend

The frontend HTML page is served directly from NGINX.

## 2. Reverse Proxy

NGINX forwards all `/ws` requests to the FastAPI backend.

Example:

```
Browser
    │
    ▼
NGINX
    │
    ▼
FastAPI
```

WebSocket upgrade headers are configured to maintain persistent WebSocket connections.

---

# WebSocket Flow

```
Browser
    │
WebSocket Connection
    │
    ▼
NGINX
    │
Proxy Pass
    │
    ▼
FastAPI Backend
```

Messages received by the backend are broadcast to all connected users, enabling real-time multi-user chat.

---

# Issues Found

### Issue 1

Docker container was listening on

```
127.0.0.1
```

instead of

```
0.0.0.0
```

Result:

NGINX could not communicate with the backend container.

Solution:

Changed Uvicorn host binding to

```
0.0.0.0
```

---

### Issue 2

Frontend volume mapping was commented out inside

```
docker-compose.yml
```

Result:

NGINX displayed the default welcome page.

Solution:

Enabled the frontend volume mount.

---

### Issue 3

NGINX WebSocket proxy pointed to

```
localhost:8000
```

instead of

```
backend:8000
```

Result:

WebSocket connection failed.

Solution:

Updated proxy_pass to the Docker service name.

---

### Issue 4

WebSocket upgrade headers were commented.

Result:

WebSocket handshake failed.

Solution:

Enabled

- Upgrade header
- Connection header

---

# Deployment

Application deployed on

- AWS EC2
- Ubuntu Server

Deployment Steps

```
git clone <repository>

cd devops

docker compose up -d --build
```

Application is accessible using

```
http://<EC2-PUBLIC-IP>
```

---

# CI/CD Pipeline

GitHub Actions automatically deploys the application.

Pipeline Flow

```
Developer

↓

Git Push

↓

GitHub Actions

↓

SSH into EC2

↓

Fetch Latest Code

↓

Rebuild Docker Containers

↓

Restart Containers

↓

Deployment Complete
```

Every push to the `main` branch automatically deploys the latest version to the EC2 instance.

---

# Repository Files

- Dockerfile
- docker-compose.yml
- nginx.conf
- GitHub Actions Workflow
- README.md

---

# Future Improvements

- HTTPS using Let's Encrypt
- Terraform Infrastructure
- Monitoring with Grafana
- Redis Integration
- Load Balancer
- Auto Scaling

---

# Author

**Prasad Khade**

DevOps Engineer

```
GitHub:
https://github.com/khade2002
```

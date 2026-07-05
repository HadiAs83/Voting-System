# 🗳️ Kubernetes Voting System

> A three-tier cloud-native voting application deployed on Kubernetes using Docker, PostgreSQL, Node.js and Nginx.

---

# 📖 Table of Contents

- Introduction
- Project Objectives
- System Architecture
- Technologies Used
- Project Structure
- Docker Images
- Kubernetes Architecture
- Application Workflow
- Why Kubernetes?
- Why Docker?
- Next Sections...

---

# 📌 Introduction

This project is a complete implementation of a **three-tier web application** deployed on a Kubernetes cluster.

The application allows users to vote for their favorite infrastructure technology. User votes are stored permanently inside a PostgreSQL database.

The project demonstrates how modern cloud-native applications are developed, containerized, deployed, configured and managed using Kubernetes.

Instead of installing all software directly on a virtual machine, every component of the application runs inside its own isolated Docker container managed by Kubernetes.

---

# 🎯 Project Objectives

The main goals of this project are:

- Containerizing multiple applications using Docker
- Deploying containers on Kubernetes
- Learning Kubernetes resources
- Understanding Service Discovery
- Working with Persistent Storage
- Using Secrets and ConfigMaps
- Connecting multiple services together
- Deploying a production-like application

---

# 🏗️ System Architecture

The application consists of three independent services.

```
                    User
                      │
                      ▼
              Frontend (Nginx)
                      │
                      ▼
             Backend API (Node.js)
                      │
                      ▼
            PostgreSQL Database
                      │
                      ▼
           Persistent Volume (PVC)
```

Each service is deployed independently inside Kubernetes.

---

# 📦 Components

## 1. Frontend

The frontend is responsible for:

- Displaying available technologies
- Sending votes to Backend
- Showing vote results
- Communicating with Backend API using HTTPS

Technology:

- HTML
- CSS
- JavaScript
- Nginx

Container Port:

```
80
```

---

## 2. Backend

The backend is responsible for:

- Receiving vote requests
- Connecting to PostgreSQL
- Inserting votes
- Reading vote statistics
- Returning JSON responses

Technology:

- Node.js
- Express.js

Container Port:

```
3000
```

---

## 3. Database

Database responsibilities:

- Store all votes
- Persist data after Pod restart
- Handle SQL queries

Technology:

- PostgreSQL 15 Alpine

Container Port:

```
5432
```

---

# 🐳 Docker Images

Three Docker images are used.

| Service | Image |
|----------|------------------------------|
| Frontend | hadias83/voting-frontend |
| Backend | hadias83/voting-backend |
| Database | postgres:15-alpine |

The Frontend and Backend images were built manually and pushed to Docker Hub.

The PostgreSQL image is the official image provided by Docker Hub.

---

# 📂 Project Structure

```
Voting-System/
│
├── frontend/
│     ├── index.html
│     ├── entrypoint.sh
│     └── Dockerfile
│
├── backend/
│     ├── server.js
│     ├── package.json
│     └── Dockerfile
│
├── kubernetes/
│     ├── frontend-manifest.yaml
│     ├── backend-manifest.yaml
│     └── postgres-manifest.yaml
│
└── README.md
```

---

# ☸ Kubernetes Architecture

Several Kubernetes resources were used throughout the project.

```
Deployment
│
├── Frontend
├── Backend
└── PostgreSQL

Service
│
├── frontend-service
├── backend-service
└── postgres-service

Storage
│
└── PersistentVolumeClaim

Configuration
│
├── Secret
└── ConfigMap
```

Each resource has a specific responsibility.

---

# Kubernetes Manifest Files

All Kubernetes manifest files used in this project are located inside the `kubernetes/` directory.

The following table lists the manifest files and the Kubernetes resources they define.

| Manifest File | Resources |
|---------------|-----------|
| `kubernetes/frontend-manifest.yaml` | Frontend Deployment, Service |
| `kubernetes/backend-manifest.yaml` | Backend Deployment, Service, ConfigMap, Secret |
| `kubernetes/postgres-manifest.yaml` | PostgreSQL Deployment, Service, PersistentVolumeClaim (PVC) |

Each manifest file may contain multiple Kubernetes resources required for deploying and configuring the application. Keeping related resources together makes the deployment process simpler while maintaining a clear project structure.

---


# 🔄 Application Workflow

When a user votes, the following sequence occurs:

```
Browser
    │
    ▼
Frontend
    │
HTTPS Request
    ▼
Backend API
    │
SQL Query
    ▼
PostgreSQL
    │
Stores Vote
    ▼
Persistent Volume
```

Finally, Backend returns the updated results to Frontend.

---

# ❓ Why Docker?

Docker provides:

- Portability
- Isolation
- Easy deployment
- Consistent runtime environment
- Version control for applications

Without Docker, installing all dependencies manually would be significantly more difficult.

---

# ❓ Why Kubernetes?

Kubernetes automates application management.

It provides:

- Self-healing
- Automatic restart
- Scaling
- Service discovery
- Load balancing
- Persistent storage
- Configuration management

Instead of managing containers manually, Kubernetes continuously monitors and maintains the desired state of the application.

---

# ✅ Features

- Three-tier architecture
- Dockerized services
- Kubernetes deployment
- Persistent PostgreSQL storage
- Secret management
- ConfigMap configuration
- HTTPS communication
- Cloud deployment
- Automatic restart
- Service discovery
- Cloud-native architecture


# ☸ Kubernetes Resources

One of the main objectives of this project was learning how Kubernetes resources work together to deploy a complete cloud-native application.

Instead of running containers manually, Kubernetes manages the entire application lifecycle using declarative resources.

The following resources were implemented.

---

# 🚀 Deployment

## What is a Deployment?

A Deployment is a Kubernetes resource responsible for managing Pods.

Instead of creating Pods manually, Kubernetes creates and manages them automatically.

Responsibilities of a Deployment include:

- Creating Pods
- Restarting failed Pods
- Rolling Updates
- Scaling
- Maintaining the desired number of replicas

---

## Why did we use Deployment?

This project contains three independent applications:

- Frontend
- Backend
- PostgreSQL

Each application requires its own Deployment.

```
Deployment
    │
    ▼
ReplicaSet
    │
    ▼
Pods
```

Whenever a Pod crashes, Kubernetes automatically recreates it.

---

# 🌐 Services

Pods are temporary.

Their IP addresses change after every restart.

Because of this, applications cannot communicate using Pod IP addresses.

Instead, Kubernetes provides Services.

---

## Backend Service

The Backend Service exposes the Node.js API inside the cluster.

```
Frontend
     │
     ▼
backend-service
     │
     ▼
Backend Pod
```

Frontend never communicates directly with the Pod.

It always communicates with the Service.

---

## PostgreSQL Service

Backend connects to PostgreSQL using

```
postgres-service
```

instead of using the Pod IP.

```
Backend
    │
    ▼
postgres-service
    │
    ▼
PostgreSQL Pod
```

This ensures the database remains reachable even if its Pod changes.

---

## Frontend Service

Frontend is exposed outside the Kubernetes cluster.

Users access the application through:

```
https://frontend-df6d9a12ff-main.apps.ir-central1.arvancaas.ir/
```

This Service makes the application publicly accessible.

---

# 🔐 Secret

Sensitive information should never be hardcoded inside source code.

Examples include:

- Database passwords
- Database usernames
- API Keys
- Tokens

Kubernetes provides Secrets for storing confidential information securely.

---

## Secret Used

```
POSTGRES_USER

POSTGRES_PASSWORD
```

Backend also reads database credentials from Kubernetes Secrets.

Instead of writing passwords inside the application code, Kubernetes injects them into the container at runtime.

Advantages:

- Improved security
- Easier password rotation
- Cleaner source code

---

# ⚙ ConfigMap

Not every configuration is confidential.

Values such as:

```
Database Name

Database Host

Application Environment

API URL
```

can be stored inside ConfigMaps.

---

## ConfigMap Used

The project stores

```
DB_NAME
```

inside a ConfigMap.

Backend reads this value when connecting to PostgreSQL.

Advantages:

- Easy configuration
- Reusable across Pods
- Separate configuration from application code

---

# 💾 Persistent Volume Claim (PVC)

By default, Kubernetes Pods are ephemeral.

If a Pod is deleted, all files stored inside the container are lost.

For databases, this behavior is unacceptable.

User votes must survive Pod restarts.

Therefore, a PersistentVolumeClaim was attached to PostgreSQL.

---

## Why is PVC required?

Without PVC

```
Vote

Vote

Vote

Restart Pod

↓

All votes disappear
```

With PVC

```
Vote

Vote

Vote

Restart Pod

↓

Votes remain available
```

---

## Mounted Directory

The PostgreSQL container stores all database files inside

```
/var/lib/postgresql/data
```

The PVC is mounted to this directory.

```
Persistent Disk
        │
        ▼
PVC
        │
        ▼
/var/lib/postgresql/data
```

As a result, even after restarting PostgreSQL, all data remains intact.

---

# 🌍 Environment Variables

Instead of modifying source code, Kubernetes injects configuration into containers using Environment Variables.

---

## Backend Variables

```
DB_HOST

DB_PORT

DB_USER

DB_PASSWORD

DB_NAME
```

Backend reads these variables when establishing a PostgreSQL connection.

---

## PostgreSQL Variables

```
POSTGRES_USER

POSTGRES_PASSWORD

POSTGRES_DB
```

These variables are required only during PostgreSQL initialization.

The PostgreSQL container automatically creates:

- Database
- User
- Password

based on these values.

---

# 🔄 Communication Between Components

The complete communication flow is illustrated below.

```
User

 │

 ▼

Frontend

 │ HTTPS

 ▼

Backend API

 │ SQL

 ▼

PostgreSQL

 │

 ▼

Persistent Volume
```

Every request follows this exact sequence.

---

# 🧩 Service Discovery

Applications never communicate using IP addresses.

Instead, Kubernetes DNS automatically resolves Service names.

Example:

```
Backend

↓

postgres-service

↓

10.x.x.x

↓

PostgreSQL
```

Backend only needs the Service name.

If PostgreSQL restarts, communication continues automatically.

---

# 🔁 Self-Healing

One of Kubernetes' most powerful features is self-healing.

If a Pod crashes

```
Backend Pod

↓

Crash

↓

Deployment detects failure

↓

Creates new Pod
```

The application continues running without manual intervention.

---

# 📈 Scalability

Although this project uses a single replica for simplicity,

Kubernetes can easily scale applications.

Example:

```
Backend

Replica = 3

↓

Pod 1

Pod 2

Pod 3
```

Traffic is automatically distributed between all replicas.

This enables high availability and better performance.



# 🚀 Deployment Guide

This section explains how the application was containerized, published to Docker Hub, and deployed on a Kubernetes cluster.

---

# 🐳 Step 1 — Build Docker Images

Each application was first converted into a Docker image.

## Frontend

```bash
docker build -t hadias83/voting-frontend:v12 .
```

---

## Backend

```bash
docker build -t hadias83/voting-backend:v1 .
```

---

# 📤 Step 2 — Push Images to Docker Hub

After building the images locally, they were uploaded to Docker Hub.

## Login

```bash
docker login
```

---

## Push Frontend

```bash
docker push hadias83/voting-frontend:v12
```

---

## Push Backend

```bash
docker push hadias83/voting-backend:v1
```

The PostgreSQL image was not built manually because the official Docker image was used.

```
postgres:15-alpine
```

---

# ☸ Step 3 — Deploy Applications

Three applications were deployed into the Kubernetes cluster.

```
Frontend

Backend

PostgreSQL
```

Each application was deployed independently.

---

# Backend Configuration

The backend container requires database connection information.

Environment variables:

```
DB_HOST
DB_PORT
DB_USER
DB_PASSWORD
DB_NAME
```

These values are injected into the container by Kubernetes.

---

# PostgreSQL Configuration

The PostgreSQL container requires three mandatory variables.

```
POSTGRES_USER

POSTGRES_PASSWORD

POSTGRES_DB
```

When PostgreSQL starts for the first time, it automatically creates the database using these values.

---

# Frontend Configuration

Frontend communicates with Backend through HTTPS.

Using HTTPS avoids browser Mixed Content errors.

---

# 💾 Persistent Storage

A PersistentVolumeClaim (PVC) was attached to PostgreSQL.

Mounted directory:

```
/var/lib/postgresql/data
```

Purpose:

- Keep database files
- Prevent data loss
- Preserve votes after Pod restart

---

# Application Endpoints

After the successful deployment of the project, the application services were accessible through the following URLs.

## Frontend

```text
https://frontend-df6d9a12ff-main.apps.ir-central1.arvancaas.ir/
```

## Backend

```text
https://backend-df6d9a12ff-chorsi.apps.ir-central1.arvancaas.ir/
```

> در صورت تغییر دامنه یا Deploy مجدد، این آدرس‌ها نیز تغییر خواهند کرد.

---

# 🔍 Application Testing

After deployment, several tests were performed.

---

## Test 1 — Frontend Availability

Open the deployed Frontend application:

https://frontend-df6d9a12ff-main.apps.ir-central1.arvancaas.ir/

Expected result:

- Application loads successfully.
- Voting buttons are visible.

---

## Test 2 — Backend API

Backend API should respond successfully.

Example endpoint:

```
GET /api/votes
```

Expected response:

```json
[]
```

or

```json
[
  {
    "tool":"Docker",
    "votes":5
  }
]
```

---

## Test 3 — Submit Vote

Click any voting button.

Expected result:

- Vote is successfully stored.
- Counter increases immediately.

---

## Test 4 — Database Persistence

1. Submit several votes.
2. Restart PostgreSQL.
3. Refresh the page.

Expected result:

All votes remain available.

This confirms that the Persistent Volume is working correctly.

---

# 🔒 Security Considerations

Security is one of the most important aspects of any cloud-native application.

Several best practices were applied throughout this project.

---

## Secrets

Sensitive information such as database credentials should never be hardcoded.

Instead, Kubernetes Secrets were used to store:

- PostgreSQL Username
- PostgreSQL Password

Benefits:

- Prevents exposing passwords inside source code
- Easier credential rotation
- Better security

---


## HTTPS Communication

The Frontend communicates with Backend using HTTPS.

Using HTTPS prevents:

- Mixed Content errors
- Browser security warnings
- Unencrypted traffic

---


# 📈 Scalability

Although the current deployment uses one replica for each service, Kubernetes allows horizontal scaling.

Example:

```
Backend

Replica = 3

↓

Backend-1

Backend-2

Backend-3
```

The Service automatically distributes requests among all replicas.

No application code changes are required.

---

# 🔄 Rolling Updates

Kubernetes Deployments support rolling updates.

When a new Docker image is deployed:

```
Old Pod

↓

New Pod

↓

Traffic Switch

↓

Old Pod Removed
```

Users experience little or no downtime.

---

# 🩺 Self-Healing

One of Kubernetes' most powerful features is self-healing.

If a Pod crashes:

```
Pod

↓

Crash

↓

Deployment detects failure

↓

New Pod created automatically
```

This ensures high availability.

---


# 📌 Conclusion

This project demonstrates the deployment of a complete cloud-native three-tier application using Docker and Kubernetes.

The application consists of three independent services:

- Frontend
- Backend
- PostgreSQL

Each component runs inside its own container while Kubernetes manages networking, storage, scaling and recovery.

Using Persistent Volumes ensures that user votes survive Pod restarts.

Secrets and ConfigMaps separate configuration from application code, improving both security and maintainability.

Overall, this project provides practical experience with containerization, orchestration and cloud-native application deployment.

---

# 📚 References

The following resources were referenced during the design, development, and deployment of this project.

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Docker Documentation](https://docs.docker.com/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Node.js Documentation](https://nodejs.org/docs/latest/api/)
- [NGINX Documentation](https://nginx.org/en/docs/)
- [ArvanCloud Kubernetes Documentation](https://www.arvancloud.ir/docs/)

---

# 👨‍💻 Author

**Name:** Hadi Asgharnejad

[**Docker Hub**](https://hub.docker.com/repositories/hadias83)

**Project:** Voting System

**Course:** Cloud Computing
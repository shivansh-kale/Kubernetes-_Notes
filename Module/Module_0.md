# 🚀 Module 0: Software Engineering Foundations (Before Kubernetes)

> **Goal:** Before learning Kubernetes, understand **how a software application is built, deployed, and reaches users.**
>
> Kubernetes doesn't replace these concepts—it builds on top of them.

---

# 🗺️ Big Picture



Actually, Kubernetes is **one small part** of a much larger software engineering workflow.

```text
Idea
   │
   ▼
Write Code
   │
   ▼
Run Application
   │
   ▼
Networking
   │
   ▼
Docker
   │
   ▼
Image Registry
   │
   ▼
CI/CD
   │
   ▼
Kubernetes
   │
   ▼
Production Users
```

If you understand this flow, Kubernetes becomes much easier.

---

# 📖 Chapter 1: How Software Actually Works

## 🤔 First Principle

Every software application exists to solve a user's problem.

But how does the user communicate with the application?

```text
User
   │
Request
   ▼
Application
   │
Process Request
   ▼
Response
   │
Back to User
```

This is called the **Client-Server Model**.

### Components

| Component | Purpose |
|-----------|---------|
| Client | Requests information (Browser, Mobile App) |
| Server | Computer running the application |
| Request | User asks for data |
| Response | Server sends the result |

---

# 🌐 Chapter 2: Networking Fundamentals

## 🤔 Why do computers need networking?

Suppose your application runs on another computer.

How does your laptop find it?

Every machine connected to a network needs an address.

That address is called an **IP Address**.

```text
Internet
      │
      ▼
IP Address
      │
      ▼
Correct Computer
```

Think of an IP Address like your home address.

Without an address,

no one can find your house.

---

## Localhost

When your application runs on your own laptop,

its address is

```text
localhost

or

127.0.0.1
```

Meaning

> **Talk to this computer only.**

---

## Public IP vs Private IP

### Private IP

Used inside a local network.

Examples

```
192.168.x.x

10.x.x.x

172.16.x.x
```

Cannot be accessed directly from the Internet.

---

### Public IP

Visible on the Internet.

Anyone can send requests to it.

Think

```text
Private IP

↓

Inside House

--------------------

Public IP

↓

House Address
```

---

## DNS

Humans remember names.

Computers understand IP addresses.

DNS translates names into IP addresses.

```text
google.com

↓

DNS

↓

142.xx.xx.xx
```

Without DNS,

we would need to remember IP addresses for every website.

---

# 🔌 Chapter 3: Ports

## 🤔 Why do Ports exist?

One computer can run many applications.

Example

```text
Chrome

MySQL

Redis

FastAPI

VS Code
```

If every application used the same network connection,

the computer wouldn't know where to send requests.

Ports solve this.

---

## Apartment Analogy

Think of a computer as an apartment building.

```text
Computer

↓

Apartment Building
```

The building has one address (IP).

Each apartment has its own number (Port).

```text
IP Address

↓

192.168.1.10

↓

Port 80

Port 443

Port 3306

Port 8000
```

A request needs

```text
IP Address

+

Port Number
```

to reach the correct application.

---

## Common Ports

| Port | Purpose |
|------|----------|
| 80 | HTTP |
| 443 | HTTPS |
| 22 | SSH |
| 3306 | MySQL |
| 5432 | PostgreSQL |
| 6379 | Redis |
| 8000 | FastAPI |
| 8080 | Web Servers |

---

# 🔄 Chapter 4: Port Mapping

## 🤔 Why Port Mapping?

Suppose your FastAPI application runs inside Docker.

Inside the container

```text
FastAPI

↓

Port 8000
```

Your browser cannot directly enter the container.

Need a bridge.

---

## Docker Port Mapping

```bash
docker run -p 8080:8000
```

Meaning

```text
Host Port

8080

↓

Container Port

8000
```

Workflow

```text
Browser

↓

localhost:8080

↓

Docker

↓

Container:8000

↓

Application
```

Later this becomes

```text
Ingress

↓

Service Port

↓

Target Port

↓

Container Port
```

inside Kubernetes.

---

# 🌍 Chapter 5: HTTP & APIs

Applications communicate using HTTP.

```text
Browser

↓

HTTP Request

↓

Server

↓

HTTP Response
```

Common HTTP Methods

| Method | Purpose |
|---------|----------|
| GET | Read Data |
| POST | Create Data |
| PUT | Update Data |
| DELETE | Delete Data |

---

## What is an API?

An API allows one application to communicate with another.

Example

```text
Frontend

↓

GET /users

↓

Backend

↓

Database

↓

JSON Response

↓

Frontend
```

---

# 🐳 Chapter 6: Docker Foundations

## 🤔 Why Docker?

Applications behave differently on different machines.

Reasons

- Different OS
- Different Python Version
- Different Libraries

Docker packages everything together.

```text
Application

↓

Dockerfile

↓

Docker Image

↓

Container
```

---

## Image vs Container

### Image

Blueprint

Template

Recipe

Cannot run by itself.

---

### Container

Running instance of an Image.

Think

```text
Recipe

↓

Cook

↓

Food
```

Recipe = Image

Food = Container

---

# 📦 Chapter 7: Docker Registry

After building an image,

where should Kubernetes download it from?

Need centralized storage.

```text
Docker Build

↓

Docker Hub

↓

Worker Node Pulls Image

↓

Container Starts
```

Popular Registries

- Docker Hub
- AWS ECR
- Azure ACR
- Google Artifact Registry

---

# 🖥️ Chapter 8: Servers

Applications need computers that run continuously.

These are called **Servers**.

```text
Laptop

↓

Virtual Machine

↓

Linux Server

↓

Docker

↓

Kubernetes
```

Cloud providers rent these servers.

Examples

- AWS EC2
- Azure VM
- Google Compute Engine

---

# 🌉 Chapter 9: Reverse Proxy

Imagine you have three applications.

```text
Frontend

Backend

ML API
```

Should users remember three different IP addresses?

No.

Instead,

one component receives every request.

```text
Browser

↓

NGINX

↓

Frontend

or

Backend

or

ML API
```

This idea later becomes

```text
Ingress

↓

Service

↓

Pods
```

---

# 🌿 Chapter 10: Git & GitHub Workflow

Writing code isn't enough.

Need version control.

Workflow

```text
Write Code

↓

Git

↓

Commit

↓

GitHub

↓

Team Collaboration
```

---

## Local vs Remote Repository

```text
Local Repository

↓

Your Laptop

----------------------

Remote Repository

↓

GitHub
```

---

# ⚙️ Chapter 11: CI/CD

## 🤔 Why CI/CD?

Imagine manually doing this every day.

```text
Write Code

↓

Run Tests

↓

Build Docker Image

↓

Push Image

↓

Deploy
```

Repetitive.

Slow.

Error-prone.

Automation solves this.

---

## CI/CD Workflow

```text
Git Push

↓

GitHub

↓

GitHub Actions

↓

Run Tests

↓

Build Docker Image

↓

Push Image to Registry

↓

Deploy Kubernetes
```

---

## CI vs CD

| CI | CD |
|----|----|
| Build & Test Code | Deploy Application |
| Finds bugs early | Delivers changes automatically |

---

# ☁️ Chapter 12: Cloud

Why don't companies run Kubernetes on laptops?

Because applications need

- High Availability
- Scalability
- Reliability
- 24×7 Uptime

Cloud providers offer these services.

Examples

- AWS
- Azure
- Google Cloud

Managed Kubernetes

| Cloud | Kubernetes Service |
|--------|--------------------|
| AWS | EKS |
| Azure | AKS |
| Google Cloud | GKE |

---

# 🧠 Complete Software Engineering Mind Map

```text
                     User
                       │
                       ▼
              Browser / Mobile App
                       │
                  HTTP Request
                       │
                       ▼
                      DNS
                       │
                       ▼
                  IP Address
                       │
                       ▼
                      Port
                       │
                       ▼
                Reverse Proxy
                       │
                       ▼
                 Application API
                       │
                ┌──────┴──────┐
                ▼             ▼
          Business Logic   Database
                │
                ▼
          HTTP Response
                │
                ▼
               User
```

---

# 🚀 Bridge to Kubernetes

Everything you've learned now connects naturally.

```text
Write Code
      │
      ▼
Git
      │
      ▼
GitHub
      │
      ▼
CI/CD Pipeline
      │
      ▼
Docker Build
      │
      ▼
Docker Registry
      │
      ▼
Kubernetes Cluster
      │
      ▼
Deployment
      │
      ▼
ReplicaSet
      │
      ▼
Pods
      │
      ▼
Service
      │
      ▼
Ingress
      │
      ▼
Users
```

---

# 🎯 Learning Outcome

Before starting Kubernetes, you should be able to answer:

- Why does every computer need an IP address?
- Why do ports exist?
- Why can't two applications use the same port?
- What is the difference between localhost, Private IP, and Public IP?
- How does a browser communicate with a server?
- What is an API?
- Why do we need Docker?
- Image vs Container?
- Why do we need Docker Hub?
- Local Repository vs Remote Repository?
- Why does CI/CD exist?
- How does code written on your laptop finally reach users?

> **Once these concepts are clear, Kubernetes becomes a natural extension instead of a collection of new terms.**
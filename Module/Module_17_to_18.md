# 📊 Module 17: Monitoring & Logging

---

## 🤔 Why do we need Monitoring?

Suppose your application is deployed successfully.

```
Users
    │
    ▼
Application Running ✅
```

Everything looks fine.

But after some time, users complain:

- Website is slow.
- API is returning errors.
- Some requests are failing.

Now the question is:

> **How do we know what's happening inside the cluster?**

Just because a Pod is **Running** doesn't mean it's **Healthy**.

We need a way to continuously observe our applications.

This is called **Monitoring**.

---

## 📈 What is Monitoring?

Monitoring means continuously collecting information about the health and performance of your application.

It answers questions like:

- Is CPU usage too high?
- Is memory almost full?
- Is the application responding slowly?
- Is a Pod restarting repeatedly?

Instead of waiting for users to complain,

we detect problems early.

---

## 📝 What is Logging?

Monitoring tells you **that something is wrong.**

Logging tells you **why it is wrong.**

Example

```
Monitoring

↓

CPU Usage = 95%

(Problem Found)
```

But why?

Logs might show

```
Database Connection Failed

or

API Timeout

or

Out of Memory
```

Think like this

```
Monitoring → Detects Problems

Logging → Explains Problems
```

---

# 📊 Metrics Server

## Why do we need it?

Kubernetes itself doesn't continuously show CPU or Memory usage.

Metrics Server collects basic resource metrics from every node.

Example

```
CPU Usage

Memory Usage
```

Useful commands

```bash
kubectl top nodes

kubectl top pods
```

Without Metrics Server,

commands like `kubectl top` won't work.

It is also required by the **Horizontal Pod Autoscaler (HPA)**.

---

# 📈 Prometheus

## What is Prometheus?

Prometheus is the most popular monitoring tool used with Kubernetes.

Think of it as a **data collector**.

It continuously asks applications:

```
How much CPU are you using?

How much Memory?

How many Requests?

How many Errors?
```

and stores these metrics.

```
Application

↓

Metrics

↓

Prometheus
```

---

## Why do we need Prometheus?

Imagine checking CPU usage manually every minute.

Impossible.

Prometheus automatically collects metrics every few seconds.

Examples

- CPU Usage
- Memory Usage
- Network Traffic
- Request Count
- Response Time

---

# 📊 Grafana

## What is Grafana?

Prometheus stores numbers.

But reading thousands of numbers isn't useful.

Grafana converts them into beautiful dashboards.

```
Prometheus

↓

Grafana

↓

Graphs & Dashboards
```

Example Dashboard

```
CPU Usage 📈

Memory Usage 📈

Requests/sec 📈

Errors 📈
```

Now engineers can immediately understand the system's health.

---

# 📜 kubectl logs

Sometimes you don't need a complete logging system.

You just want to see what happened inside one Pod.

```
kubectl logs pod-name
```

Example

```
Server Started...

Database Connected...

Prediction Completed...

Error: Model Not Found
```

Very useful while debugging.

---

# 🔍 kubectl describe

If a Pod isn't starting,

logs may not even exist.

Instead use

```bash
kubectl describe pod pod-name
```

It shows

- Events
- Scheduling Issues
- Image Pull Errors
- Restart Count

Usually this is the first command engineers run while debugging.

---

# Monitoring Workflow

```
Application
      │
      ▼
Metrics Generated
      │
      ▼
Prometheus Collects Metrics
      │
      ▼
Grafana Displays Dashboards
```

For debugging

```
Application Error
      │
      ▼
kubectl logs
      │
or
      ▼
kubectl describe
      │
      ▼
Find Root Cause
```

---

# ⭐ Quick Revision

```
Metrics Server → CPU & Memory Metrics

Prometheus → Collects Metrics

Grafana → Visualizes Metrics

kubectl logs → Application Logs

kubectl describe → Resource Details & Events
```

---

# 🚀 Module 18: Complete Kubernetes Workflow

---

## 🤔 Let's connect everything together

Until now we've learned many Kubernetes objects.

```
Deployment

ReplicaSet

Pods

Service

Ingress

ConfigMap

Secret

PVC

...
```

But how do they work together?

Let's follow the complete journey of an application.

---

# Step 1️⃣ Build the Application

Write your application.

Example

```
FastAPI

Flask

Node.js

Spring Boot
```

---

# Step 2️⃣ Dockerize the Application

Containers are the language Kubernetes understands.

```
Application

↓

Dockerfile

↓

Docker Image
```

---

# Step 3️⃣ Push Image to Registry

Worker Nodes need somewhere to download the image from.

Push it to

- Docker Hub
- AWS ECR
- GCR
- Azure Container Registry

```
Docker Image

↓

Container Registry
```

---

# Step 4️⃣ Create Deployment

Now tell Kubernetes

- Which image to use
- How many Pods to create
- CPU & Memory
- Environment Variables

```
Deployment.yaml

↓

kubectl apply
```

---

# Step 5️⃣ Kubernetes Starts the Application

```
API Server

↓

Scheduler

↓

Worker Node

↓

kubelet

↓

Container Runtime

↓

Pod Running
```

Now your application is alive.

---

# Step 6️⃣ Expose the Application

Pods have temporary IP addresses.

Users should never access Pods directly.

Instead

```
Pods

↓

Service

↓

Stable IP
```

Now other applications can communicate reliably.

---

# Step 7️⃣ Expose to Internet

Users still can't access the Service.

Need one public entry point.

```
Internet

↓

Ingress

↓

Service

↓

Pods
```

Now users can visit

```
example.com
```

instead of Pod IP addresses.

---

# Step 8️⃣ Store Configuration

Instead of hardcoding

```
Database URL

API URL

Environment Variables
```

Store them in

```
ConfigMap
```

Passwords

```
Secrets
```

Now configuration changes don't require rebuilding Docker images.

---

# Step 9️⃣ Store Data

Containers are temporary.

Never store important data inside them.

Instead

```
Application

↓

PVC

↓

Persistent Volume

↓

Disk
```

Even if Pods restart,

data remains safe.

---

# Step 🔟 Keep Application Healthy

Kubernetes continuously checks

```
Startup Probe

↓

Application Started?

-----------------------

Liveness Probe

↓

Application Alive?

-----------------------

Readiness Probe

↓

Ready for Traffic?
```

If something goes wrong,

Kubernetes automatically reacts.

---

# Step 1️⃣1️⃣ Scale Automatically

Traffic increases.

```
2 Pods

↓

High CPU

↓

HPA

↓

6 Pods
```

Traffic decreases.

```
6 Pods

↓

Low CPU

↓

2 Pods
```

No manual intervention required.

---

# Step 1️⃣2️⃣ Monitor Everything

Application is running.

Now observe it.

```
Metrics

↓

Prometheus

↓

Grafana
```

If errors occur

```
kubectl logs

or

kubectl describe
```

Debug the issue.

---

# 🎯 Complete Kubernetes Workflow

```
Write Application
        │
        ▼
Dockerize
        │
        ▼
Push Image to Registry
        │
        ▼
Deployment
        │
Creates
        ▼
ReplicaSet
        │
Maintains
        ▼
Pods
        │
Need Stable Networking
        ▼
Service
        │
Need External Access
        ▼
Ingress
        │
Users Access Application
```

Supporting Components

```
ConfigMap
      │
Configuration

----------------------

Secret
      │
Sensitive Data

----------------------

PVC
      │
Persistent Storage

----------------------

Health Probes
      │
Application Health

----------------------

Requests & Limits
      │
Resource Management

----------------------

HPA
      │
Automatic Scaling

----------------------

Prometheus + Grafana
      │
Monitoring

----------------------

Jobs / CronJobs
      │
Background Tasks
```

---

# 🧠 Final Kubernetes Mental Model

Think of Kubernetes as a **city**.

```
Application
      │
      ▼
Container → Person

Pod → House

ReplicaSet → House Maintenance Team

Deployment → City Manager

Service → Address

Ingress → Main City Gate

ConfigMap → Instructions

Secret → Locker

PVC → Warehouse

HPA → Builds More Houses

Prometheus → CCTV Cameras

Grafana → Control Room Dashboard

Jobs → One-Time Workers

CronJobs → Scheduled Workers
```

If you remember this analogy, you'll naturally remember the role of each Kubernetes object.

---

# ⭐ If You Can Explain This Flow, You Understand Kubernetes

```
Application
      │
Docker
      │
Deployment
      │
ReplicaSet
      │
Pods
      │
Service
      │
Ingress
      │
Users
```

And supporting everything behind the scenes:

-  ConfigMap & Secret → Configuration
-  PVC → Persistent Storage
-  Probes → Health Checks
-  Requests & Limits → Resource Control
-  HPA → Automatic Scaling
-  Prometheus & Grafana → Monitoring
-  Jobs & CronJobs → Background & Scheduled Tasks
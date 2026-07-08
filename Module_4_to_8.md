# 🚀 Module 4: Deployments

---

## ❓Why not create Pods directly?

Pods are **temporary (ephemeral)**.

If a Pod crashes or a node fails, the Pod disappears.

```
Create Pod
     │
     ▼
Application Running
     │
Pod Crashes
     │
     ▼
Application Down ❌
```

Creating Pods manually means you have to recreate them every time they fail.

There should be something that automatically manages Pods.

That's where **Deployment** comes in.

---

## 🚀 What is a Deployment?

A **Deployment** is a higher-level Kubernetes object that manages your application's Pods.

Instead of creating Pods directly, you create a Deployment, and Kubernetes handles everything else.

```
Deployment
      │
Creates
      ▼
ReplicaSet
      │
Maintains
      ▼
Pods
```

Think of it as the **manager** of your application.

Responsibilities:

- Create Pods
- Scale Pods
- Replace failed Pods
- Update application versions
- Rollback if updates fail

---

## 🔄 Deployment Workflow

```
deployment.yaml
        │
kubectl apply
        │
        ▼
Deployment Created
        │
Creates
        ▼
ReplicaSet
        │
Creates
        ▼
Pods
```

Notice:

You never create ReplicaSets manually.

Deployment automatically creates and manages them.

---

## 📈 Scaling

Suppose your application has high traffic.

Current

```
2 Pods
```

Traffic increases.

Instead of manually creating Pods:

```
kubectl scale --replicas=5
```

Result

```
Deployment
      │
ReplicaSet
      │
5 Running Pods
```

Scaling becomes automatic and consistent.

---

## 🔄 Rolling Updates

Imagine version **v1** of your application is running.

```
v1
v1
v1
```

Now you deploy **v2**.

Stopping all Pods first would cause downtime.

Instead, Deployment updates Pods one by one.

```
v1 → v2

v1 → v2

v1 → v2
```

Users continue using the application while the update happens.

---

## ↩ Rollback

Suppose version **v2** contains a bug.

Deployment remembers previous versions.

```
v1 ✅

↓

Deploy v2 ❌

↓

Rollback

↓

v1 ✅
```

Useful Commands

```bash
kubectl rollout status deployment/my-app

kubectl rollout history deployment/my-app

kubectl rollout undo deployment/my-app
```

---

# 🛰 Module 5: Services

---

## ❓Why do we need Services?

Pods are temporary.

Whenever a Pod restarts,

- IP Address changes
- Pod name may change

```
Pod

10.0.0.5

↓

Crash

↓

Pod

10.0.0.18
```

If another application was talking directly to the Pod,

everything breaks.

We need a **stable endpoint**.

That's exactly what a Service provides.

---

## 🌐 What is a Service?

A Service is a **permanent network endpoint** that forwards requests to the correct Pods.

```
Client
    │
    ▼
Service
    │
    ▼
Pods
```

Pods may change.

Service stays the same.

---

## 🎯 Labels & Selectors

A Service doesn't know Pods by name.

Instead, it searches using Labels.

Example

```
Pod A

app=backend

Pod B

app=backend

Pod C

app=frontend
```

Service

```
Selector

app=backend
```

Result

```
Requests

↓

Pod A

or

Pod B
```

This is why Labels are extremely important.

---

## 📦 Types of Services

### 1️⃣ ClusterIP (Default)

Used for communication **inside** the cluster.

```
Frontend

↓

Service

↓

Backend
```

Cannot be accessed from the Internet.

---

### 2️⃣ NodePort

Exposes application outside the cluster.

```
Browser

↓

NodeIP:30001

↓

Service

↓

Pods
```

Mostly used for testing.

---

### 3️⃣ LoadBalancer

Used on cloud platforms like AWS, Azure, GCP.

Cloud provider creates an external Load Balancer.

```
Internet

↓

Load Balancer

↓

Service

↓

Pods
```

Production environments mostly use this.

---

### 4️⃣ ExternalName

Instead of forwarding traffic,

it maps to another DNS.

Example

```
database.company.com

↓

ExternalName Service

↓

Application
```

---

# 🏷 Module 6: Labels & Selectors

---

## What are Labels?

Labels are **key-value pairs** attached to Kubernetes resources.

Example

```
app=backend

env=production

version=v2
```

Think of them as tags.

---

## Why do we need Labels?

Imagine a cluster with hundreds of Pods.

```
Pod 1

Pod 2

Pod 3

...

Pod 300
```

How do we identify only backend Pods?

Labels solve this.

```
backend Pods

↓

app=backend
```

---

## What are Selectors?

Selectors search resources using Labels.

```
Selector

app=backend

↓

Matches

Pod 1

Pod 2

Pod 5
```

Services and Deployments use Selectors internally.

Without Labels, Kubernetes cannot know which Pods belong together.

---

# 🔐 Module 7: ConfigMaps & Secrets

---

## ❓Why do we need them?

Suppose your application contains

```
Database URL

API URL

Username

Password
```

If these are hardcoded inside your application,

changing them requires rebuilding the Docker image.

Not good.

Configuration should be stored separately.

---

## 📄 ConfigMap

Stores **non-sensitive configuration**.

Examples

- Database Host
- Environment
- API URL
- Feature Flags

```
Application

↓

Reads

↓

ConfigMap
```

Change configuration without rebuilding the application.

---

## 🔑 Secret

Stores **sensitive information**.

Examples

- Passwords
- API Keys
- AWS Credentials
- Tokens

```
Application

↓

Reads

↓

Secret
```

Secrets are stored separately because they require additional protection.

---

## ConfigMap vs Secret

| ConfigMap | Secret |
|------------|---------|
| Normal Configuration | Sensitive Data |
| API URL | Password |
| Hostname | AWS Keys |
| Environment Variables | Access Tokens |

---

# 💾 Module 8: Volumes & Persistent Storage

---

## ❓Why do we need Storage?

Containers are temporary.

Suppose a database stores data inside a container.

```
Database

↓

Stores Data
```

Now container crashes.

```
Container Deleted

↓

Data Lost ❌
```

Applications need permanent storage.

---

## EmptyDir

Temporary storage.

Created when Pod starts.

Deleted when Pod is deleted.

```
Pod Starts

↓

EmptyDir Created

↓

Pod Deleted

↓

Data Deleted
```

Useful for temporary files.

---

## hostPath

Uses a directory from the Worker Node.

```
Worker Node Folder

↓

Mounted

↓

Container
```

Mostly used for learning and local development.

Not recommended for production.

---

## Persistent Volume (PV)

Represents actual storage inside the cluster.

Think

```
Disk

↓

Persistent Volume
```

Administrator provides the storage.

---

## Persistent Volume Claim (PVC)

Applications should not directly use PV.

Instead,

they request storage.

```
Application

↓

PVC

↓

PV

↓

Disk
```

This keeps applications independent of storage implementation.

---

## StorageClass

Different applications need different storage.

Examples

- SSD
- HDD
- AWS EBS
- Azure Disk

StorageClass automatically creates the required Persistent Volume.

```
Application

↓

PVC

↓

StorageClass

↓

PV Created Automatically
```

This is called **Dynamic Provisioning**.

No manual PV creation required.

---

# ⭐ Workflow Revision

```
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
Need Stable Network
      ▼
Service
      │
Uses
      ▼
Labels & Selectors
      │
Application Needs Config
      ▼
ConfigMap / Secret
      │
Application Needs Storage
      ▼
PVC → PV → Disk
```
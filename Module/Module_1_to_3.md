# ☸️ Module 1: Kubernetes Fundamentals

## What is Kubernetes?

> **Kubernetes (K8s)** is a **Container Orchestration Platform** that automates the deployment, scaling, networking, and management of containerized applications across multiple machines.

Docker solves **containerization**, Kubernetes solves **container management at scale**.


### First Principles

```
Application
      │
      ▼
Container (Docker)
      │
      ▼
One Machine ❌ (Limited)
      │
      ▼
Many Machines
      │
      ▼
Need Automation
      │
      ▼
Kubernetes
```

---

## Why Kubernetes when Docker already exists?

### Docker

```
Application
      │
      ▼
Docker Image
      │
      ▼
Container Runs
```

Docker's responsibility ends here.

### Kubernetes

```
Many Containers
      │
      ▼
Deployment
      ▼
Scaling
      ▼
Load Balancing
      ▼
Self-Healing
      ▼
Rolling Updates
```

### Docker vs Kubernetes

| Docker | Kubernetes |
|---------|------------|
| Creates & runs containers | Manages containers |
| Single machine | Multiple machines |
| Manual scaling | Automatic scaling |
| No self-healing | Recreates failed containers |
| Packaging tool | Orchestration platform |

---

# Kubernetes Cluster Architecture
A Kubernetes cluster has two parts.

```
                Kubernetes Cluster
        ┌─────────────────────────────────┐
        │                                 │
        ▼                                 ▼
 Control Plane (Brain)             Worker Nodes (Execution)
```

### Control Plane

Makes decisions for the cluster.


- Where should Pods run?
- How many Pods should exist?
- Is any Pod unhealthy?


Responsible for:

- Scheduling Pods
- Monitoring cluster
- Maintaining desired state
- Managing resources

### Worker Node

Actually runs your application.

```
Worker Node
      │
      ▼
Pod
      │
      ▼
Container
      │
      ▼
Application
```

---

# Control Plane Components


## 1. API Server

The **entry point** of Kubernetes.

```
Developer
      │
      ▼
kubectl
      │
      ▼
API Server
```

Responsibilities

- Receives every request
- Validates requests
- Talks to all other components

### Why do we need it?

Instead of every component talking to each other directly, they all communicate through one central component.

This keeps Kubernetes organized and secure.
---

## 2. Scheduler

Chooses the best Worker Node.

```
New Pod
      │
      ▼
Scheduler
      │
      ▼
Best Worker Node
```


The Scheduler checks:

- Available CPU
- Available Memory
- Node Health

before making the decision.

---

## Controller Manager

Maintains the **Desired State**.

### Why do we need it?

Pods can crash or be deleted.

The Controller continuously checks the cluster and fixes any mismatch.

```
Desired Pods = 3

Current Pods = 2

        │
        ▼

Creates 1 New Pod
```
Think of it as an **automatic supervisor**.
---

## etcd

Cluster/ K8s's Database.

### Why do we need it?

Kubernetes needs somewhere to store information like:

- Pods
- Deployments
- Services
- Secrets
- Cluster Configuration

Without etcd, Kubernetes would forget the cluster's state.

Think of it as Kubernetes' **memory**.

---

# Worker Node Components

## kubelet

Node Agent.

```
API Server
      │
      ▼
kubelet
      │
      ▼
Start / Stop Pod
```

---

## kube-proxy

Handles networking between Pods.

```
Frontend Pod
      │
      ▼
Backend Pod
      │
      ▼
Database
```

---

## Container Runtime

Actually starts containers.

Examples

- containerd
- CRI-O

```
kubelet
      │
      ▼
Container Runtime
      │
      ▼
Container Starts
```

---

# Complete Workflow

```
Developer
      │
kubectl apply
      │
      ▼
API Server
      │
      ▼
Store State (etcd)
      │
      ▼
Scheduler
      │
      ▼
Worker Node
      │
      ▼
kubelet
      │
      ▼
Container Runtime
      │
      ▼
Pod Running
```

---

# Quick Revision

```
API Server         → Entry Point

Scheduler          → Chooses Node

Controller Manager → Maintains Desired State

etcd               → Database

kubelet            → Executes Instructions

kube-proxy         → Networking

Container Runtime  → Runs Containers
```

---

# 💻 Module 2: kubectl

## What is kubectl?

`kubectl` is the CLI used to communicate with a Kubernetes cluster.

```
Developer
      │
kubectl
      │
      ▼
API Server
      │
      ▼
Cluster
```

---

## Most Important Commands

| Command | Purpose |
|----------|---------|
| `kubectl get` | List resources |
| `kubectl describe` | Detailed information |
| `kubectl logs` | View logs |
| `kubectl exec` | Enter container |
| `kubectl apply -f file.yaml` | Create / Update resources |
| `kubectl delete` | Delete resources |
| `kubectl edit` | Modify live resource |
| `kubectl explain` | View YAML documentation |
| `kubectl rollout` | Manage deployments |

---

## Namespaces

Logical separation inside one cluster.

```
Cluster
   │
   ├── default
   ├── production
   ├── development
   └── testing
```

Commands

```bash
kubectl get ns
kubectl get pods -A
```

---

## kubectl Flow

```
kubectl Command
        │
        ▼
API Server
        │
        ▼
Cluster Executes
        │
        ▼
Result Returned
```

---

# 📦 Module 3: Pods

## What is a Pod?

A **Pod** is the **smallest deployable unit** in Kubernetes.

```
Container
      │
Wrapped Inside
      │
      ▼
Pod
```

Kubernetes never manages containers directly.

---

## Why Pods?

A Pod provides

- Network
- Storage
- Metadata
- Restart Policy

```
Container
      │
      ▼
Pod
      │
Managed by Kubernetes
```

---

## Pod Lifecycle

```
Pending
    │
    ▼
Running
    │
    ▼
Succeeded / Failed
    │
    ▼
Deleted
```

---

## Types of Pods

### Single Container Pod

```
Pod
 └── FastAPI Container
```

---

### Multi Container Pod

Containers share

- IP
- Storage
- Network

```
Pod
 ├── Main App
 └── Helper Container
```

---

### Init Container

Runs **before** the main application.

```
Init Container
      │
Completed
      │
      ▼
Application Starts
```

---

### Sidecar Container

Runs **alongside** the application.

```
Pod
 ├── Main App
 └── Logging / Monitoring Container
```

---

## Important Commands

```bash
kubectl run nginx --image=nginx

kubectl get pods

kubectl describe pod <name>

kubectl delete pod <name>
```

---

## Pod YAML Structure

```yaml
apiVersion:
kind:
metadata:
spec:
```

| Field | Purpose |
|--------|----------|
| apiVersion | Kubernetes API version |
| kind | Resource type |
| metadata | Name, labels, namespace |
| spec | Desired configuration |

---

## Pod Workflow

```
Pod YAML
      │
kubectl apply
      │
      ▼
API Server
      │
      ▼
Scheduler
      │
      ▼
Worker Node
      │
      ▼
Pod Running
```

---

# 🚀 Module 4: Deployments

## Why Deployment?

Without Deployment

```
Pod
 │
Crash
 │
Application Down ❌
```

With Deployment

```
Pod
 │
Crash
 │
Deployment Creates New Pod ✅
```

---

## Deployment Architecture

```
Deployment
      │
      ▼
ReplicaSet
      │
      ▼
Pods
```

---

## ReplicaSet

Maintains the desired number of Pods.

```
Desired Pods = 3
        │
Current = 2
        │
ReplicaSet
        │
Creates 1 Pod
```

---

## Deployment Workflow

```
deployment.yaml
        │
kubectl apply
        │
        ▼
Deployment
        │
        ▼
ReplicaSet
        │
        ▼
Pods Created
```

---

## Scaling

```
2 Pods
   │
kubectl scale
   │
   ▼
5 Pods
```

```bash
kubectl scale deployment my-app --replicas=5
```

---

## Rolling Update

Updates Pods one by one.

```
v1  →  v2
v1  →  v2
v1  →  v2
```

Result

- No downtime
- Safe updates

---

## Rollout Commands

```bash
kubectl rollout status deployment/my-app

kubectl rollout history deployment/my-app

kubectl rollout undo deployment/my-app
```

---

## Complete Flow

```
Developer
      │
Deployment YAML
      │
kubectl apply
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
Application Running
```

---

#  One-Line Revision

```
Container  → Runs Application

Pod        → Smallest Deployable Unit

ReplicaSet → Maintains Pod Count

Deployment → Manages Pods, Updates & Scaling

kubectl    → CLI to Control Cluster

Control Plane → Makes Decisions

Worker Node → Executes Work
```
# ☸️ Module 1: Kubernetes Fundamentals

## What is Kubernetes?

> **Kubernetes (K8s)** is a **Container Orchestration Platform** that automates the deployment, scaling, networking, and management of containerized applications across multiple machines.

---

## 🤔 Why Kubernetes when Docker already exists?

Docker solves **containerization**, Kubernetes solves **container management at scale**.

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

### Kubernetes

```
Many Containers
      │
      ▼
Deploy → Scale → Heal → Update → Manage
```

| Docker | Kubernetes |
|---------|------------|
| Builds & Runs Containers | Manages Containers |
| Single Machine | Multiple Machines |
| Manual Scaling | Automatic Scaling |
| No Self-Healing | Recreates Failed Containers |

---

# 🏗 Kubernetes Cluster Architecture

A Kubernetes cluster has two parts.

```
             Kubernetes Cluster
      ┌──────────────┬──────────────┐
      ▼                             ▼
Control Plane                 Worker Nodes
   (Brain)                     (Workers)
```

### 🧠 Control Plane

Makes decisions for the cluster.

Responsible for:

- Scheduling Pods
- Monitoring cluster
- Maintaining desired state
- Managing resources

### ⚙ Worker Node

Runs your application.

```
Worker Node
      │
      ▼
Pod
      │
      ▼
Container
```

---

# 🧩 Control Plane Components

## API Server

### What is it?

The **entry point** of Kubernetes.

Every request first reaches the API Server.

```
Developer
      │
kubectl
      │
      ▼
API Server
```

### Why do we need it?

Instead of every component talking to each other directly, they all communicate through one central component.

This keeps Kubernetes organized and secure.

---

## Scheduler

### What is it?

Selects the **best Worker Node** for a new Pod.

```
New Pod
    │
    ▼
Scheduler
    │
    ▼
Best Worker Node
```

### Why do we need it?

In a cluster with many machines, Kubernetes needs to decide **where** the application should run.

The Scheduler checks:

- Available CPU
- Available Memory
- Node Health

before making the decision.

---

## Controller Manager

### What is it?

Ensures the **actual cluster state matches the desired state**.

### Why do we need it?

Pods can crash or be deleted.

The Controller continuously checks the cluster and fixes any mismatch.

```
Desired Pods = 3

Current Pods = 2

        │
        ▼

Controller creates 1 new Pod
```

Think of it as an **automatic supervisor**.

---

## etcd

### What is it?

The database of Kubernetes.

### Why do we need it?

Kubernetes needs somewhere to store information like:

- Pods
- Deployments
- Services
- Secrets
- Cluster Configuration

Without etcd, Kubernetes would forget the cluster's state.

---

# ⚙ Worker Node Components

## kubelet

### What is it?

An agent running on every Worker Node.

### Why do we need it?

After the Scheduler selects a node, kubelet actually starts and monitors the Pod.

```
API Server
      │
      ▼
kubelet
      │
      ▼
Container Runtime
```

---

## kube-proxy

### What is it?

Handles networking between Pods and Services.

### Why do we need it?

Applications usually communicate with other applications.

Example

```
Frontend
    │
    ▼
Backend
    │
    ▼
Database
```

kube-proxy makes this communication possible.

---

## Container Runtime

### What is it?

Software responsible for running containers.

Examples

- containerd
- CRI-O

### Why do we need it?

Kubernetes itself cannot start containers.

It instructs the Container Runtime to do so.

---

# 🚀 Kubernetes Workflow

```
Developer
      │
kubectl apply
      │
      ▼
API Server
      │
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

# 💻 Module 2: kubectl

## What is kubectl?

`kubectl` is the **Command Line Interface (CLI)** used to communicate with a Kubernetes cluster.

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

Without `kubectl`, you cannot create, inspect, or manage Kubernetes resources.

---

## Important Commands

| Command | Why we use it |
|----------|---------------|
| `kubectl get` | View resources like Pods, Services, Deployments |
| `kubectl describe` | Detailed information and troubleshooting |
| `kubectl logs` | View application logs |
| `kubectl exec` | Enter inside a running container |
| `kubectl apply` | Create or update resources using YAML |
| `kubectl delete` | Remove resources |
| `kubectl edit` | Modify a running resource |
| `kubectl explain` | View YAML documentation |
| `kubectl rollout` | Manage Deployment updates |

---

## Namespaces

### What is it?

A Namespace is a **logical partition** inside a cluster.

### Why do we need it?

Different teams or environments can use the same cluster without interfering with each other.

```
Cluster
   │
   ├── default
   ├── development
   ├── testing
   └── production
```

Commands

```bash
kubectl get ns
kubectl get pods -A
```

---

## kubectl Workflow

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
Response Returned
```

---

# 📦 Module 3: Pods

## What is a Pod?

A **Pod** is the **smallest deployable unit** in Kubernetes.

A Pod usually contains **one container**, but it can contain multiple containers that work together.

---

## Why do we need Pods?

Containers alone don't provide everything Kubernetes needs.

A Pod adds:

- Network Identity (IP)
- Shared Storage
- Metadata
- Restart Policy

```
Container
      │
Wrapped By
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

## Single Container Pod

Most applications use this.

```
Pod
 └── FastAPI Container
```

Simple and lightweight.

---

## Multi Container Pod

### Why do we use it?

Sometimes multiple containers need to work together very closely.

They share:

- Same IP
- Same Storage
- Same Network

```
Pod
 ├── Main Application
 └── Helper Container
```

Example:

- Web Server + Log Collector

---

## Init Container

### What is it?

A container that runs **before** the main application.

### Why do we use it?

Some setup tasks must finish before the application starts.

Example

```
Download ML Model
        │
Completed
        │
        ▼
Application Starts
```

---

## Sidecar Container

### What is it?

A helper container that runs alongside the main application.

### Why do we use it?

Keeps additional responsibilities separate from the application.

Examples

- Logging
- Monitoring
- Proxy

```
Pod
 ├── Main Application
 └── Logging Container
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

Every Kubernetes resource follows this structure.

```yaml
apiVersion:
kind:
metadata:
spec:
```

| Field | Purpose |
|--------|----------|
| `apiVersion` | Kubernetes API version |
| `kind` | Resource type (Pod, Deployment, Service) |
| `metadata` | Name, labels, namespace |
| `spec` | Desired configuration of the resource |

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

# ⭐ Quick Revision

```
Kubernetes → Manages Containers

Control Plane → Makes Decisions

Worker Node → Runs Applications

API Server → Entry Point

Scheduler → Chooses Best Node

Controller Manager → Maintains Desired State

etcd → Cluster Database

kubelet → Starts & Monitors Pods

kube-proxy → Networking

Container Runtime → Runs Containers

kubectl → CLI for Kubernetes

Pod → Smallest Deployable Unit
```
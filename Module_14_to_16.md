# 🌐 Module 14: Ingress

---

## 🤔 Why was Ingress introduced?

Suppose you've deployed three applications in Kubernetes.

```
Frontend Website

Backend API

ML Prediction API
```

Initially, you expose each application using a **LoadBalancer Service**.

```
Frontend  → LoadBalancer

Backend   → LoadBalancer

ML API    → LoadBalancer
```

Everything works.

But now imagine your company has **30 applications**.

Would you create **30 different LoadBalancers**?

❌ Expensive

❌ Hard to manage

❌ Every application gets a different public IP

There should be **one common entry point** that receives all requests and decides where they should go.

That's why Kubernetes introduced **Ingress**.

---

## 💡 Think of Ingress as a Receptionist

Imagine entering a company office.

You don't directly walk into the CEO's cabin.

Instead,

```
Visitor
    │
    ▼
Reception
    │
    ├── HR
    ├── Finance
    └── Engineering
```

The receptionist checks where you need to go.

Ingress does exactly the same thing.

```
Internet
     │
     ▼
Ingress
     │
     ├── Frontend Service
     ├── Backend Service
     └── ML API Service
```

Instead of remembering many IP addresses,

users only remember one domain.

---

## How does Ingress know where to send requests?

Ingress uses **routing rules**.

Example

```
example.com
      │
      ▼
Frontend

-----------------------

example.com/api
      │
      ▼
Backend

-----------------------

example.com/predict
      │
      ▼
ML API
```

Same website.

Different destinations.

---

## 🚨 Important

Ingress **doesn't actually forward traffic.**

It only stores the routing rules.

Someone has to read those rules and forward the request.

That component is called the **Ingress Controller**.

```
Internet
     │
     ▼
Ingress Rules
     │
     ▼
NGINX Ingress Controller
     │
     ▼
Correct Service
     │
     ▼
Correct Pods
```

Without an Ingress Controller,

Ingress is just a configuration file.

---

## Service vs Ingress

| Service | Ingress |
|----------|----------|
| Exposes one application | Exposes multiple applications |
| Stable endpoint for Pods | Single entry point for cluster |
| Can be internal/external | Mainly HTTP/HTTPS routing |

---

## Workflow

```
Browser
    │
example.com/api
    │
    ▼
Ingress
    │
Backend Service
    │
    ▼
Backend Pods
```

---

# 📦 Module 15: Helm

---

## 🤔 Why was Helm introduced?

Suppose you deploy a simple application.

You create

```
Deployment.yaml

Service.yaml

ConfigMap.yaml

Secret.yaml

Ingress.yaml
```

Five YAML files.

Now imagine deploying Prometheus.

Instead of 5 files,

it may require **30+ YAML files**.

Need another environment?

Edit all files.

Need another version?

Edit all files again.

Deployment becomes difficult.

There should be a way to package everything together.

That's why Helm exists.

---

## 💡 Think of Helm like pip

Python

```
pip install pandas
```

You don't manually download

- pandas
- numpy
- dependencies

pip installs everything.

Helm works the same way.

```
helm install prometheus
```

Instead of manually applying dozens of YAML files,

Helm installs everything automatically.

---

## What is Helm?

Helm is the **Package Manager for Kubernetes.**

It packages all Kubernetes resources into one reusable package called a **Chart**.

```
Helm Chart

├── Deployment

├── Service

├── ConfigMap

├── Secret

├── PVC

└── Ingress
```

Instead of managing many YAML files,

manage one Chart.

---

## What is a Chart?

A Chart is simply a folder containing all Kubernetes resources required by an application.

Think of it like a project template.

```
Chart

↓

Application Ready to Deploy
```

Many companies publish Charts for their software.

Examples

- Prometheus
- Grafana
- Jenkins
- PostgreSQL
- Redis

Instead of writing YAML yourself,

install their Chart.

---

## Why do we need values.yaml?

Suppose Development needs

```
Replicas = 1
```

Production needs

```
Replicas = 5
```

Without Helm,

you edit Deployment YAML.

With Helm,

change only

```
values.yaml
```

Everything updates automatically.

Think of it as a configuration file for the entire application.

---

## Helm Workflow

```
Helm Chart
      │
Reads
values.yaml
      │
Generates
YAML Files
      │
Applies
      ▼
Kubernetes Cluster
```

---

## Common Commands

```bash
helm install

helm upgrade

helm rollback

helm uninstall
```

---

## Why Helm is Popular

Instead of

```
20 YAML Files
```

you simply do

```
helm install my-app
```

One command.

Entire application deployed.

---

# 🤖 Module 16: Kubernetes for MLOps

---

## 🤔 Why is Kubernetes used in MLOps?

Imagine you've trained an ML model.

```
Train Model

↓

model.pkl
```

Now users want predictions.

Simply having the model isn't enough.

Questions arise.

- Where should the model run?
- What if the server crashes?
- What if 10,000 users send requests?
- Where should models be stored?
- How do we update models without downtime?

Kubernetes solves these production problems.

---

## ML Deployment Workflow

A typical ML pipeline looks like this.

```
Train Model

↓

Save Model

↓

Create API (FastAPI / Flask / BentoML)

↓

Docker Image

↓

Push to Registry

↓

Deploy on Kubernetes
```

Kubernetes now manages the application.

---

## Model Serving

Your model runs inside Pods.

```
Users

↓

Service

↓

Pods

↓

Prediction
```

If one Pod crashes,

Deployment automatically creates another one.

Users don't notice.

---

## Scaling ML APIs

Suppose one Pod can handle

```
100 Requests/sec
```

Traffic suddenly becomes

```
1000 Requests/sec
```

Without Kubernetes,

the application becomes slow.

With HPA

```
High CPU

↓

HPA

↓

More Pods

↓

Traffic Distributed
```

Your API scales automatically.

---

## Persistent Storage

Imagine the Pod stores

```
model.pkl
```

inside the container.

Container crashes.

```
Container Deleted

↓

Model Lost ❌
```

Instead,

store models in

```
Persistent Volume

↓

Disk

↓

Pod Mounts Storage
```

Even if Pods restart,

models remain safe.

---

## Configuration

Instead of hardcoding

```
Model Version

Threshold

Database URL
```

Store them in

```
ConfigMap
```

Sensitive values like

```
AWS Keys

Database Password

API Tokens
```

go into

```
Secrets
```

This makes updating configurations much easier.

---

## Batch ML Workloads

Not every ML task is an API.

Training usually runs once.

```
Dataset

↓

Training Job

↓

Model Generated
```

Use

```
Job
```

Need retraining every night?

```
CronJob

↓

Training Job

↓

New Model
```

---

## Complete MLOps Workflow

```
Train Model
      │
      ▼
Docker Image
      │
      ▼
Deployment
      │
Creates
      ▼
Pods
      │
Need Configuration
      ▼
ConfigMap / Secret
      │
Need Storage
      ▼
PVC
      │
Need Networking
      ▼
Service
      │
Need External Access
      ▼
Ingress
      │
Need Scaling
      ▼
HPA
      │
Users Get Predictions
```

---

# ⭐ Beginner Revision

### Ingress

- Solves the problem of exposing **multiple applications using one domain**.
- Routes requests based on URL or hostname.
- Requires an **Ingress Controller** to actually forward traffic.

---

### Helm

- Solves the problem of managing many YAML files.
- Packages Kubernetes resources into a **Chart**.
- Uses `values.yaml` to customize deployments.
- Similar to **pip** for Python.

---

### Kubernetes in MLOps

- Deploys ML APIs reliably.
- Automatically replaces failed Pods.
- Scales during high traffic.
- Stores models using Persistent Volumes.
- Uses ConfigMaps and Secrets for configuration.
- Runs training using Jobs and scheduled retraining using CronJobs.



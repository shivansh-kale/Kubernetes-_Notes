# 📂 Module 9: Namespaces

---

## ❓Why do we need Namespaces?

Imagine a company where multiple teams share the same Kubernetes cluster.

```
Cluster

├── Team A
├── Team B
├── Team C
```

If everyone creates Pods in the same place:

- Resource names may conflict
- Hard to manage applications
- Difficult to apply permissions
- No logical separation

We need a way to divide one cluster into multiple virtual environments.

---

## 📦 What is a Namespace?

A **Namespace** is a logical partition inside a Kubernetes cluster that isolates resources without creating a new cluster.

Think of it like folders in your computer.

```
Computer

├── Documents
├── Downloads
└── Pictures
```

Similarly,

```
Cluster

├── default
├── production
├── development
└── testing
```

Each namespace contains its own Pods, Services, Deployments, etc.

---

## Built-in Namespaces

| Namespace | Purpose |
|-----------|---------|
| `default` | Default workspace for users |
| `kube-system` | Kubernetes internal components |
| `kube-public` | Public resources accessible to everyone |
| `kube-node-lease` | Stores node heartbeat information |

---

## Common Commands

```bash
kubectl get ns

kubectl create namespace dev

kubectl get pods -n dev

kubectl delete namespace dev
```

---

## Workflow

```
One Cluster
      │
      ▼
Namespaces
      │
      ├── Dev
      ├── Testing
      └── Production
```

---

# ❤️ Module 10: Health Checks (Probes)

---

## ❓Why do we need Health Checks?

Just because a container is **running** doesn't mean it's **working**.

Example:

```
Application Started ✅

↓

Database Connection Failed

↓

Application Useless ❌
```

Kubernetes needs a way to know whether an application is actually healthy.

This is done using **Probes**.

---

## 🟢 Liveness Probe

### Purpose

Checks whether the application is still alive.

If it fails repeatedly,

Kubernetes assumes the application is stuck and restarts it.

```
Application

↓

Alive?

↓

Yes → Continue

No → Restart Pod
```

Example

- Infinite loop
- Deadlock
- Application freeze

---

## 🟡 Readiness Probe

### Purpose

Checks whether the application is ready to receive traffic.

Unlike Liveness,

it **doesn't restart** the Pod.

Instead,

it temporarily removes the Pod from the Service.

```
Application Starting...

↓

Ready?

↓

No

↓

Don't Send Requests
```

Once ready,

```
Ready

↓

Receive Traffic
```

Useful when applications need time to connect to databases or load ML models.

---

## 🔵 Startup Probe

### Purpose

Some applications take a long time to start.

Without Startup Probe,

Liveness Probe may think the application has failed and restart it repeatedly.

```
Application Starting

↓

Startup Probe Running

↓

Application Ready

↓

Liveness Probe Starts
```

Useful for

- Spring Boot
- ML Models
- Large Applications

---

## Quick Comparison

| Probe | Purpose |
|--------|----------|
| Startup | Wait until application starts |
| Liveness | Check if application is alive |
| Readiness | Check if application can receive traffic |

---

# 📊 Module 11: Resource Management

---

## ❓Why do we need Resource Limits?

Imagine one Pod consumes all CPU and Memory.

```
Pod A

CPU = 100%

Memory = 100%
```

Other Pods won't have enough resources.

Kubernetes prevents this using Requests and Limits.

---

## CPU & Memory Request

### What is it?

Minimum resources guaranteed to a Pod.

```
Request

↓

Reserve Resources
```

Example

```
CPU = 500m

Memory = 512Mi
```

Scheduler uses Requests while selecting Worker Nodes.

---

## CPU & Memory Limit

### What is it?

Maximum resources a Pod is allowed to use.

```
Pod

↓

Crosses Limit

↓

Restricted
```

If memory exceeds the limit,

```
OOMKilled
```

If CPU exceeds the limit,

```
CPU Throttling
```

---

## Workflow

```
Pod Created
      │
      ▼
Request Resources
      │
Scheduler Finds Node
      │
      ▼
Pod Runs
      │
      ▼
Cannot Cross Limits
```

---

# 📈 Module 12: Autoscaling

---

## ❓Why do we need Autoscaling?

Traffic changes throughout the day.

Morning

```
2 Pods
```

Evening

```
Need 10 Pods
```

Instead of manually scaling,

Kubernetes can do it automatically.

---

## Horizontal Pod Autoscaler (HPA)

Scales the **number of Pods**.

```
High CPU

↓

HPA

↓

More Pods
```

Most commonly used autoscaler.

---

## Vertical Pod Autoscaler (VPA)

Instead of adding Pods,

it increases CPU or Memory of existing Pods.

```
Pod

↓

More CPU

More Memory
```

Useful for applications that can't easily be replicated.

---

## Cluster Autoscaler

Suppose all Worker Nodes are full.

Even HPA cannot create more Pods.

Cluster Autoscaler solves this.

```
More Pods Needed

↓

No Space

↓

Add New Worker Node

↓

Pods Scheduled
```

Used mainly in cloud environments.

---

## Scaling Comparison

| Autoscaler | Scales |
|------------|---------|
| HPA | Number of Pods |
| VPA | CPU & Memory of Pod |
| Cluster Autoscaler | Number of Worker Nodes |

---

# ⏰ Module 13: Jobs & CronJobs

---

## ❓Why do we need Jobs?

Not every application runs forever.

Some tasks need to execute only once.

Examples

- Database Migration
- ML Model Training
- File Processing
- Backup

A Deployment is not suitable because it keeps Pods running forever.

---

## 📦 Job

Runs a Pod until the task completes successfully.

```
Job Created

↓

Pod Runs

↓

Task Completed

↓

Pod Stops
```

If the Pod fails,

Job automatically retries.

---

## ⏰ CronJob

Runs Jobs on a schedule.

Think of Linux Cron.

```
Every Night

↓

CronJob

↓

Job

↓

Backup Completed
```

Examples

- Daily Backup
- Weekly Report
- Data Cleanup
- Scheduled ML Training

---

## Workflow

```
Cron Schedule

↓

CronJob

↓

Job

↓

Pod

↓

Task Completed
```

---

# ⭐ Workflow Revision

```
Cluster
      │
Namespaces
      │
Applications
      │
Health Probes
      │
Resource Requests & Limits
      │
Autoscaling
      │
Jobs / CronJobs
```

### Interview Revision

- **Namespace** → Logically divides a cluster.
- **Liveness Probe** → Restarts unhealthy Pods.
- **Readiness Probe** → Controls traffic to Pods.
- **Startup Probe** → Prevents premature restarts during startup.
- **Request** → Minimum guaranteed resources.
- **Limit** → Maximum allowed resources.
- **HPA** → Adds/removes Pods.
- **VPA** → Changes Pod resources.
- **Cluster Autoscaler** → Adds/removes Worker Nodes.
- **Job** → Runs a task once.
- **CronJob** → Runs tasks on a schedule.
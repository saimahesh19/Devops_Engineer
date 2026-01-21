# 🚢 Kubernetes Complete Guide

## Table of Contents
1. [Introduction to Kubernetes](#introduction-to-kubernetes)
2. [Core Terminology](#core-terminology)
3. [Kubernetes YAML Basics](#kubernetes-yaml-basics)
4. [Kubernetes Architecture](#kubernetes-architecture)
5. [Kubernetes Networking](#kubernetes-networking)
6. [Kubernetes Storage](#kubernetes-storage)
7. [Kubernetes Scaling](#kubernetes-scaling)
8. [Observability](#observability)
9. [Security](#security)
10. [Production Deployment](#production-deployment)
11. [🔐 Security and Access Control (NEW)](#security-and-access-control)
12. [🎯 Advanced Topics for Interviews (NEW)](#advanced-topics-for-interviews)
13. [💡 Interview Questions & Answers (NEW)](#interview-questions-and-answers)

---

## Introduction to Kubernetes

Kubernetes (K8s) is an **orchestration platform** that helps you automate the deployment, scaling, and management of containerized applications.

### Why Kubernetes?

**Before Kubernetes:**
- ❌ Single-host deployments
- ❌ No auto-healing
- ❌ No auto-scaling
- ❌ Minimalistic setup with no enterprise support
- ❌ No built-in load balancing
- ❌ No firewall management
- ❌ No API gateway
- ❌ Complex networking and service discovery
- ❌ Difficult multi-server (node) management

**With Kubernetes:**
- ✅ Multi-node cluster management
- ✅ Automatic healing and recovery
- ✅ Horizontal and vertical scaling
- ✅ Enterprise-grade features
- ✅ Built-in load balancing
- ✅ Service discovery and DNS
- ✅ Rolling updates and rollbacks
- ✅ Declarative configuration

---

## Core Terminology

### 1. **Cluster**
A **cluster** is a group of machines (nodes) running Kubernetes.
- These machines work together to host your applications
- Contains **Control Plane** (master) and **Worker Nodes**
- Control Plane manages the cluster
- Worker Nodes run applications

### 2. **Node**
A **node** is a single machine in the cluster.
- Runs pods and is managed by the Control Plane
- Has `kubelet` to communicate with the Control Plane
- Can be physical or virtual machines

### 3. **Pod**
A **pod** is the smallest deployment unit in Kubernetes.
- Contains one or more containers
- Containers in a pod share the same memory and network
- Example: A pod running a single nginx container

**Key Points:**
- Containers inside pods share the same IP address
- Pods share storage volumes
- Pods share the same lifecycle
- If a container crashes, the pod controller restarts it

### 4. **Deployment**
A **Deployment** manages pods.
- Ensures a certain number of pods are running
- Handles updates and rollbacks
- Maintains desired state
- Handles scaling automatically

### 5. **Service**
A **Service** exposes pods to the network.
- Provides stable IP and DNS
- Enables communication between pods or external users
- Three main types:

#### **ClusterIP** (Default)
- Only accessible inside the cluster
- Pods inside the cluster communicate using ClusterIP
- Cannot be accessed from outside

#### **NodePort**
- Exposes the service on every worker node's IP
- Port range: 30000-32767
- Example: `http://192.168.1.10:31234`
- **Limitations:** Not secure, not user-friendly, not scalable

#### **LoadBalancer**
- Best way for external access
- Automatically forwards traffic to services
- Highly available and scalable
- Cloud-managed (AWS ELB, GCP Load Balancer, Azure Load Balancer)

### 6. **Namespace**
A **Namespace** is a virtual partition inside a Kubernetes cluster.
- Organizes and isolates resources
- Useful for multi-team clusters
- Separate environments: dev, staging, production

```bash
kubectl create namespace dev
kubectl create deployment nginx --image=nginx -n dev
```

### 7. **ConfigMap & Secret**

#### **ConfigMap**
- Stores non-sensitive configuration data
- Acts like a mailbox for configuration
- Separates code from configuration

#### **Secret**
- Stores sensitive data (passwords, tokens, API keys)
- Base64 encoded (not encrypted by default)
- Should be encrypted at rest in production

### 8. **Ingress**
**Ingress** is used when external users want to access applications inside Kubernetes.
- Receives, routes, and handles external traffic
- Provides load balancing, SSL termination, and URL routing

**Example Routing:**
```
myapp.com/       → nginx-service
myapp.com/api    → backend-service
blog.myapp.com   → blog-service
```

**One entry point → Many apps routed**

#### **Ingress vs Ingress Controller**
- **Ingress:** A configuration file (YAML manifest) that declares routing rules
- **Ingress Controller:** Software that watches Ingress resources and enforces routing rules (e.g., Nginx Ingress Controller, Traefik)

---

## Kubernetes YAML Basics

Every Kubernetes YAML file has **4 main sections:**

### 1. **apiVersion**
Tells Kubernetes which API version the resource uses.

```yaml
apiVersion: v1                      # Pod, Service, ConfigMap, Secret
apiVersion: apps/v1                 # Deployment, StatefulSet, DaemonSet
apiVersion: networking.k8s.io/v1    # Ingress, NetworkPolicy
```

### 2. **kind**
Specifies what resource you're creating.

```yaml
kind: Pod
kind: Deployment
kind: Service
kind: ConfigMap
kind: Ingress
```

### 3. **metadata**
Provides basic information about the resource.

```yaml
metadata:
  name: my-app
  namespace: default
  labels:
    app: my-app
```

### 4. **spec**
The heart of the YAML file. Each resource has its own spec.

---

## YAML Examples

### **Pod YAML**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx:latest
```

### **Deployment YAML**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:latest
          ports:
            - containerPort: 80
```

### **Service YAML**

#### ClusterIP Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 80
```

#### NodePort Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: NodePort
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```

#### LoadBalancer Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 80
```

### **ConfigMap YAML**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  APP_ENV: "production"
  APP_VERSION: "1.0"
```

### **Secret YAML**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: bWFoZXNo        # base64 encoded
  password: QmZiQDM0bmo=    # base64 encoded
```

### **Ingress YAML**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
    - host: my.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
```

### **Job YAML**
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-job
spec:
  template:
    spec:
      containers:
        - name: task
          image: busybox
          command: ["echo", "Hello from Job!"]
      restartPolicy: Never
```

### **CronJob YAML**
```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: my-cronjob
spec:
  schedule: "*/5 * * * *"  # Every 5 minutes
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: task
              image: busybox
              command: ["echo", "Hello from CronJob!"]
          restartPolicy: Never
```

---

## Pod Management Commands

### Create Pod
```bash
kubectl apply -f pod.yaml
```

### Check Pods
```bash
kubectl get pods
```

### Describe Pod
```bash
kubectl describe pod myfirst-pod
```

### Get Pod Logs
```bash
kubectl logs myfirst-pod
```

### Enter Container
```bash
kubectl exec -it myfirst-pod -- /bin/bash
```

### Delete Pod
```bash
kubectl delete pod myfirst-pod
```

---

## Pod Statuses

| Status              | Meaning                             |
|---------------------|-------------------------------------|
| Pending             | Image pulling / scheduling          |
| ContainerCreating   | Creating container                  |
| Running             | Pod running normally                |
| CrashLoopBackOff    | Container keeps crashing repeatedly |
| ImagePullBackOff    | Wrong image name / cannot pull      |
| Completed           | Finished (for jobs)                 |
| Error               | Container exited with error         |
| Terminating         | Pod is being deleted                |

---

## How Pods Get an IP Address

Every pod gets its own IP address inside the cluster.

```bash
curl http://10.244.1.10:80
```

The IP is assigned by the **CNI (Container Network Interface)** plugin.

---

## Kubernetes Architecture

Kubernetes has **2 planes:**

### 1. **Control Plane** (Master)
Decides what should run and where.

### 2. **Data Plane** (Worker Nodes)
Machines that actually run your containers.

---

## Control Plane Components

### 1. **kube-apiserver**
- All `kubectl` commands and requests go here
- Exposes the Kubernetes API
- Stores desired state in etcd
- Validates requests but doesn't make decisions

### 2. **etcd**
- Distributed key-value store
- Stores everything about the cluster
- Without etcd, Kubernetes won't remember anything
- **Needs regular backups**

### 3. **kube-scheduler**
- Acts as a job allocator
- Looks for new pods that need a place to run
- Chooses the best worker node based on:
  - CPU and memory availability
  - Taints and tolerations
  - Affinity rules
  - Node selectors

### 4. **kube-controller-manager**
- Acts as a rule enforcer
- Runs many controllers:
  - ReplicaSet Controller
  - Node Controller
  - Deployment Controller
  - Job Controller
- Example: You asked for 3 replicas, but only 2 are running → Controller creates 1 more

### 5. **cloud-controller-manager**
- Integrates Kubernetes with cloud providers (AWS/GCP/Azure)
- Creates load balancers
- Manages volumes
- Handles cloud-specific lifecycle operations

---

## Data Plane Components (Worker Nodes)

### 1. **kubelet**
- The supervisor on each node
- Talks to the API server
- Ensures containers are running
- Reports health back to the Control Plane

### 2. **Container Runtime**
- Runs container images
- Examples: Docker, containerd, CRI-O

### 3. **kube-proxy**
- Ensures traffic routes to the correct pod
- Implements services using iptables or IPVS
- Handles load balancing at the network level

### 4. **CNI (Container Network Interface) Plugin**
- Assigns pod IPs
- Routes traffic between pods
- Enforces network policies
- Examples: Calico, Flannel, Weave Net, Cilium

---

## Overall Workflow

```
1. Deploy pod → kubectl apply -f app.yaml
2. API server receives request and stores desired state in etcd
3. Controller Manager notices we need (e.g., 3 replicas)
4. Scheduler decides pods should run on which Node (e.g., Node 2)
5. kubelet on Node 2 starts containers using container runtime
6. kube-proxy + CNI set up networking and assign IPs
```

**Flow:**
```
YAML → API Server → etcd → Controllers → Scheduler → kubelet → CNI & kube-proxy
```

---

## Moving Applications to Production

A production-ready cluster must be:

- ✅ **Highly Available:** Multiple API servers, multiple etcd members, spread across multiple Availability Zones
- ✅ **Secure:** RBAC, secrets encryption, network policies, TLS everywhere
- ✅ **Observable:** Logs, metrics, alerts
- ✅ **Backed Up:** Regular etcd backups
- ✅ **Maintained:** Regular updates and patches
- ✅ **Autoscaled:** HPA, VPA, Cluster Autoscaler

---

## Cluster Deployment Using KOPS

**KOPS (Kubernetes Operations)** is a tool for creating Kubernetes clusters on AWS.

### Features:
- ✅ Automates Control Plane + infrastructure setup
- ✅ Stores cluster config in S3
- ✅ Creates VPC, subnets, routing
- ✅ Creates EC2 instances for masters and nodes
- ✅ Sets up etcd
- ✅ Sets up API server load balancer
- ✅ Generates cluster configs
- ✅ Supports rolling upgrades

---

## Kubernetes Networking

Kubernetes follows **4 fundamental rules:**

1. Every Pod gets its own IP
2. Pods can communicate without NAT
3. Nodes can communicate with Pods directly
4. Services provide stable access to Pods

---

## Key Networking Concepts

### 1. **CNI (Container Network Interface)**
A plugin that creates networks for pods.

**Responsibilities:**
- Assigns Pod IP
- Creates network interface for the pod
- Connects pod to the cluster network
- Sets up routing rules

**Popular CNI Plugins:**
- Calico
- Flannel
- Weave Net
- Cilium

### 2. **Pod CIDR**
The **Pod CIDR (Classless Inter-Domain Routing)** is the IP address range from which Kubernetes assigns unique IP addresses to pods.

**Example:**
```
Pod CIDR: 10.244.0.0/16
Pod 1: 10.244.1.5
Pod 2: 10.244.1.8
Pod 3: 10.244.2.9
```

### 3. **Service CIDR**
The **Service CIDR** is a range of IP addresses used for assigning virtual IPs (VIPs) to Services.

**Why Services?**
- Pods can die and change IPs
- Services keep the same IP & DNS
- Clients don't get confused or break

**Example:**
```
1. Create a Service: backend-service → 10.96.45.25
2. Frontend calls: http://backend-service
3. Backend Pod dies & IP changes
4. Service automatically updates its backend endpoints
✔ Service IP does NOT change
✔ DNS name does NOT change
✔ Clients don't care about Pod IPs
```

---

## DNS in Kubernetes (CoreDNS)

**CoreDNS** is responsible for service discovery and dynamic DNS updates within the cluster.

### How It Works:
- CoreDNS runs as a pod in the `kube-system` namespace
- Every pod is automatically configured to ask CoreDNS for name resolution
- CoreDNS answers two kinds of questions:
  - **Internal names:** Kubernetes services
  - **External names:** Internet domains

### DNS Names Format:
```
<service-name>.<namespace>.svc.cluster.local
```

**Example:**
```
backend.default.svc.cluster.local
```

### DNS Resolution Flow:

```
1. Frontend asks: curl http://backend:8080
2. Pod checks /etc/resolv.conf
3. Sees nameserver 10.96.0.10 (CoreDNS Service)
4. Request goes to CoreDNS
5. CoreDNS asks Kubernetes API: "Who is backend?"
6. Kubernetes replies: "Yes → ClusterIP = 10.96.45.20"
7. CoreDNS replies to frontend: backend = 10.96.45.20
8. kube-proxy intercepts and forwards to one of the backend pods
```

---

## How Pods Communicate

### **Case 1: Pod → Pod (Same Node)**
```
Pod A → 10.244.1.5
Pod B → 10.244.1.8

Flow:
1. Pod A sends packet to 10.244.1.8
2. Packet stays inside the node
3. CNI bridge delivers it to Pod B
```
**No kube-proxy involved here.**

### **Case 2: Pod → Pod (Different Nodes)**
```
Pod A → Node-1 → 10.244.1.5
Pod B → Node-2 → 10.244.2.9

Flow:
1. Pod A sends packet to 10.244.2.9
2. Node-1 routing table knows: 10.244.2.0/24 → Node-2
3. Packet travels over the cluster network
4. Node-2 delivers it to Pod B
```

---

## How Services Route Traffic

### Steps:
```
1. Frontend calls Backend: curl http://backend:8080
2. DNS Resolution: Pod asks CoreDNS about "backend"
   CoreDNS replies: backend → 10.96.45.20 (Service IP)
3. Traffic hits Service IP
4. kube-proxy has already installed iptables/IPVS rules
5. Rule: If destination = 10.96.45.20, forward to one of:
   - 10.244.2.9
   - 10.244.3.4
```

**kube-proxy load balances at the network level using iptables or IPVS.**

### Traffic Flow Diagram:
```
Pod
 ↓ (DNS)
Service IP
 ↓ (kube-proxy)
Pod
```

---

## Kubernetes Storage

### The Problem:
If a pod restarts:
- ❌ Container filesystem is wiped
- ❌ Data is lost

**Solution:** Persistent Storage

---

## Storage Concepts

### 1. **PersistentVolume (PV)**
- Real piece of storage in the cluster
- Created by an admin
- Cluster-level resource
- Exists independently of pods

### 2. **PersistentVolumeClaim (PVC)**
- Pods never use PV directly
- Pods use PVC
- PVC is a request for storage

### Flow:
```
Pod → PVC → PV → Actual Storage
```

When a pod starts:
- Kubernetes mounts the PV
- Data persists across pod restarts

---

## StorageClass

**Without StorageClass:**
- Admin manually creates PVs
- Slow and not scalable

**With StorageClass:**
- Automates storage provisioning
- Defines how storage should be created

### StorageClass Describes:
- Disk type (gp3, gp2, standard)
- Performance characteristics
- Provisioner (EBS, Azure Disk, GCE PD, etc.)

### Dynamic Provisioning:
Automates the process of PVC and PV creation.

**Flow:**
```
1. User creates PVC
2. StorageClass automatically creates PV
3. PV binds to PVC
4. Pod uses PVC
```

---

## Kubernetes Scaling

Traffic is not constant:
- Morning → Low traffic
- Afternoon → Medium traffic
- Sale/Incident → Huge spike

**Problems:**
- Too few pods → App crashes ❌
- Too many pods → Money wasted ❌

**Solution:** Autoscaling

---

## Scaling Happens at 4 Levels:

1. **Pod Count** (HPA)
2. **Pod Size** (VPA)
3. **Cluster Size** (Cluster Autoscaler)
4. **Event-Based** (KEDA)

---

## 1. Horizontal Pod Autoscaler (HPA)

**HPA** increases or decreases the number of pods based on metrics.

### How HPA Works:
```
1. Pods expose CPU/memory usage
2. Metrics Server collects metrics
3. HPA watches metrics
4. If threshold crossed → scale pods
   Example: CPU > 70% → scale from 2 → 5 pods
```

### HPA YAML Example:
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

---

## 2. Vertical Pod Autoscaler (VPA)

**VPA** adjusts CPU & memory requests/limits of pods.

**Use Case:**
- Pod count is fine
- But pod size is wrong

**VPA optimizes resource size, not replicas.**

---

## 3. Cluster Autoscaler (CA)

**Cluster Autoscaler** adds or removes worker nodes.

### Flow:
```
1. HPA wants more pods
2. No nodes available
3. Pods stuck in Pending ❌
4. CA notices
5. New node is added (cloud provider)
6. Pod gets scheduled ✅
```

---

## 4. KEDA (Event-Driven Autoscaling)

**KEDA** scales pods based on events, not just resource usage.

### Examples:
- Kafka messages pending
- RabbitMQ queue depth
- AWS SQS messages
- Cron schedules
- HTTP requests

---

## Observability

In production, you always ask **3 questions:**

1. Is my system healthy?
2. What is happening right now?
3. Why did something break?

### Three Pillars of Observability:

- **Logs** → What happened?
- **Metrics** → How much / how often?
- **Traces** → Where is time spent?

---

## Prometheus + Grafana

### **Prometheus:**
- Pulls metrics (scrapes)
- Stores time-series data
- Uses PromQL for queries
- Generates alerts

### **Grafana:**
- Queries Prometheus
- Displays dashboards
- Creates visualizations
- Sends alerts

### Architecture:
```
Application
 ↓
Prometheus (scrapes metrics)
 ↓
Grafana (visualizes)
```

---

## Kube-State-Metrics

**kube-state-metrics** exposes Kubernetes resource state (not resource usage).

### Examples:
- Desired replicas vs available replicas
- Pod status (Running/Pending)
- Node conditions
- Deployment status

---

## OpenTelemetry

**Earlier:**
- One tool for logs
- One tool for metrics
- One tool for tracing

**OpenTelemetry:**
- One standard to collect logs, metrics, and traces
- A collection + instrumentation framework

### Architecture:
```
Application
 ├── Logs ─────────► Loki / ELK
 ├── Metrics ──────► Prometheus
 └── Traces ───────► Jaeger / Tempo
           ▲
     OpenTelemetry
```

---

## Metrics Server vs Prometheus

| Aspect                | Metrics Server        | Prometheus                      |
|-----------------------|-----------------------|---------------------------------|
| Purpose               | Autoscaling & kubectl | Monitoring & alerting           |
| Metrics type          | CPU & Memory only     | Any metric (custom, app, infra) |
| Storage               | No (short-lived)      | Yes (time-series DB)            |
| Retention             | Seconds               | Days / weeks                    |
| Used by               | HPA, kubectl top      | Grafana, alerts                 |
| Querying              | Not supported         | PromQL                          |
| Production monitoring | ❌ No                 | ✅ Yes                          |

---

## How Prometheus Scrapes Kubernetes

### Flow:
```
1. Kubernetes components & apps expose /metrics endpoint
   - kubelet
   - kube-apiserver
   - coredns
   - apps (via exporters)

2. Prometheus runs inside the cluster (Deployment/StatefulSet)

3. Prometheus uses Kubernetes Service Discovery
   - Watches: Pods, Services, Endpoints, Nodes

4. Based on labels/annotations, Prometheus:
   - Discovers targets dynamically
   - Scrapes metrics over HTTP

Example: http://<pod-ip>:<port>/metrics

5. Metrics are stored as time-series data
```

**Note:** Prometheus always **pulls** metrics. No pushing.

---

## Security

Kubernetes security answers **4 questions:**

1. **Who are you?** → Service Accounts
2. **What are you allowed to do?** → RBAC
3. **Who can talk to whom?** → Network Policies
4. **How do we store secrets safely?** → Secrets Management

---

## 1. Service Accounts (Identity for Pods)

**ServiceAccounts** provide pod-level identity to access the Kubernetes API.

### Key Points:
- Namespaced
- Automatically created (`default`)
- Mounted into pods as a token

**Inside a pod:**
```
/var/run/secrets/kubernetes.io/serviceaccount/token
```

This token is used to authenticate to `kube-apiserver`.

---

## 2. RBAC (Role-Based Access Control)

Once identity is known, Kubernetes asks: **"What are you allowed to do?"**

### 4 RBAC Objects:

#### **Role** (Namespace-scoped)
Defines allowed actions within a namespace.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list"]
```

#### **ClusterRole** (Cluster-scoped)
Defines cluster-wide permissions.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: secret-reader
rules:
  - apiGroups: [""]
    resources: ["secrets"]
    verbs: ["get", "list"]
```

#### **RoleBinding**
Binds Role → User/ServiceAccount (namespace-scoped).

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
  - kind: ServiceAccount
    name: my-sa
    namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

#### **ClusterRoleBinding**
Binds ClusterRole → User/ServiceAccount (cluster-scoped).

---

## 3. Network Policies

**By default:** All pods can talk to all pods. This is dangerous.

**NetworkPolicy** controls network traffic between pods.

### Defines:
- Which pods can talk
- On which ports
- In which direction

### Types:

#### **Ingress**
- Incoming traffic from external sources into the cluster
- Managed using an Ingress resource
- Defines rules for routing HTTP/HTTPS traffic
- Provides load balancing, SSL termination, name-based virtual hosting
- Requires an Ingress Controller (Nginx, Traefik, Istio)

#### **Egress**
- Outgoing traffic from pods to external destinations
- By default, Kubernetes allows unrestricted egress
- Can be restricted using Network Policies

### Example NetworkPolicy:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
      ports:
        - protocol: TCP
          port: 8080
```

---

## 4. Secrets Management

Kubernetes Secrets store sensitive data in **base64 format**.

### Usage:
- Environment variables
- Mounted files

### Example:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: bWFoZXNo        # base64 encoded
  password: QmZiQDM0bmo=    # base64 encoded
```

### Best Practices:
- ✅ Use external secret managers in production:
  - AWS Secrets Manager
  - HashiCorp Vault
  - Azure Key Vault
  - Google Secret Manager
- ✅ Enable encryption at rest
- ✅ Use RBAC to restrict access
- ✅ Rotate secrets regularly
- ✅ Never commit secrets to Git

---

## Additional Important Concepts

### 1. **StatefulSets**
For stateful applications that require:
- Stable network identities
- Persistent storage
- Ordered deployment and scaling

**Use Cases:** Databases, message queues, distributed systems

### 2. **DaemonSets**
Ensures a copy of a pod runs on all (or some) nodes.

**Use Cases:** Log collectors, monitoring agents, network plugins

### 3. **Init Containers**
Run before app containers start.

**Use Cases:** Database migrations, configuration setup, waiting for dependencies

### 4. **Liveness & Readiness Probes**

#### **Liveness Probe**
Checks if container is alive. If it fails, kubelet restarts the container.

#### **Readiness Probe**
Checks if container is ready to serve traffic. If it fails, pod is removed from service endpoints.

### 5. **Resource Requests & Limits**
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

### 6. **Taints & Tolerations**
Control which pods can be scheduled on which nodes.

### 7. **Node Affinity**
Constrains which nodes pods can be scheduled on based on labels.

### 8. **Pod Disruption Budgets (PDB)**
Ensures minimum number of pods remain available during voluntary disruptions.

---

## Best Practices

### Development:
- ✅ Use namespaces to organize resources
- ✅ Label everything consistently
- ✅ Use ConfigMaps for configuration
- ✅ Use Secrets for sensitive data
- ✅ Define resource requests and limits
- ✅ Use health checks (liveness/readiness probes)

### Production:
- ✅ Enable RBAC
- ✅ Use Network Policies
- ✅ Encrypt secrets at rest
- ✅ Enable audit logging
- ✅ Use Pod Security Standards
- ✅ Implement monitoring and alerting
- ✅ Regular backups (especially etcd)
- ✅ Use Ingress with TLS
- ✅ Implement autoscaling (HPA, VPA, CA)
- ✅ Use external secret management
- ✅ Multi-zone/region deployment
- ✅ Regular security updates

---

## Common kubectl Commands

### Cluster Info:
```bash
kubectl cluster-info
kubectl get nodes
kubectl get namespaces
```

### Working with Pods:
```bash
kubectl get pods
kubectl get pods -n <namespace>
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs -f <pod-name>  # Follow logs
kubectl exec -it <pod-name> -- /bin/bash
```

### Working with Deployments:
```bash
kubectl get deployments
kubectl describe deployment <name>
kubectl scale deployment <name> --replicas=5
kubectl rollout status deployment <name>
kubectl rollout history deployment <name>
kubectl rollout undo deployment <name>
```

### Working with Services:
```bash
kubectl get services
kubectl describe service <name>
kubectl expose deployment <name> --port=80 --type=LoadBalancer
```

### Apply/Delete Resources:
```bash
kubectl apply -f <file.yaml>
kubectl delete -f <file.yaml>
kubectl delete pod <pod-name>
kubectl delete deployment <name>
```

### Debugging:
```bash
kubectl get events
kubectl top nodes
kubectl top pods
kubectl describe <resource> <name>
```

---

## 🔐 Security and Access Control

This comprehensive section covers all aspects of Kubernetes security that are essential for production environments and commonly asked in interviews.

---

## Authentication in Kubernetes

### What is Authentication?
Authentication answers the question: **"Who are you?"**

Kubernetes supports multiple authentication methods:

### 1. **X.509 Client Certificates**
- Most common method for user authentication
- Certificate contains user identity and groups
- Generated using `openssl` or `cfssl`

**Example:**
```bash
# Generate private key
openssl genrsa -out user.key 2048

# Create certificate signing request
openssl req -new -key user.key -out user.csr -subj "/CN=john/O=developers"

# Sign certificate with cluster CA
openssl x509 -req -in user.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out user.crt -days 365
```

### 2. **Service Account Tokens**
- Used by pods to authenticate to API server
- Automatically mounted in pods
- JWT (JSON Web Token) format

### 3. **OpenID Connect (OIDC)**
- Integration with external identity providers (Google, Azure AD, Okta)
- Users authenticate with IdP, receive token
- Token presented to Kubernetes API

### 4. **Webhook Token Authentication**
- External service validates tokens
- Flexible but requires external dependency

### 5. **Bootstrap Tokens**
- Used for joining new nodes to cluster
- Short-lived tokens

---

## Authorization in Kubernetes

### What is Authorization?
Authorization answers the question: **"What are you allowed to do?"**

Kubernetes supports multiple authorization modes:

### 1. **RBAC (Role-Based Access Control)** ⭐ Most Important

#### Understanding RBAC Components

**Role**: Defines permissions within a namespace
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: production
  name: pod-manager
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch", "create", "update", "delete"]
- apiGroups: [""]
  resources: ["pods/log"]
  verbs: ["get", "list"]
```

**ClusterRole**: Defines cluster-wide permissions
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admin-custom
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
- nonResourceURLs: ["*"]
  verbs: ["*"]
```

**RoleBinding**: Binds a Role to subjects in a namespace
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-manager-binding
  namespace: production
subjects:
- kind: User
  name: john
  apiGroup: rbac.authorization.k8s.io
- kind: ServiceAccount
  name: app-sa
  namespace: production
roleRef:
  kind: Role
  name: pod-manager
  apiGroup: rbac.authorization.k8s.io
```

**ClusterRoleBinding**: Binds a ClusterRole cluster-wide
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-viewer
subjects:
- kind: Group
  name: developers
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: view
  apiGroup: rbac.authorization.k8s.io
```

#### Common RBAC Patterns

**Read-Only Access to Namespace:**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: read-only
rules:
- apiGroups: ["", "apps", "batch"]
  resources: ["*"]
  verbs: ["get", "list", "watch"]
```

**Deployment Manager:**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: production
  name: deployment-manager
rules:
- apiGroups: ["apps"]
  resources: ["deployments", "replicasets"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
- apiGroups: [""]
  resources: ["pods", "pods/log"]
  verbs: ["get", "list", "watch"]
```

**Secret Reader (Specific Secrets):**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: production
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  resourceNames: ["db-password", "api-key"]
  verbs: ["get"]
```

#### RBAC Best Practices

1. **Principle of Least Privilege**: Grant minimum permissions needed
2. **Use Namespaces**: Isolate resources and permissions
3. **Avoid Wildcards**: Be specific with resources and verbs
4. **Regular Audits**: Review and update permissions regularly
5. **Service Account per Application**: Don't use default service account
6. **Group-Based Access**: Use groups instead of individual users

#### Checking RBAC Permissions

```bash
# Check if you can create pods
kubectl auth can-i create pods

# Check if service account can list secrets
kubectl auth can-i list secrets --as=system:serviceaccount:default:my-sa

# Check what user can do
kubectl auth can-i --list --as=john

# Check in specific namespace
kubectl auth can-i get pods --namespace=production --as=john
```

### 2. **ABAC (Attribute-Based Access Control)**
- Policy file defines rules
- Less flexible than RBAC
- Rarely used in modern clusters

### 3. **Node Authorization**
- Special authorizer for kubelet
- Limits kubelet to only access resources on its node

### 4. **Webhook Authorization**
- External service makes authorization decisions
- Flexible but adds latency

---

## Pod Security

### Pod Security Standards (PSS)

Three levels of pod security:

#### 1. **Privileged**
- Unrestricted policy
- Allows all capabilities
- Use only for trusted workloads

#### 2. **Baseline**
- Minimally restrictive
- Prevents known privilege escalations
- Good default for most workloads

#### 3. **Restricted**
- Heavily restricted
- Follows pod hardening best practices
- Recommended for security-critical applications

### Implementing Pod Security Standards

**Namespace Level:**
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: secure-apps
  labels:
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/warn: restricted
```

### Security Context

**Pod-Level Security Context:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
    fsGroupChangePolicy: "OnRootMismatch"
    seccompProfile:
      type: RuntimeDefault
    supplementalGroups: [4000]
  containers:
  - name: app
    image: myapp:latest
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      runAsNonRoot: true
      capabilities:
        drop:
        - ALL
        add:
        - NET_BIND_SERVICE
```

**Key Security Context Fields:**

| Field | Description | Example |
|-------|-------------|---------|
| runAsUser | User ID to run container | 1000 |
| runAsGroup | Group ID to run container | 3000 |
| fsGroup | Group ID for volume ownership | 2000 |
| runAsNonRoot | Prevent running as root | true |
| readOnlyRootFilesystem | Make root filesystem read-only | true |
| allowPrivilegeEscalation | Allow privilege escalation | false |
| capabilities | Linux capabilities to add/drop | drop: [ALL] |
| seccompProfile | Seccomp profile | RuntimeDefault |
| seLinuxOptions | SELinux options | level: "s0:c123,c456" |

---

## Network Security

### Network Policies Deep Dive

**Default Deny All Ingress:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
  namespace: production
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

**Default Deny All Egress:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-egress
  namespace: production
spec:
  podSelector: {}
  policyTypes:
  - Egress
```

**Allow Specific Ingress:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: backend
      tier: api
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    - namespaceSelector:
        matchLabels:
          name: production
    ports:
    - protocol: TCP
      port: 8080
```

**Allow Specific Egress:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-backend-to-database
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Egress
  egress:
  - to:
    - podSelector:
        matchLabels:
          app: postgres
    ports:
    - protocol: TCP
      port: 5432
  - to:
    - namespaceSelector:
        matchLabels:
          name: kube-system
    - podSelector:
        matchLabels:
          k8s-app: kube-dns
    ports:
    - protocol: UDP
      port: 53
```

**Allow External Traffic:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-external-api
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Egress
  egress:
  - to:
    - ipBlock:
        cidr: 0.0.0.0/0
        except:
        - 169.254.169.254/32  # Block metadata service
    ports:
    - protocol: TCP
      port: 443
```

### Network Policy Best Practices

1. **Start with Deny All**: Default deny, then allow specific traffic
2. **Use Labels Effectively**: Organize pods with meaningful labels
3. **Test Policies**: Verify policies work as expected
4. **Document Policies**: Comment why each policy exists
5. **Monitor Traffic**: Use tools like Cilium Hubble to visualize traffic
6. **Namespace Isolation**: Use namespaceSelector for cross-namespace communication

---

## Secrets Management

### Understanding Kubernetes Secrets

**Secret Types:**

| Type | Description | Use Case |
|------|-------------|----------|
| Opaque | Arbitrary user-defined data | General purpose |
| kubernetes.io/service-account-token | Service account token | Pod authentication |
| kubernetes.io/dockercfg | Docker config | Pull images from private registry |
| kubernetes.io/dockerconfigjson | Docker config JSON | Pull images from private registry |
| kubernetes.io/basic-auth | Basic authentication | HTTP basic auth |
| kubernetes.io/ssh-auth | SSH authentication | Git over SSH |
| kubernetes.io/tls | TLS certificate | HTTPS/TLS |
| bootstrap.kubernetes.io/token | Bootstrap token | Node joining |

### Creating Secrets

**From Literal:**
```bash
kubectl create secret generic db-credentials \
  --from-literal=username=admin \
  --from-literal=password=secretpass123
```

**From File:**
```bash
kubectl create secret generic tls-cert \
  --from-file=tls.crt=./server.crt \
  --from-file=tls.key=./server.key
```

**From YAML:**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: api-credentials
type: Opaque
stringData:
  api-key: "my-secret-api-key-123"
  api-secret: "my-secret-api-secret-456"
```

### Using Secrets in Pods

**As Environment Variables:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-secrets
spec:
  containers:
  - name: app
    image: myapp:latest
    env:
    - name: DB_USERNAME
      valueFrom:
        secretKeyRef:
          name: db-credentials
          key: username
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-credentials
          key: password
```

**As Volume Mounts:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-secret-files
spec:
  containers:
  - name: app
    image: myapp:latest
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secrets
      readOnly: true
  volumes:
  - name: secret-volume
    secret:
      secretName: api-credentials
      items:
      - key: api-key
        path: api-key.txt
        mode: 0400
```

### Encrypting Secrets at Rest

**Enable Encryption Configuration:**
```yaml
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
    - secrets
    providers:
    - aescbc:
        keys:
        - name: key1
          secret: <base64-encoded-32-byte-key>
    - identity: {}
```

**Apply to API Server:**
```bash
# Add to kube-apiserver flags
--encryption-provider-config=/etc/kubernetes/encryption-config.yaml
```

**Rotate Encryption Keys:**
```bash
# 1. Add new key as first provider
# 2. Restart API server
# 3. Re-encrypt all secrets
kubectl get secrets --all-namespaces -o json | kubectl replace -f -
# 4. Remove old key
```

### External Secrets Management

#### HashiCorp Vault Integration

**Install External Secrets Operator:**
```bash
helm repo add external-secrets https://charts.external-secrets.io
helm install external-secrets external-secrets/external-secrets
```

**Configure SecretStore:**
```yaml
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: vault-backend
  namespace: production
spec:
  provider:
    vault:
      server: "https://vault.example.com"
      path: "secret"
      version: "v2"
      auth:
        kubernetes:
          mountPath: "kubernetes"
          role: "my-app-role"
          serviceAccountRef:
            name: external-secrets-sa
```

**Create ExternalSecret:**
```yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: vault-secret
  namespace: production
spec:
  refreshInterval: 1h
  secretStoreRef:
    name: vault-backend
    kind: SecretStore
  target:
    name: app-credentials
    creationPolicy: Owner
  data:
  - secretKey: username
    remoteRef:
      key: myapp/database
      property: username
  - secretKey: password
    remoteRef:
      key: myapp/database
      property: password
```

#### AWS Secrets Manager Integration

```yaml
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: aws-secrets-manager
  namespace: production
spec:
  provider:
    aws:
      service: SecretsManager
      region: us-east-1
      auth:
        jwt:
          serviceAccountRef:
            name: external-secrets-sa
```

### Secrets Best Practices

1. **Never Commit Secrets to Git**: Use `.gitignore` for secret files
2. **Use External Secret Managers**: Vault, AWS Secrets Manager, Azure Key Vault
3. **Enable Encryption at Rest**: Protect secrets in etcd
4. **Rotate Secrets Regularly**: Implement secret rotation policies
5. **Limit Secret Access**: Use RBAC to restrict who can read secrets
6. **Audit Secret Access**: Enable audit logging for secret operations
7. **Use Immutable Secrets**: Prevent accidental modifications
8. **Separate Secrets by Environment**: Different secrets for dev/staging/prod
9. **Monitor Secret Usage**: Track which pods access which secrets
10. **Use Short-Lived Secrets**: Implement dynamic secrets when possible

---

## Image Security

### Container Image Scanning

**Using Trivy:**
```bash
# Scan image for vulnerabilities
trivy image nginx:latest

# Scan with severity filter
trivy image --severity HIGH,CRITICAL nginx:latest

# Generate report
trivy image --format json --output report.json nginx:latest
```

**Integrate with CI/CD:**
```yaml
# GitLab CI example
scan_image:
  stage: security
  image: aquasec/trivy:latest
  script:
    - trivy image --exit-code 1 --severity HIGH,CRITICAL $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
```

### Image Pull Secrets

**Create Docker Registry Secret:**
```bash
kubectl create secret docker-registry regcred \
  --docker-server=https://index.docker.io/v1/ \
  --docker-username=myuser \
  --docker-password=mypassword \
  --docker-email=myemail@example.com
```

**Use in Pod:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: private-image-pod
spec:
  containers:
  - name: app
    image: myregistry.io/myapp:v1.0
  imagePullSecrets:
  - name: regcred
```

**Use in ServiceAccount:**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-sa
imagePullSecrets:
- name: regcred
```

### Image Policy

**Admission Controller for Image Policy:**
```yaml
apiVersion: admissionregistration.k8s.io/v1
kind: ValidatingWebhookConfiguration
metadata:
  name: image-policy-webhook
webhooks:
- name: image-policy.example.com
  rules:
  - operations: ["CREATE", "UPDATE"]
    apiGroups: [""]
    apiVersions: ["v1"]
    resources: ["pods"]
  clientConfig:
    service:
      name: image-policy-service
      namespace: kube-system
    caBundle: <base64-ca-cert>
  admissionReviewVersions: ["v1"]
  sideEffects: None
```

### Image Best Practices

1. **Use Official Images**: Start with official base images
2. **Scan for Vulnerabilities**: Integrate scanning in CI/CD
3. **Use Specific Tags**: Avoid `latest` tag
4. **Sign Images**: Use Cosign or Notary for image signing
5. **Minimal Base Images**: Use distroless or alpine images
6. **Multi-Stage Builds**: Reduce image size and attack surface
7. **Run as Non-Root**: Never run containers as root
8. **Read-Only Filesystem**: Make root filesystem read-only
9. **Remove Unnecessary Tools**: Don't include shells or debugging tools in production
10. **Regular Updates**: Keep base images and dependencies updated

---

## Admission Controllers

### What are Admission Controllers?

Admission controllers intercept requests to the Kubernetes API server before objects are persisted, allowing for validation and mutation.

### Types of Admission Controllers

#### 1. **Validating Admission Controllers**
- Validate requests
- Accept or reject requests
- Cannot modify objects

#### 2. **Mutating Admission Controllers**
- Can modify objects
- Run before validating controllers
- Examples: Add labels, inject sidecars

### Common Built-in Admission Controllers

| Name | Type | Purpose |
|------|------|---------|
| NamespaceLifecycle | Validating | Prevents deletion of system namespaces |
| LimitRanger | Validating | Enforces resource limits |
| ServiceAccount | Mutating | Adds default service account |
| DefaultStorageClass | Mutating | Adds default storage class to PVCs |
| ResourceQuota | Validating | Enforces resource quotas |
| PodSecurityPolicy | Validating | Enforces pod security policies (deprecated) |
| PodSecurity | Validating | Enforces Pod Security Standards |
| MutatingAdmissionWebhook | Mutating | Calls external webhooks |
| ValidatingAdmissionWebhook | Validating | Calls external webhooks |

### Enable Admission Controllers

```bash
# Check enabled admission controllers
kubectl -n kube-system get pod kube-apiserver-master -o yaml | grep admission-plugins

# Enable in API server
--enable-admission-plugins=NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,ResourceQuota,PodSecurity
```

### Dynamic Admission Control

**Validating Webhook:**
```yaml
apiVersion: admissionregistration.k8s.io/v1
kind: ValidatingWebhookConfiguration
metadata:
  name: pod-policy-webhook
webhooks:
- name: pod-policy.example.com
  clientConfig:
    service:
      name: pod-policy-service
      namespace: default
      path: "/validate"
    caBundle: <base64-ca-cert>
  rules:
  - operations: ["CREATE", "UPDATE"]
    apiGroups: [""]
    apiVersions: ["v1"]
    resources: ["pods"]
  admissionReviewVersions: ["v1"]
  sideEffects: None
  timeoutSeconds: 5
  failurePolicy: Fail
```

**Mutating Webhook:**
```yaml
apiVersion: admissionregistration.k8s.io/v1
kind: MutatingWebhookConfiguration
metadata:
  name: sidecar-injector
webhooks:
- name: sidecar-injector.example.com
  clientConfig:
    service:
      name: sidecar-injector-service
      namespace: default
      path: "/mutate"
    caBundle: <base64-ca-cert>
  rules:
  - operations: ["CREATE"]
    apiGroups: [""]
    apiVersions: ["v1"]
    resources: ["pods"]
  admissionReviewVersions: ["v1"]
  sideEffects: None
  timeoutSeconds: 5
  failurePolicy: Ignore
```

### Use Cases for Admission Controllers

1. **Policy Enforcement**: Ensure all pods have resource limits
2. **Security**: Block privileged containers
3. **Compliance**: Add required labels or annotations
4. **Sidecar Injection**: Automatically inject logging or monitoring sidecars
5. **Image Policy**: Enforce approved image registries
6. **Namespace Validation**: Ensure proper namespace configuration
7. **Resource Validation**: Validate custom resources

---

## Audit Logging

### What is Audit Logging?

Audit logging records all requests to the Kubernetes API server for security analysis and compliance.

### Audit Policy

**Audit Policy Levels:**

| Level | Description |
|-------|-------------|
| None | Don't log events |
| Metadata | Log metadata only (who, what, when) |
| Request | Log metadata and request body |
| RequestResponse | Log metadata, request, and response |

**Example Audit Policy:**
```yaml
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
# Log all requests at Metadata level
- level: Metadata
  omitStages:
  - RequestReceived

# Log pod changes at Request level
- level: Request
  resources:
  - group: ""
    resources: ["pods"]
  verbs: ["create", "update", "patch", "delete"]

# Log secret access at Metadata level
- level: Metadata
  resources:
  - group: ""
    resources: ["secrets"]

# Don't log read-only requests to certain resources
- level: None
  resources:
  - group: ""
    resources: ["events", "nodes/status", "pods/status"]
  verbs: ["get", "list", "watch"]

# Log everything else at RequestResponse level
- level: RequestResponse
```

### Configure Audit Logging

**API Server Configuration:**
```bash
# Add to kube-apiserver flags
--audit-policy-file=/etc/kubernetes/audit-policy.yaml
--audit-log-path=/var/log/kubernetes/audit.log
--audit-log-maxage=30
--audit-log-maxbackup=10
--audit-log-maxsize=100
```

**Audit Webhook Backend:**
```yaml
apiVersion: v1
kind: Config
clusters:
- name: audit-webhook
  cluster:
    server: https://audit-webhook.example.com/audit
    certificate-authority: /etc/kubernetes/pki/ca.crt
contexts:
- context:
    cluster: audit-webhook
    user: audit-webhook
  name: audit-webhook
current-context: audit-webhook
users:
- name: audit-webhook
  user:
    client-certificate: /etc/kubernetes/pki/audit-webhook.crt
    client-key: /etc/kubernetes/pki/audit-webhook.key
```

### Analyzing Audit Logs

**Search for Failed Authentication:**
```bash
grep "Forbidden" /var/log/kubernetes/audit.log
```

**Find Secret Access:**
```bash
grep "secrets" /var/log/kubernetes/audit.log | grep -v "get"
```

**Track User Activity:**
```bash
grep "user=john" /var/log/kubernetes/audit.log
```

### Audit Best Practices

1. **Enable Audit Logging**: Always enable in production
2. **Define Clear Policy**: Log what matters, avoid noise
3. **Secure Audit Logs**: Protect logs from tampering
4. **Centralize Logs**: Send to SIEM or log aggregation system
5. **Regular Review**: Analyze logs for suspicious activity
6. **Retention Policy**: Keep logs for compliance requirements
7. **Alert on Anomalies**: Set up alerts for suspicious patterns
8. **Rotate Logs**: Implement log rotation to manage disk space

---

## Certificate Management

### Understanding Kubernetes Certificates

**Certificate Types in Kubernetes:**

| Certificate | Purpose | Location |
|-------------|---------|----------|
| CA Certificate | Root certificate authority | /etc/kubernetes/pki/ca.crt |
| API Server Certificate | API server identity | /etc/kubernetes/pki/apiserver.crt |
| Kubelet Certificate | Kubelet identity | /var/lib/kubelet/pki/kubelet.crt |
| Service Account Key | Sign service account tokens | /etc/kubernetes/pki/sa.key |
| etcd CA | etcd root CA | /etc/kubernetes/pki/etcd/ca.crt |
| etcd Server Certificate | etcd server identity | /etc/kubernetes/pki/etcd/server.crt |

### Certificate Lifecycle

**Check Certificate Expiration:**
```bash
# Check API server certificate
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -text | grep "Not After"

# Check all certificates
kubeadm certs check-expiration
```

**Renew Certificates:**
```bash
# Renew all certificates
kubeadm certs renew all

# Renew specific certificate
kubeadm certs renew apiserver

# Restart control plane components
kubectl -n kube-system delete pod -l component=kube-apiserver
kubectl -n kube-system delete pod -l component=kube-controller-manager
kubectl -n kube-system delete pod -l component=kube-scheduler
```

### cert-manager

**Install cert-manager:**
```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.0/cert-manager.yaml
```

**Create ClusterIssuer (Let's Encrypt):**
```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: admin@example.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: nginx
```

**Create Certificate:**
```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: example-com-tls
  namespace: default
spec:
  secretName: example-com-tls
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
  dnsNames:
  - example.com
  - www.example.com
```

**Use in Ingress:**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
    - example.com
    - www.example.com
    secretName: example-com-tls
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: example-service
            port:
              number: 80
```

---

## 🎯 Advanced Topics for Interviews

This section covers advanced Kubernetes concepts that are frequently asked in senior-level interviews.

---

## Resource Management

### Resource Requests and Limits

**Understanding Resources:**

| Resource | Description | Units |
|----------|-------------|-------|
| CPU | Compute processing | millicores (m) or cores |
| Memory | RAM | bytes (Ki, Mi, Gi) |
| Ephemeral Storage | Local storage | bytes (Ki, Mi, Gi) |
| Extended Resources | Custom (GPU, etc.) | integer |

**Example:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-demo
spec:
  containers:
  - name: app
    image: myapp:latest
    resources:
      requests:
        memory: "256Mi"
        cpu: "500m"
      limits:
        memory: "512Mi"
        cpu: "1000m"
```

**What Happens:**
- **Requests**: Guaranteed resources, used for scheduling
- **Limits**: Maximum resources, container killed if exceeded (OOMKilled for memory)

### Quality of Service (QoS) Classes

Kubernetes assigns QoS classes based on resource configuration:

#### 1. **Guaranteed**
- All containers have requests = limits
- Highest priority
- Last to be evicted

```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "500m"
  limits:
    memory: "256Mi"
    cpu: "500m"
```

#### 2. **Burstable**
- Has requests < limits
- Medium priority
- Evicted after BestEffort

```yaml
resources:
  requests:
    memory: "128Mi"
    cpu: "250m"
  limits:
    memory: "256Mi"
    cpu: "500m"
```

#### 3. **BestEffort**
- No requests or limits
- Lowest priority
- First to be evicted

```yaml
# No resources specified
```

### LimitRange

**Enforce Resource Constraints:**
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: resource-constraints
  namespace: development
spec:
  limits:
  - max:
      cpu: "2"
      memory: "2Gi"
    min:
      cpu: "100m"
      memory: "128Mi"
    default:
      cpu: "500m"
      memory: "512Mi"
    defaultRequest:
      cpu: "250m"
      memory: "256Mi"
    type: Container
  - max:
      cpu: "4"
      memory: "4Gi"
    type: Pod
```

### ResourceQuota

**Limit Namespace Resources:**
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: namespace-quota
  namespace: development
spec:
  hard:
    requests.cpu: "10"
    requests.memory: "20Gi"
    limits.cpu: "20"
    limits.memory: "40Gi"
    persistentvolumeclaims: "10"
    pods: "50"
    services: "20"
    services.loadbalancers: "2"
    services.nodeports: "5"
```

---

## Advanced Scheduling

### Node Affinity

**Require Node with Specific Label:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: zone
            operator: In
            values:
            - us-east-1a
  containers:
  - name: app
    image: myapp:latest
```

### Pod Affinity and Anti-Affinity

**Co-locate Pods:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-pod-affinity
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - cache
        topologyKey: kubernetes.io/hostname
  containers:
  - name: app
    image: myapp:latest
```

**Spread Pods Across Nodes:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-pod-anti-affinity
spec:
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - web
        topologyKey: kubernetes.io/hostname
  containers:
  - name: app
    image: myapp:latest
```

### Taints and Tolerations

**Taint a Node:**
```bash
# Add taint
kubectl taint nodes node1 key=value:NoSchedule

# Remove taint
kubectl taint nodes node1 key=value:NoSchedule-
```

**Tolerate Taint:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-toleration
spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
  containers:
  - name: app
    image: myapp:latest
```

**Taint Effects:**
- **NoSchedule**: Don't schedule new pods
- **PreferNoSchedule**: Try not to schedule
- **NoExecute**: Evict existing pods

### Pod Topology Spread Constraints

**Spread Pods Evenly:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-topology-spread
  labels:
    app: web
spec:
  topologySpreadConstraints:
  - maxSkew: 1
    topologyKey: zone
    whenUnsatisfiable: DoNotSchedule
    labelSelector:
      matchLabels:
        app: web
  containers:
  - name: app
    image: myapp:latest
```

---

## StatefulSets Deep Dive

### When to Use StatefulSets

Use StatefulSets for applications that require:
1. Stable, unique network identifiers
2. Stable, persistent storage
3. Ordered, graceful deployment and scaling
4. Ordered, automated rolling updates

### StatefulSet Example

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  serviceName: mysql
  replicas: 3
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: data
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: password
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: fast-ssd
      resources:
        requests:
          storage: 10Gi
```

### Headless Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  clusterIP: None
  selector:
    app: mysql
  ports:
  - port: 3306
    name: mysql
```

**Pod DNS Names:**
```
mysql-0.mysql.default.svc.cluster.local
mysql-1.mysql.default.svc.cluster.local
mysql-2.mysql.default.svc.cluster.local
```

---

## Init Containers

### Use Cases

1. **Wait for Dependencies**: Wait for database to be ready
2. **Setup Configuration**: Generate config files
3. **Clone Repository**: Git clone before app starts
4. **Database Migration**: Run migrations before app

### Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-init
spec:
  initContainers:
  - name: wait-for-db
    image: busybox:latest
    command: ['sh', '-c', 'until nc -z mysql 3306; do echo waiting for mysql; sleep 2; done']
  - name: run-migrations
    image: myapp:latest
    command: ['python', 'manage.py', 'migrate']
    env:
    - name: DATABASE_URL
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: url
  containers:
  - name: app
    image: myapp:latest
    ports:
    - containerPort: 8080
```

---

## Custom Resource Definitions (CRDs)

### What are CRDs?

CRDs extend Kubernetes API to create custom resources.

### Create a CRD

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: applications.example.com
spec:
  group: example.com
  versions:
  - name: v1
    served: true
    storage: true
    schema:
      openAPIV3Schema:
        type: object
        properties:
          spec:
            type: object
            properties:
              replicas:
                type: integer
              image:
                type: string
              port:
                type: integer
  scope: Namespaced
  names:
    plural: applications
    singular: application
    kind: Application
    shortNames:
    - app
```

### Use Custom Resource

```yaml
apiVersion: example.com/v1
kind: Application
metadata:
  name: my-app
spec:
  replicas: 3
  image: myapp:v1.0
  port: 8080
```

---

## Operators

### What are Operators?

Operators are software extensions that use custom resources to manage applications and their components.

### Operator Pattern

1. **Observe**: Watch for changes to custom resources
2. **Analyze**: Determine desired state
3. **Act**: Reconcile current state with desired state

### Example: Database Operator

```yaml
apiVersion: databases.example.com/v1
kind: PostgreSQL
metadata:
  name: my-database
spec:
  version: "13"
  replicas: 3
  storage: 100Gi
  backup:
    enabled: true
    schedule: "0 2 * * *"
```

---

## Service Mesh

### What is a Service Mesh?

A service mesh is an infrastructure layer that handles service-to-service communication.

### Popular Service Meshes

1. **Istio**: Feature-rich, complex
2. **Linkerd**: Lightweight, simple
3. **Consul**: HashiCorp ecosystem
4. **AWS App Mesh**: AWS-native

### Istio Example

**Install Istio:**
```bash
istioctl install --set profile=demo
```

**Enable Sidecar Injection:**
```bash
kubectl label namespace default istio-injection=enabled
```

**Virtual Service:**
```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v2
  - route:
    - destination:
        host: reviews
        subset: v1
```

**Destination Rule:**
```yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: reviews
spec:
  host: reviews
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
```

---

## 💡 Interview Questions and Answers

This section contains common Kubernetes interview questions with detailed answers.

---

## Basic Level Questions

### Q1: What is Kubernetes?

**Answer:**
Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It was originally developed by Google and is now maintained by the Cloud Native Computing Foundation (CNCF).

**Key Features:**
- Automated rollouts and rollbacks
- Service discovery and load balancing
- Storage orchestration
- Self-healing
- Secret and configuration management
- Horizontal scaling

---

### Q2: Explain the difference between a Pod and a Container.

**Answer:**
- **Container**: A lightweight, standalone executable package that includes everything needed to run a piece of software (code, runtime, system tools, libraries).
- **Pod**: The smallest deployable unit in Kubernetes. A Pod can contain one or more containers that share the same network namespace, IP address, and storage volumes.

**Key Differences:**
- Pods are Kubernetes objects; containers are runtime instances
- Multiple containers in a Pod share resources
- Pods have a lifecycle managed by Kubernetes
- Containers within a Pod can communicate via localhost

---

### Q3: What is a Namespace in Kubernetes?

**Answer:**
A Namespace is a way to divide cluster resources between multiple users or teams. It provides a scope for names and allows resource isolation.

**Common Namespaces:**
- `default`: Default namespace for objects with no namespace
- `kube-system`: Namespace for Kubernetes system components
- `kube-public`: Publicly readable namespace
- `kube-node-lease`: Namespace for node heartbeat data

**Use Cases:**
- Multi-tenancy
- Environment separation (dev, staging, prod)
- Resource quotas and limits
- Access control

---

### Q4: What are the main components of Kubernetes architecture?

**Answer:**

**Control Plane Components:**
1. **kube-apiserver**: Front-end for Kubernetes control plane, exposes API
2. **etcd**: Consistent key-value store for cluster data
3. **kube-scheduler**: Assigns pods to nodes
4. **kube-controller-manager**: Runs controller processes
5. **cloud-controller-manager**: Integrates with cloud providers

**Node Components:**
1. **kubelet**: Agent that ensures containers are running
2. **kube-proxy**: Network proxy for service abstraction
3. **Container Runtime**: Software for running containers (containerd, CRI-O)

---

### Q5: Explain the difference between Deployment and StatefulSet.

**Answer:**

| Aspect | Deployment | StatefulSet |
|--------|------------|-------------|
| Use Case | Stateless applications | Stateful applications |
| Pod Identity | Random names | Stable, unique names |
| Storage | Ephemeral or shared | Persistent, per-pod |
| Scaling | Parallel | Ordered (sequential) |
| Updates | Rolling, random order | Rolling, ordered |
| Network Identity | No stable network ID | Stable network ID |
| Examples | Web servers, APIs | Databases, message queues |

---

## Intermediate Level Questions

### Q6: How does Kubernetes handle service discovery?

**Answer:**
Kubernetes provides two main methods for service discovery:

**1. Environment Variables:**
- When a Pod starts, Kubernetes injects environment variables for each active Service
- Format: `{SVCNAME}_SERVICE_HOST` and `{SVCNAME}_SERVICE_PORT`

**2. DNS (Recommended):**
- CoreDNS provides DNS-based service discovery
- Services get DNS names: `<service-name>.<namespace>.svc.cluster.local`
- Pods can resolve service names to ClusterIP addresses
- Supports both A records (IPv4) and AAAA records (IPv6)

**Example:**
```bash
# From any pod, you can access a service named "backend" in the same namespace
curl http://backend:8080

# From a different namespace
curl http://backend.production.svc.cluster.local:8080
```

---

### Q7: Explain Kubernetes networking model.

**Answer:**
Kubernetes networking follows these fundamental rules:

**1. Every Pod gets its own IP address**
- Pods can communicate without NAT
- IP address is unique within the cluster

**2. All Pods can communicate with all other Pods**
- Without NAT
- Across nodes

**3. Agents on a node can communicate with all Pods on that node**

**Implementation:**
- **CNI (Container Network Interface)**: Plugins like Calico, Flannel, Weave implement networking
- **kube-proxy**: Implements Services using iptables or IPVS
- **CoreDNS**: Provides DNS-based service discovery

**Network Types:**
- **Pod Network (Pod CIDR)**: IP range for pods (e.g., 10.244.0.0/16)
- **Service Network (Service CIDR)**: IP range for services (e.g., 10.96.0.0/12)
- **Node Network**: Physical/virtual network for nodes

---

### Q8: What are the different types of Services in Kubernetes?

**Answer:**

**1. ClusterIP (Default):**
- Exposes service on cluster-internal IP
- Only accessible within cluster
- Use for internal communication

**2. NodePort:**
- Exposes service on each node's IP at a static port (30000-32767)
- Accessible from outside cluster via `<NodeIP>:<NodePort>`
- Not recommended for production

**3. LoadBalancer:**
- Creates external load balancer (cloud provider)
- Assigns external IP
- Best for production external access

**4. ExternalName:**
- Maps service to DNS name
- Returns CNAME record
- No proxy involved

**5. Headless Service:**
- ClusterIP set to None
- No load balancing
- Returns pod IPs directly
- Used with StatefulSets

---

### Q9: How does Horizontal Pod Autoscaler (HPA) work?

**Answer:**
HPA automatically scales the number of pods based on observed metrics.

**How it Works:**
1. **Metrics Collection**: Metrics Server collects resource metrics (CPU, memory)
2. **HPA Controller**: Periodically queries metrics (default: 15 seconds)
3. **Calculation**: Compares current metrics with target
4. **Scaling Decision**: Calculates desired replicas
5. **Update**: Updates Deployment/ReplicaSet replica count

**Formula:**
```
desiredReplicas = ceil[currentReplicas * (currentMetricValue / targetMetricValue)]
```

**Example:**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

**Cooldown Periods:**
- **Scale Up**: 3 minutes (default)
- **Scale Down**: 5 minutes (default)

---

### Q10: What is the difference between ConfigMap and Secret?

**Answer:**

| Aspect | ConfigMap | Secret |
|--------|-----------|--------|
| Purpose | Non-sensitive configuration | Sensitive data |
| Encoding | Plain text | Base64 encoded |
| Encryption | Not encrypted | Can be encrypted at rest |
| Size Limit | 1MB | 1MB |
| Use Cases | App config, env vars | Passwords, tokens, keys |
| Visibility | Visible in kubectl get | Hidden by default |
| RBAC | Standard permissions | More restrictive |

**Best Practices:**
- Use ConfigMap for non-sensitive data
- Use Secret for passwords, tokens, certificates
- Enable encryption at rest for Secrets
- Use external secret managers (Vault) for production
- Never commit Secrets to Git

---

## Advanced Level Questions

### Q11: Explain the concept of RBAC in Kubernetes.

**Answer:**
RBAC (Role-Based Access Control) regulates access to Kubernetes resources based on roles.

**Components:**

**1. Role/ClusterRole:**
- Define permissions (what can be done)
- Role: namespace-scoped
- ClusterRole: cluster-scoped

**2. RoleBinding/ClusterRoleBinding:**
- Bind roles to subjects (who can do it)
- RoleBinding: namespace-scoped
- ClusterRoleBinding: cluster-scoped

**3. Subjects:**
- User: Human user
- Group: Group of users
- ServiceAccount: Pod identity

**Example Workflow:**
```
1. Create Role: "pod-reader" can get/list pods
2. Create RoleBinding: Bind "pod-reader" to user "john"
3. Result: John can get/list pods in that namespace
```

**Verbs:**
- get, list, watch (read)
- create, update, patch (write)
- delete, deletecollection (delete)
- * (all)

**Best Practices:**
- Principle of least privilege
- Use groups instead of individual users
- Regular audits
- Avoid wildcards in production

---

### Q12: How do you troubleshoot a CrashLoopBackOff error?

**Answer:**
CrashLoopBackOff means the container keeps crashing and Kubernetes is backing off restart attempts.

**Troubleshooting Steps:**

**1. Check Pod Status:**
```bash
kubectl get pods
kubectl describe pod <pod-name>
```

**2. Check Container Logs:**
```bash
# Current logs
kubectl logs <pod-name>

# Previous container logs
kubectl logs <pod-name> --previous

# Specific container in multi-container pod
kubectl logs <pod-name> -c <container-name>
```

**3. Check Events:**
```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

**4. Check Resource Limits:**
```bash
kubectl describe pod <pod-name> | grep -A 5 "Limits"
```

**5. Check Liveness/Readiness Probes:**
```bash
kubectl describe pod <pod-name> | grep -A 10 "Liveness"
```

**Common Causes:**
- Application crashes on startup
- Missing environment variables
- Incorrect command/args
- Resource limits too low (OOMKilled)
- Failed liveness probe
- Missing dependencies
- Configuration errors

**Solutions:**
- Fix application code
- Adjust resource limits
- Fix probes
- Add init containers for dependencies
- Check configuration

---

### Q13: Explain the concept of Pod Disruption Budget (PDB).

**Answer:**
PDB ensures a minimum number of pods remain available during voluntary disruptions.

**Voluntary Disruptions:**
- Node drain for maintenance
- Cluster autoscaler scale down
- Deployment updates
- Manual pod deletion

**Involuntary Disruptions:**
- Hardware failure
- Kernel panic
- Network partition
- Out of resources

**Example:**
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: my-app
```

**Options:**
- `minAvailable`: Minimum pods that must be available
- `maxUnavailable`: Maximum pods that can be unavailable

**Use Cases:**
- Ensure high availability during updates
- Prevent too many pods from being disrupted
- Protect critical applications

---

### Q14: How does Kubernetes handle storage?

**Answer:**
Kubernetes provides several storage abstractions:

**1. Volumes:**
- Directory accessible to containers in a Pod
- Lifetime tied to Pod
- Types: emptyDir, hostPath, configMap, secret, etc.

**2. PersistentVolume (PV):**
- Cluster-level storage resource
- Provisioned by admin or dynamically
- Independent lifecycle from pods

**3. PersistentVolumeClaim (PVC):**
- Request for storage by user
- Binds to PV
- Used by pods

**4. StorageClass:**
- Defines storage types
- Enables dynamic provisioning
- Specifies provisioner and parameters

**Workflow:**
```
1. Admin creates StorageClass
2. User creates PVC
3. StorageClass dynamically provisions PV
4. PVC binds to PV
5. Pod uses PVC
```

**Access Modes:**
- ReadWriteOnce (RWO): Single node read-write
- ReadOnlyMany (ROX): Multiple nodes read-only
- ReadWriteMany (RWX): Multiple nodes read-write

**Reclaim Policies:**
- Retain: Keep data after PVC deletion
- Delete: Delete data after PVC deletion
- Recycle: Basic scrub (deprecated)

---

### Q15: What are Init Containers and when would you use them?

**Answer:**
Init containers run before app containers start. They run to completion sequentially.

**Characteristics:**
- Run before app containers
- Must complete successfully
- Run in order
- Share volumes with app containers
- Have separate images

**Use Cases:**

**1. Wait for Dependencies:**
```yaml
initContainers:
- name: wait-for-db
  image: busybox
  command: ['sh', '-c', 'until nc -z mysql 3306; do sleep 2; done']
```

**2. Setup Configuration:**
```yaml
initContainers:
- name: setup-config
  image: busybox
  command: ['sh', '-c', 'cp /config/* /app/config/']
  volumeMounts:
  - name: config
    mountPath: /app/config
```

**3. Clone Repository:**
```yaml
initContainers:
- name: git-clone
  image: alpine/git
  command: ['git', 'clone', 'https://github.com/user/repo.git', '/app']
  volumeMounts:
  - name: app
    mountPath: /app
```

**4. Database Migrations:**
```yaml
initContainers:
- name: migrate
  image: myapp:latest
  command: ['python', 'manage.py', 'migrate']
```

**Benefits:**
- Separation of concerns
- Reusable init logic
- Security (different images)
- Ordered execution

---

### Q16: Explain Kubernetes network policies.

**Answer:**
Network policies control traffic flow between pods at the IP address or port level.

**Default Behavior:**
- All pods can communicate with all pods
- All pods can reach all services
- No isolation by default

**Policy Types:**
- **Ingress**: Incoming traffic to pods
- **Egress**: Outgoing traffic from pods

**Example: Deny All Ingress:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

**Example: Allow Specific Traffic:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
```

**Selectors:**
- **podSelector**: Select pods by labels
- **namespaceSelector**: Select namespaces by labels
- **ipBlock**: Select IP ranges

**Best Practices:**
- Start with deny-all
- Allow specific traffic
- Use labels effectively
- Test policies
- Document policies

---

### Q17: How do you perform a rolling update in Kubernetes?

**Answer:**
Rolling updates gradually replace old pods with new ones.

**Deployment Strategy:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # Max pods above desired
      maxUnavailable: 0  # Max pods unavailable
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app
        image: myapp:v2.0
```

**Update Commands:**
```bash
# Update image
kubectl set image deployment/my-app app=myapp:v2.0

# Edit deployment
kubectl edit deployment my-app

# Apply new manifest
kubectl apply -f deployment.yaml
```

**Monitor Update:**
```bash
# Watch rollout status
kubectl rollout status deployment/my-app

# View rollout history
kubectl rollout history deployment/my-app

# View specific revision
kubectl rollout history deployment/my-app --revision=2
```

**Rollback:**
```bash
# Rollback to previous version
kubectl rollout undo deployment/my-app

# Rollback to specific revision
kubectl rollout undo deployment/my-app --to-revision=2
```

**Pause/Resume:**
```bash
# Pause rollout
kubectl rollout pause deployment/my-app

# Resume rollout
kubectl rollout resume deployment/my-app
```

**Update Process:**
1. Create new ReplicaSet with new version
2. Scale up new ReplicaSet (respecting maxSurge)
3. Scale down old ReplicaSet (respecting maxUnavailable)
4. Repeat until all pods updated
5. Keep old ReplicaSets for rollback

---

### Q18: What is the difference between liveness and readiness probes?

**Answer:**

| Aspect | Liveness Probe | Readiness Probe |
|--------|----------------|-----------------|
| Purpose | Is container alive? | Is container ready? |
| Failure Action | Restart container | Remove from service |
| When to Use | Detect deadlocks | Detect temporary unavailability |
| Impact | High (restart) | Low (traffic routing) |
| Example | App frozen | App warming up |

**Liveness Probe:**
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 30
  periodSeconds: 10
  timeoutSeconds: 5
  failureThreshold: 3
```

**Readiness Probe:**
```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
  timeoutSeconds: 3
  failureThreshold: 3
```

**Startup Probe (Kubernetes 1.16+):**
```yaml
startupProbe:
  httpGet:
    path: /startup
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
```

**Probe Types:**
- **HTTP GET**: Send HTTP request
- **TCP Socket**: Open TCP connection
- **Exec**: Execute command

**Best Practices:**
- Always use readiness probes
- Use liveness probes carefully (avoid restart loops)
- Set appropriate timeouts
- Different endpoints for liveness and readiness
- Use startup probes for slow-starting apps

---

### Q19: Explain the concept of Taints and Tolerations.

**Answer:**
Taints and tolerations work together to ensure pods are not scheduled onto inappropriate nodes.

**Taints:**
- Applied to nodes
- Repel pods without matching tolerations
- Three effects: NoSchedule, PreferNoSchedule, NoExecute

**Tolerations:**
- Applied to pods
- Allow (but don't require) pods to schedule on tainted nodes

**Example:**

**Taint a Node:**
```bash
# Add taint
kubectl taint nodes node1 key=value:NoSchedule

# Remove taint
kubectl taint nodes node1 key=value:NoSchedule-

# View taints
kubectl describe node node1 | grep Taints
```

**Pod with Toleration:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
  containers:
  - name: app
    image: myapp:latest
```

**Taint Effects:**

**1. NoSchedule:**
- Don't schedule new pods
- Existing pods remain

**2. PreferNoSchedule:**
- Try not to schedule
- Soft constraint

**3. NoExecute:**
- Don't schedule new pods
- Evict existing pods without toleration

**Use Cases:**
- Dedicated nodes for specific workloads
- Nodes with special hardware (GPU)
- Isolate problematic nodes
- Node maintenance

**Special Taints:**
```bash
# Node not ready
node.kubernetes.io/not-ready:NoExecute

# Node unreachable
node.kubernetes.io/unreachable:NoExecute

# Memory pressure
node.kubernetes.io/memory-pressure:NoSchedule

# Disk pressure
node.kubernetes.io/disk-pressure:NoSchedule
```

---

### Q20: How do you backup and restore a Kubernetes cluster?

**Answer:**

**What to Backup:**
1. **etcd**: Cluster state (most important)
2. **Persistent Volumes**: Application data
3. **Kubernetes Objects**: YAML manifests
4. **Certificates**: TLS certificates
5. **Configuration**: kubeconfig, secrets

**Backup etcd:**
```bash
# Snapshot etcd
ETCDCTL_API=3 etcdctl snapshot save snapshot.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key

# Verify snapshot
ETCDCTL_API=3 etcdctl snapshot status snapshot.db
```

**Restore etcd:**
```bash
# Stop API server
systemctl stop kube-apiserver

# Restore snapshot
ETCDCTL_API=3 etcdctl snapshot restore snapshot.db \
  --data-dir=/var/lib/etcd-restore

# Update etcd data directory
# Edit /etc/kubernetes/manifests/etcd.yaml
# Change --data-dir to /var/lib/etcd-restore

# Start API server
systemctl start kube-apiserver
```

**Backup Kubernetes Objects:**
```bash
# Export all resources
kubectl get all --all-namespaces -o yaml > all-resources.yaml

# Export specific resources
kubectl get deployments --all-namespaces -o yaml > deployments.yaml
kubectl get services --all-namespaces -o yaml > services.yaml
kubectl get configmaps --all-namespaces -o yaml > configmaps.yaml
kubectl get secrets --all-namespaces -o yaml > secrets.yaml
```

**Using Velero:**
```bash
# Install Velero
velero install \
  --provider aws \
  --bucket my-backup-bucket \
  --secret-file ./credentials-velero

# Create backup
velero backup create my-backup

# Restore backup
velero restore create --from-backup my-backup

# Schedule backups
velero schedule create daily-backup --schedule="0 2 * * *"
```

**Best Practices:**
- Automate backups (cron, Velero)
- Test restores regularly
- Store backups off-cluster
- Encrypt backups
- Document restore procedures
- Monitor backup success

---

## Scenario-Based Questions

### Q21: A pod is stuck in Pending state. How do you troubleshoot?

**Answer:**

**Step 1: Check Pod Status**
```bash
kubectl get pods
kubectl describe pod <pod-name>
```

**Common Causes:**

**1. Insufficient Resources:**
```bash
# Check node resources
kubectl top nodes
kubectl describe nodes

# Check resource requests
kubectl describe pod <pod-name> | grep -A 5 "Requests"
```

**Solution:**
- Reduce resource requests
- Add more nodes
- Enable cluster autoscaler

**2. PVC Not Bound:**
```bash
# Check PVC status
kubectl get pvc
kubectl describe pvc <pvc-name>
```

**Solution:**
- Create PV
- Fix StorageClass
- Check provisioner

**3. Node Selector/Affinity:**
```bash
# Check node labels
kubectl get nodes --show-labels

# Check pod spec
kubectl get pod <pod-name> -o yaml | grep -A 10 "nodeSelector\|affinity"
```

**Solution:**
- Fix node labels
- Adjust node selector
- Modify affinity rules

**4. Taints:**
```bash
# Check node taints
kubectl describe node <node-name> | grep Taints
```

**Solution:**
- Add toleration to pod
- Remove taint from node

**5. Image Pull Issues:**
```bash
# Check events
kubectl get events --field-selector involvedObject.name=<pod-name>
```

**Solution:**
- Fix image name
- Add image pull secret
- Check registry access

---

### Q22: How would you implement blue-green deployment in Kubernetes?

**Answer:**

**Strategy:**
1. Run two identical environments (blue and green)
2. Blue serves production traffic
3. Deploy new version to green
4. Test green environment
5. Switch traffic to green
6. Keep blue for rollback

**Implementation:**

**Step 1: Create Blue Deployment**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-blue
  labels:
    app: myapp
    version: blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: blue
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
      - name: app
        image: myapp:v1.0
```

**Step 2: Create Service (Points to Blue)**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
    version: blue
  ports:
  - port: 80
    targetPort: 8080
```

**Step 3: Create Green Deployment**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-green
  labels:
    app: myapp
    version: green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: green
  template:
    metadata:
      labels:
        app: myapp
        version: green
    spec:
      containers:
      - name: app
        image: myapp:v2.0
```

**Step 4: Test Green**
```bash
# Create test service for green
kubectl expose deployment app-green --name=myapp-test --port=80 --target-port=8080

# Test green
curl http://myapp-test
```

**Step 5: Switch Traffic to Green**
```bash
# Update service selector
kubectl patch service myapp -p '{"spec":{"selector":{"version":"green"}}}'
```

**Step 6: Rollback if Needed**
```bash
# Switch back to blue
kubectl patch service myapp -p '{"spec":{"selector":{"version":"blue"}}}'
```

**Advantages:**
- Zero downtime
- Easy rollback
- Full testing before switch

**Disadvantages:**
- Double resources
- Database migrations complex
- Stateful apps challenging

---

### Q23: How would you secure a Kubernetes cluster?

**Answer:**

**1. API Server Security:**
```bash
# Enable RBAC
--authorization-mode=RBAC

# Disable anonymous auth
--anonymous-auth=false

# Enable audit logging
--audit-log-path=/var/log/kubernetes/audit.log
--audit-policy-file=/etc/kubernetes/audit-policy.yaml

# Encrypt etcd
--encryption-provider-config=/etc/kubernetes/encryption-config.yaml
```

**2. Network Security:**
```yaml
# Default deny all
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

**3. Pod Security:**
```yaml
# Enforce restricted policy
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    pod-security.kubernetes.io/enforce: restricted
```

**4. RBAC:**
```yaml
# Least privilege principle
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

**5. Secrets Management:**
```bash
# Enable encryption at rest
# Use external secret managers (Vault)
# Rotate secrets regularly
```

**6. Image Security:**
```bash
# Scan images for vulnerabilities
trivy image myapp:latest

# Use private registries
# Sign images with Cosign
```

**7. Node Security:**
```bash
# Disable SSH
# Enable SELinux/AppArmor
# Regular security updates
# Minimal OS (Container-Optimized OS)
```

**8. Monitoring and Auditing:**
```bash
# Enable audit logging
# Monitor API server logs
# Set up alerts
# Regular security audits
```

**Security Checklist:**
- [ ] RBAC enabled
- [ ] Network policies in place
- [ ] Pod Security Standards enforced
- [ ] Secrets encrypted at rest
- [ ] Images scanned for vulnerabilities
- [ ] Audit logging enabled
- [ ] TLS everywhere
- [ ] Regular updates
- [ ] Monitoring and alerting
- [ ] Incident response plan

---

## Conclusion

Kubernetes is a powerful platform for managing containerized applications at scale. This guide covers the fundamental concepts, architecture, networking, storage, scaling, observability, and security.

**Key Takeaways:**
- Kubernetes automates deployment, scaling, and management
- Understand pods, deployments, services, and ingress
- Master YAML configuration
- Implement proper security (RBAC, Network Policies, Secrets)
- Use autoscaling for efficiency
- Monitor with Prometheus and Grafana
- Follow best practices for production deployments

**Next Steps:**
- Practice with a local cluster (Minikube, Kind, k3s)
- Deploy sample applications
- Implement CI/CD pipelines
- Explore service meshes (Istio, Linkerd)
- Learn Helm for package management
- Study GitOps (ArgoCD, Flux)
- Get certified (CKA, CKAD, CKS)

---

**Happy Learning! 🚀**

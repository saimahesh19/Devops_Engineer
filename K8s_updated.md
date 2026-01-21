# Kubernetes (K8s) Complete Guide

## Table of Contents
1. [Introduction](#introduction)
2. [Core Concepts](#core-concepts)
3. [Architecture](#architecture)
4. [Workload Resources](#workload-resources)
5. [Networking](#networking)
6. [Storage](#storage)
7. [Configuration Management](#configuration-management)
8. [Security and Access Control](#security-and-access-control)
9. [Monitoring and Logging](#monitoring-and-logging)
10. [High Availability and Disaster Recovery](#high-availability-and-disaster-recovery)
11. [Best Practices](#best-practices)
12. [Troubleshooting](#troubleshooting)

## Introduction

Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications.

### Key Features
- **Automated Rollouts and Rollbacks**: Deploy changes with zero downtime
- **Service Discovery and Load Balancing**: Automatic DNS and load balancing
- **Storage Orchestration**: Mount storage systems automatically
- **Self-Healing**: Restarts failed containers, replaces containers
- **Secret and Configuration Management**: Manage sensitive information
- **Horizontal Scaling**: Scale applications up/down automatically

## Core Concepts

### Pod
The smallest deployable unit in Kubernetes. A Pod represents a single instance of a running process and can contain one or more containers.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    ports:
    - containerPort: 80
```

### Node
A worker machine in Kubernetes (physical or virtual). Each node contains the services necessary to run Pods.

### Cluster
A set of nodes grouped together. Provides redundancy and high availability.

### Namespace
Virtual clusters within a physical cluster. Used for resource isolation and multi-tenancy.

```bash
kubectl create namespace development
kubectl get namespaces
```

## Architecture

### Control Plane Components

#### API Server (kube-apiserver)
- Front-end for the Kubernetes control plane
- Exposes the Kubernetes API
- Handles REST operations and validates them

#### etcd
- Consistent and highly-available key-value store
- Stores all cluster data
- Backing store for all cluster state

#### Scheduler (kube-scheduler)
- Watches for newly created Pods with no assigned node
- Selects a node for them to run on
- Considers resource requirements, constraints, affinity/anti-affinity

#### Controller Manager (kube-controller-manager)
- Runs controller processes
- Node Controller: Monitors node health
- Replication Controller: Maintains correct number of pods
- Endpoints Controller: Populates Endpoints object
- Service Account & Token Controllers: Create default accounts and API access tokens

#### Cloud Controller Manager
- Embeds cloud-specific control logic
- Links cluster into cloud provider's API

### Node Components

#### kubelet
- Agent that runs on each node
- Ensures containers are running in a Pod
- Takes PodSpecs and ensures described containers are running and healthy

#### kube-proxy
- Network proxy on each node
- Maintains network rules
- Enables communication to Pods from inside or outside the cluster

#### Container Runtime
- Software responsible for running containers
- Supports Docker, containerd, CRI-O, etc.

## Workload Resources

### Deployment
Manages stateless applications with declarative updates.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

### StatefulSet
Manages stateful applications with stable network identities and persistent storage.

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
        volumeMounts:
        - name: data
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```

### DaemonSet
Ensures all (or some) nodes run a copy of a Pod (e.g., log collectors, monitoring agents).

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
spec:
  selector:
    matchLabels:
      name: fluentd
  template:
    metadata:
      labels:
        name: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluentd:latest
```

### Job and CronJob
- **Job**: Creates one or more Pods and ensures a specified number complete successfully
- **CronJob**: Creates Jobs on a time-based schedule

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: backup-job
spec:
  schedule: "0 2 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: backup-tool:latest
            command: ["/bin/sh", "-c", "backup.sh"]
          restartPolicy: OnFailure
```

## Networking

### Service Types

#### ClusterIP (Default)
Exposes service on cluster-internal IP.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

#### NodePort
Exposes service on each Node's IP at a static port.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nodeport-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30080
```

#### LoadBalancer
Exposes service externally using cloud provider's load balancer.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-loadbalancer-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

#### ExternalName
Maps service to a DNS name.

### Ingress
Manages external access to services, typically HTTP/HTTPS.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /app
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80
```

### Network Policies
Control traffic flow between Pods.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
```

## Storage

### Volumes
Directory accessible to containers in a Pod.

Types:
- **emptyDir**: Temporary storage, deleted when Pod is removed
- **hostPath**: Mounts file/directory from host node
- **persistentVolumeClaim**: Mounts a PersistentVolume

### PersistentVolume (PV)
Piece of storage in the cluster provisioned by administrator or dynamically.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-example
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: standard
  hostPath:
    path: /mnt/data
```

### PersistentVolumeClaim (PVC)
Request for storage by a user.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-example
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: standard
```

### StorageClass
Provides a way to describe storage "classes" with different performance characteristics.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-storage
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp3
  iopsPerGB: "10"
```

## Configuration Management

### ConfigMap
Store non-confidential configuration data in key-value pairs.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database_url: "mysql://db.example.com:3306"
  log_level: "info"
  feature_flag: "true"
```

Using ConfigMap in Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:latest
    envFrom:
    - configMapRef:
        name: app-config
```

### Secret
Store sensitive information like passwords, tokens, keys.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=  # base64 encoded
  password: cGFzc3dvcmQxMjM=  # base64 encoded
```

Using Secret in Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:latest
    env:
    - name: DB_USERNAME
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: username
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: password
```

## Security and Access Control

### Role-Based Access Control (RBAC)

RBAC regulates access to Kubernetes resources based on roles assigned to users, groups, or service accounts.

#### Role and ClusterRole

**Role**: Grants permissions within a specific namespace

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: development
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

**ClusterRole**: Grants permissions cluster-wide

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```

#### RoleBinding and ClusterRoleBinding

**RoleBinding**: Grants permissions defined in a Role to users/groups within a namespace

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: development
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

**ClusterRoleBinding**: Grants permissions cluster-wide

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-secrets-global
subjects:
- kind: Group
  name: manager
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```

### Service Accounts

Service accounts provide an identity for processes running in Pods.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-service-account
  namespace: production
```

Using Service Account in Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: app-service-account
  containers:
  - name: app
    image: myapp:latest
```

### Pod Security Standards

#### Pod Security Policies (Deprecated in 1.25)

Replaced by Pod Security Admission.

#### Pod Security Admission

Three levels: Privileged, Baseline, Restricted

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: secure-namespace
  labels:
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/warn: restricted
```

### Security Context

Define privilege and access control settings for Pods/Containers.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
    seccompProfile:
      type: RuntimeDefault
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
```

### Network Policies for Security

Restrict network traffic between Pods.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-ingress
  namespace: production
spec:
  podSelector: {}
  policyTypes:
  - Ingress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-specific-ingress
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: frontend
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
```

### Secrets Management Best Practices

1. **Encrypt Secrets at Rest**: Enable encryption in etcd

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
          secret: <base64-encoded-secret>
    - identity: {}
```

2. **Use External Secrets Management**: Integrate with HashiCorp Vault, AWS Secrets Manager, Azure Key Vault

```yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: vault-secret
spec:
  secretStoreRef:
    name: vault-backend
    kind: SecretStore
  target:
    name: my-secret
  data:
  - secretKey: password
    remoteRef:
      key: secret/data/myapp
      property: password
```

3. **Limit Secret Access**: Use RBAC to restrict who can read secrets

### Image Security

1. **Use Private Registries**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: regcred
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: <base64-encoded-docker-config>
---
apiVersion: v1
kind: Pod
metadata:
  name: private-image-pod
spec:
  containers:
  - name: app
    image: private-registry.io/myapp:latest
  imagePullSecrets:
  - name: regcred
```

2. **Image Scanning**: Use tools like Trivy, Clair, or Anchore

3. **Image Pull Policies**:
   - `Always`: Always pull the image
   - `IfNotPresent`: Pull if not present locally
   - `Never`: Never pull, use local image

### Admission Controllers

Intercept requests to the API server before persistence.

Common admission controllers:
- **NamespaceLifecycle**: Prevents deletion of system namespaces
- **LimitRanger**: Enforces resource limits
- **ResourceQuota**: Enforces resource quotas
- **PodSecurityPolicy**: Controls security-sensitive aspects of pod specification
- **ValidatingAdmissionWebhook**: Calls external webhooks for validation
- **MutatingAdmissionWebhook**: Calls external webhooks for mutation

### Audit Logging

Track API server requests for security analysis.

```yaml
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
- level: Metadata
  resources:
  - group: ""
    resources: ["secrets", "configmaps"]
- level: RequestResponse
  resources:
  - group: ""
    resources: ["pods"]
```

### Certificate Management

Use cert-manager for automated certificate management:

```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: example-com
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

## Monitoring and Logging

### Monitoring with Prometheus

```yaml
apiVersion: v1
kind: ServiceMonitor
metadata:
  name: app-monitor
spec:
  selector:
    matchLabels:
      app: myapp
  endpoints:
  - port: metrics
    interval: 30s
```

### Logging with EFK Stack

- **Elasticsearch**: Store logs
- **Fluentd**: Collect and forward logs
- **Kibana**: Visualize logs

### Metrics Server

Provides resource usage metrics.

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
kubectl top nodes
kubectl top pods
```

### Health Checks

#### Liveness Probe
Determines if container is running.

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 3
```

#### Readiness Probe
Determines if container is ready to serve traffic.

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

#### Startup Probe
Determines if application has started.

```yaml
startupProbe:
  httpGet:
    path: /startup
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
```

## High Availability and Disaster Recovery

### Multi-Master Setup

- Run multiple API server instances
- Use load balancer for API server endpoints
- Ensure etcd cluster has odd number of nodes (3, 5, 7)

### Backup and Restore

#### Backup etcd

```bash
ETCDCTL_API=3 etcdctl snapshot save snapshot.db \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key
```

#### Restore etcd

```bash
ETCDCTL_API=3 etcdctl snapshot restore snapshot.db \
  --data-dir=/var/lib/etcd-restore
```

### Resource Quotas

Limit resource consumption per namespace.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: development
spec:
  hard:
    requests.cpu: "10"
    requests.memory: 20Gi
    limits.cpu: "20"
    limits.memory: 40Gi
    persistentvolumeclaims: "10"
```

### Limit Ranges

Set default resource limits for containers.

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-mem-limit-range
  namespace: development
spec:
  limits:
  - default:
      cpu: 500m
      memory: 512Mi
    defaultRequest:
      cpu: 250m
      memory: 256Mi
    type: Container
```

### Pod Disruption Budgets

Ensure minimum availability during voluntary disruptions.

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: app-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: myapp
```

## Best Practices

### Resource Management

1. **Always set resource requests and limits**

```yaml
resources:
  requests:
    memory: "256Mi"
    cpu: "250m"
  limits:
    memory: "512Mi"
    cpu: "500m"
```

2. **Use Horizontal Pod Autoscaler (HPA)**

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
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

3. **Use Vertical Pod Autoscaler (VPA)** for automatic resource adjustment

### Deployment Strategies

#### Rolling Update (Default)

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
```

#### Blue-Green Deployment

Maintain two identical environments, switch traffic between them.

#### Canary Deployment

Gradually roll out changes to a small subset of users.

### Labels and Annotations

Use meaningful labels for organization:

```yaml
metadata:
  labels:
    app: myapp
    environment: production
    version: v1.2.3
    tier: frontend
  annotations:
    description: "Main application frontend"
    owner: "team-a@example.com"
```

### Namespace Organization

- Separate environments (dev, staging, prod)
- Separate teams or projects
- Apply resource quotas per namespace

### Configuration Management

- Use ConfigMaps for non-sensitive configuration
- Use Secrets for sensitive data
- Version control your manifests
- Use Kustomize or Helm for templating

### Security Best Practices

1. Enable RBAC
2. Use Network Policies
3. Scan images for vulnerabilities
4. Run containers as non-root
5. Use read-only root filesystems
6. Drop unnecessary capabilities
7. Enable Pod Security Standards
8. Rotate secrets regularly
9. Enable audit logging
10. Use admission controllers

## Troubleshooting

### Common kubectl Commands

```bash
# Get resources
kubectl get pods
kubectl get pods -o wide
kubectl get pods --all-namespaces
kubectl get pods -l app=nginx

# Describe resources
kubectl describe pod <pod-name>
kubectl describe node <node-name>

# Logs
kubectl logs <pod-name>
kubectl logs <pod-name> -c <container-name>
kubectl logs <pod-name> --previous
kubectl logs -f <pod-name>  # Follow logs

# Execute commands in container
kubectl exec -it <pod-name> -- /bin/bash
kubectl exec <pod-name> -- ls /app

# Port forwarding
kubectl port-forward <pod-name> 8080:80

# Copy files
kubectl cp <pod-name>:/path/to/file ./local-file
kubectl cp ./local-file <pod-name>:/path/to/file

# Resource usage
kubectl top nodes
kubectl top pods

# Events
kubectl get events --sort-by=.metadata.creationTimestamp

# Debug
kubectl debug <pod-name> -it --image=busybox
```

### Common Issues

#### Pod Stuck in Pending
- Check node resources: `kubectl describe node`
- Check PVC status: `kubectl get pvc`
- Check scheduler logs

#### Pod Stuck in CrashLoopBackOff
- Check logs: `kubectl logs <pod-name>`
- Check previous logs: `kubectl logs <pod-name> --previous`
- Check resource limits
- Check liveness/readiness probes

#### ImagePullBackOff
- Verify image name and tag
- Check image pull secrets
- Verify registry access
- Check network connectivity

#### Service Not Accessible
- Verify service selector matches pod labels
- Check endpoints: `kubectl get endpoints <service-name>`
- Test with port-forward
- Check network policies

#### Node NotReady
- Check kubelet status: `systemctl status kubelet`
- Check kubelet logs: `journalctl -u kubelet`
- Check node resources
- Check network connectivity

### Debug Tools

```bash
# Create debug pod
kubectl run debug-pod --image=busybox --rm -it -- /bin/sh

# Create debug pod with network tools
kubectl run debug-pod --image=nicolaka/netshoot --rm -it -- /bin/bash

# Ephemeral debug container (K8s 1.23+)
kubectl debug <pod-name> -it --image=busybox --target=<container-name>
```

### Performance Tuning

1. **Optimize resource requests/limits**
2. **Use HPA for auto-scaling**
3. **Enable cluster autoscaler**
4. **Use node affinity/anti-affinity**
5. **Optimize container images** (multi-stage builds, minimal base images)
6. **Use persistent volumes for stateful apps**
7. **Enable horizontal pod autoscaling**
8. **Monitor and analyze metrics**

---

## Additional Resources

- [Official Kubernetes Documentation](https://kubernetes.io/docs/)
- [Kubernetes GitHub Repository](https://github.com/kubernetes/kubernetes)
- [CNCF Kubernetes Certification](https://www.cncf.io/certification/cka/)
- [Kubernetes Patterns Book](https://k8spatterns.io/)
- [Production Best Practices](https://learnk8s.io/production-best-practices)

## Useful Tools

- **kubectl**: Official CLI tool
- **k9s**: Terminal UI for Kubernetes
- **Lens**: Kubernetes IDE
- **Helm**: Package manager for Kubernetes
- **Kustomize**: Template-free customization
- **Skaffold**: Development workflow automation
- **Telepresence**: Local development with remote cluster
- **Stern**: Multi-pod log tailing
- **kubectx/kubens**: Context and namespace switching
- **Velero**: Backup and restore tool

---

**Last Updated**: 2024
**Version**: Kubernetes 1.28+

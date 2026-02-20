# ☸️ Kubernetes (kubectl) Cheat Sheet

> A quick-reference guide to the most useful `kubectl` commands for everyday infrastructure & observability work.

---

## 🔀 Context & Cluster

```bash
kubectl config get-contexts                # List all contexts
kubectl config current-context             # Show active context
kubectl config use-context <ctx>           # Switch context
kubectl config set-context --current \
  --namespace=<ns>                         # Set default namespace
kubectl cluster-info                       # Cluster endpoint info
```

---

## 📦 Namespaces

```bash
kubectl get ns                             # List namespaces
kubectl create ns <ns>                     # Create namespace
kubectl delete ns <ns>                     # Delete namespace
```

---

## 🖥️ Nodes

```bash
kubectl get nodes -o wide                  # List nodes (detailed)
kubectl describe node <node>               # Inspect a node
kubectl top node                           # Node resource usage
kubectl cordon <node>                      # Mark node unschedulable
kubectl uncordon <node>                    # Mark node schedulable
kubectl drain <node> --ignore-daemonsets   # Safely evict pods from node
```

---

## 🐳 Pods

```bash
kubectl get pods                           # Pods in current namespace
kubectl get pods -A                        # Pods across ALL namespaces
kubectl get pods -o wide                   # Pods with node & IP info
kubectl describe pod <pod>                 # Full pod details
kubectl delete pod <pod>                   # Delete a pod
kubectl run debug --image=busybox -it \
  --rm -- /bin/sh                          # Quick throwaway pod
```

### Logs

```bash
kubectl logs <pod>                         # Pod logs
kubectl logs <pod> -c <container>          # Specific container logs
kubectl logs -f <pod>                      # Stream logs (follow)
kubectl logs <pod> --previous              # Logs from crashed container
kubectl logs -l app=myapp                  # Logs by label selector
```

### Exec & Debug

```bash
kubectl exec -it <pod> -- /bin/bash        # Shell into a pod
kubectl exec <pod> -- cat /etc/config      # Run a single command
kubectl debug pod/<pod> -it \
  --image=busybox                          # Ephemeral debug container
kubectl cp <pod>:/path/file ./local-file   # Copy file from pod
kubectl cp ./local-file <pod>:/path/file   # Copy file to pod
```

---

## 🚀 Deployments / StatefulSets / DaemonSets

```bash
kubectl get deployments                    # List deployments
kubectl get statefulsets                   # List statefulsets
kubectl get daemonsets                     # List daemonsets
kubectl describe deployment <dep>         # Inspect deployment
kubectl scale deployment <dep> \
  --replicas=3                             # Scale replicas
kubectl rollout status deployment <dep>   # Watch rollout progress
kubectl rollout history deployment <dep>  # View revision history
kubectl rollout undo deployment <dep>     # Rollback to previous
kubectl rollout restart deployment <dep>  # Rolling restart
```

---

## 🌐 Services

```bash
kubectl get svc                            # List services
kubectl get svc -A                         # Services across all ns
kubectl describe svc <svc>                 # Inspect a service
kubectl get svc <svc> -o jsonpath=\
  '{.status.loadBalancer.ingress[0].hostname}'  # External IP/DNS
kubectl port-forward svc/<svc> 8080:80     # Local port forward
```

---

## 🔗 Ingress

```bash
kubectl get ingress -n <ns>                # List ingresses
kubectl describe ingress <name> -n <ns>    # Inspect ingress
```

---

## 🔐 ConfigMaps & Secrets

```bash
# ConfigMaps
kubectl get configmap                      # List ConfigMaps
kubectl describe configmap <cm>            # Inspect ConfigMap
kubectl create configmap <cm> \
  --from-file=config.yaml                  # Create from file

# Secrets
kubectl get secrets                        # List secrets
kubectl describe secret <secret>           # Inspect (metadata only)
kubectl get secret <secret> -o jsonpath=\
  '{.data.<key>}' | base64 --decode        # Decode a secret value
kubectl create secret generic <secret> \
  --from-literal=password=s3cr3t           # Create from literal
```

---

## 💾 Persistent Volumes & Claims

```bash
kubectl get pv                             # List PersistentVolumes
kubectl get pvc                            # List PersistentVolumeClaims
kubectl describe pv <pv>                   # Inspect PV
kubectl describe pvc <pvc>                 # Inspect PVC
```

---

## 📊 Resource Usage & Metrics

```bash
kubectl top node                           # Node CPU/memory
kubectl top pod                            # Pod CPU/memory
kubectl top pod -n <ns> --sort-by=memory   # Sort by memory usage
kubectl top pod -n <ns> --sort-by=cpu      # Sort by CPU usage
```

---

## 📝 Apply, Delete & Edit

```bash
kubectl apply -f <file.yaml>               # Create/update resource
kubectl apply -f ./manifests/              # Apply entire directory
kubectl delete -f <file.yaml>              # Delete from manifest
kubectl edit deployment/<dep>              # Edit live resource
kubectl diff -f <file.yaml>               # Preview changes
kubectl replace --force -f <file.yaml>    # Force replace resource
```

---

## 🏷️ Labels & Selectors

```bash
kubectl label pod <pod> env=dev            # Add label
kubectl label pod <pod> env-               # Remove label
kubectl annotate pod <pod> owner=teamA     # Add annotation
kubectl get pods -l env=dev                # Filter by label
kubectl get pods -l 'env in (dev,staging)' # Multi-value selector
kubectl get all -l app=myapp               # All resources by label
```

---

## 🔍 Troubleshooting

```bash
kubectl get events -n <ns> \
  --sort-by='.lastTimestamp'               # Recent events (sorted)
kubectl describe pod <pod>                 # Check pod conditions
kubectl get pod <pod> -o yaml              # Full YAML for inspection
kubectl get componentstatuses              # Control plane health
kubectl auth can-i create pods             # Check RBAC permissions
kubectl auth can-i '*' '*'                 # Am I cluster-admin?
```

---

## 📤 Export & Output

```bash
kubectl get deployment <dep> -o yaml       # Export as YAML
kubectl get all -n <ns> -o yaml            # All resources as YAML
kubectl get pods -o json                   # Output as JSON
kubectl get pods -o name                   # Just resource names
kubectl get pods --no-headers              # Skip header row
kubectl get pods -o custom-columns=\
  'NAME:.metadata.name,STATUS:.status.phase'  # Custom columns
```

---

## 👀 Watch & Monitor

```bash
kubectl get pods -w                        # Watch pods live
kubectl get svc -w                         # Watch services live
kubectl get events -w -n <ns>              # Watch events stream
kubectl get all -n <ns>                    # Quick namespace overview
```

---

## ⚡ Handy One-Liners

```bash
# Delete all evicted pods in a namespace
kubectl get pods -n <ns> --field-selector=status.phase=Failed \
  -o name | xargs kubectl delete -n <ns>

# Restart all pods in a deployment (rolling)
kubectl rollout restart deployment/<dep>

# Get images used by all pods
kubectl get pods -A -o jsonpath=\
  '{range .items[*]}{.metadata.name}{"\t"}{range .spec.containers[*]}{.image}{"\n"}{end}{end}'

# Find pods not in Running state
kubectl get pods -A --field-selector=status.phase!=Running

# Get all resource types available
kubectl api-resources --verbs=list -o name
```

---

## 🧰 Useful Aliases (add to `~/.bashrc` or `~/.zshrc`)

```bash
alias k='kubectl'
alias kgp='kubectl get pods'
alias kgpa='kubectl get pods -A'
alias kgs='kubectl get svc'
alias kgd='kubectl get deployments'
alias kgn='kubectl get nodes -o wide'
alias kd='kubectl describe'
alias kl='kubectl logs'
alias klf='kubectl logs -f'
alias kex='kubectl exec -it'
alias kaf='kubectl apply -f'
alias kdf='kubectl delete -f'
alias kctx='kubectl config use-context'
alias kns='kubectl config set-context --current --namespace'
```

---

> **💡 Tip:** Install [`kubectx` + `kubens`](https://github.com/ahmetb/kubectx) for even faster context and namespace switching!

---

*Happy Kuberneting!* 🚀

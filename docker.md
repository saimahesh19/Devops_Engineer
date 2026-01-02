# Docker Complete Guide: Beginner to Professional 🐳

> **📚 Complete Docker Mastery Guide**
> From fundamentals to advanced concepts, container orchestration, and production best practices.
> Perfect for interviews, hands-on learning, and real-world applications.

---

## Table of Contents

1. [Docker Fundamentals](#1-docker-fundamentals)
2. [Docker Architecture Deep Dive](#2-docker-architecture-deep-dive)
3. [Docker Images](#3-docker-images)
4. [Docker Containers](#4-docker-containers)
5. [Dockerfile Mastery](#5-dockerfile-mastery)
6. [Docker Volumes & Data Persistence](#6-docker-volumes--data-persistence)
7. [Docker Networking](#7-docker-networking)
8. [Docker Compose](#8-docker-compose)
9. [Docker Registry & Image Management](#9-docker-registry--image-management)
10. [Docker Security](#10-docker-security)
11. [Docker Performance & Optimization](#11-docker-performance--optimization)
12. [Docker in Production](#12-docker-in-production)
13. [Docker vs Virtual Machines](#13-docker-vs-virtual-machines)
14. [Docker Troubleshooting](#14-docker-troubleshooting)
15. [Interview Questions & Answers](#15-interview-questions--answers)

---

## 1️⃣ Docker Fundamentals

### What is Docker? 🤔

**Docker** is a platform for developing, shipping, and running applications in **containers**.

**Think of it as:** Shipping containers for software
- Just like shipping containers standardize cargo transport, Docker containers standardize software deployment
- Your app runs the same way everywhere: laptop, server, cloud

---

### Why Docker? The Problem It Solves

**Before Docker:**

```
Developer's Machine:
- Python 3.8
- Node.js 14
- MySQL 5.7
- Works perfectly! ✅

Production Server:
- Python 3.6
- Node.js 12
- MySQL 8.0
- Doesn't work! ❌ "But it works on my machine!"
```

**With Docker:**

```
Developer's Machine:
- Docker container with Python 3.8, Node.js 14, MySQL 5.7
- Works perfectly! ✅

Production Server:
- Same Docker container
- Works perfectly! ✅ "It works everywhere!"
```

---

### Key Benefits of Docker

**1. Consistency:**
- Same environment everywhere (dev, test, prod)
- No more "works on my machine" problems

**2. Isolation:**
- Each container is isolated
- Dependencies don't conflict
- Multiple versions of same software can coexist

**3. Portability:**
- Run anywhere: laptop, server, cloud (AWS, Azure, GCP)
- Easy migration between environments

**4. Efficiency:**
- Lightweight (shares host OS kernel)
- Fast startup (seconds vs minutes for VMs)
- Better resource utilization

**5. Scalability:**
- Easy to scale up/down
- Spin up multiple containers quickly

**6. Version Control:**
- Images are versioned
- Easy rollback to previous versions

---

### Docker vs Virtual Machines 🔥

**Critical for Interviews!**

| Feature | Docker Container | Virtual Machine |
|---------|-----------------|-----------------|
| **OS** | Shares host OS kernel | Full OS per VM |
| **Size** | MBs (10-100 MB) | GBs (1-10 GB) |
| **Startup** | Seconds | Minutes |
| **Performance** | Near-native | Overhead from hypervisor |
| **Isolation** | Process-level | Hardware-level |
| **Resource Usage** | Lightweight | Heavy |
| **Portability** | High | Medium |

**Visual Comparison:**

```
Virtual Machines:
┌─────────────────────────────────────┐
│         Host Operating System        │
├─────────────────────────────────────┤
│           Hypervisor (VMware)        │
├──────────┬──────────┬───────────────┤
│  VM 1    │  VM 2    │    VM 3       │
│ ┌──────┐ │ ┌──────┐ │  ┌──────┐     │
│ │Guest │ │ │Guest │ │  │Guest │     │
│ │  OS  │ │ │  OS  │ │  │  OS  │     │
│ └──────┘ │ └──────┘ │  └──────┘     │
│  App A   │  App B   │   App C       │
└──────────┴──────────┴───────────────┘

Docker Containers:
┌─────────────────────────────────────┐
│         Host Operating System        │
├─────────────────────────────────────┤
│           Docker Engine              │
├──────────┬──────────┬───────────────┤
│Container1│Container2│  Container3   │
│  App A   │  App B   │   App C       │
│  Libs    │  Libs    │   Libs        │
└──────────┴──────────┴───────────────┘
```

**Key Insight:** Containers share the host OS kernel, VMs each have their own OS.

---

### Core Docker Concepts

**1. Image:**
- Blueprint/template for containers
- Immutable (read-only)
- Contains OS libraries, dependencies, app code
- Like a class in OOP

**2. Container:**
- Running instance of an image
- Mutable (has writable layer)
- Isolated environment
- Like an object in OOP

**3. Dockerfile:**
- Text file with instructions to build an image
- Recipe for creating images

**4. Docker Hub:**
- Public registry for Docker images
- Like GitHub for Docker images

**5. Docker Engine:**
- Core software that runs containers
- Client-server architecture

---

### Docker Installation & Setup

**Check Docker Installation:**
```bash
docker --version
# Output: Docker version 24.0.7, build afdd53b

docker info
# Shows detailed information about Docker installation
```

**Test Docker:**
```bash
docker run hello-world
```

**What happens:**
1. Docker client contacts Docker daemon
2. Daemon pulls "hello-world" image from Docker Hub
3. Daemon creates container from image
4. Container runs and prints message
5. Container exits

---

## 2️⃣ Docker Architecture Deep Dive

### Docker Architecture Overview 🔥🔥

**Critical for Interviews!**

```
┌─────────────────────────────────────────────────────────┐
│                    Docker Client (CLI)                   │
│                  docker run, docker build                │
└────────────────────────┬────────────────────────────────┘
                         │ REST API
                         ▼
┌─────────────────────────────────────────────────────────┐
│                    Docker Daemon (dockerd)               │
│  - Manages containers, images, networks, volumes        │
│  - Listens for Docker API requests                      │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│                      containerd                          │
│  - Container lifecycle management                       │
│  - Image management                                     │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│                         runc                             │
│  - Low-level container runtime                          │
│  - Creates and runs containers                          │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              Linux Kernel (namespaces + cgroups)         │
│  - Process isolation (namespaces)                       │
│  - Resource limits (cgroups)                            │
└─────────────────────────────────────────────────────────┘
```

---

### Component Breakdown

**1. Docker Client:**
- Command-line interface (CLI)
- User interacts with Docker through client
- Sends commands to Docker daemon via REST API
- Can connect to remote daemons

**Commands:**
```bash
docker run    # Run container
docker build  # Build image
docker pull   # Pull image from registry
docker push   # Push image to registry
```

---

**2. Docker Daemon (dockerd):**
- Background service running on host
- Manages Docker objects (containers, images, networks, volumes)
- Listens for API requests
- Can communicate with other daemons

**Responsibilities:**
- Build images
- Run containers
- Distribute images
- Manage storage
- Manage networking

---

**3. containerd:**
- Industry-standard container runtime
- Manages container lifecycle (start, stop, pause, delete)
- Manages images (pull, push)
- Supervises runc

**Why separate from Docker daemon?**
- Modularity (can be used without Docker)
- Stability (daemon can restart without affecting containers)
- Standardization (OCI-compliant)

---

**4. runc:**
- Low-level container runtime
- Creates and runs containers according to OCI specification
- Spawns container processes
- Sets up namespaces and cgroups

**What it does:**
- Creates isolated environment (namespaces)
- Applies resource limits (cgroups)
- Mounts filesystems
- Starts container process

---

**5. Linux Kernel Features:**

**Namespaces (Isolation):**
- **PID:** Process isolation (container sees only its processes)
- **NET:** Network isolation (own network stack)
- **MNT:** Filesystem isolation (own filesystem view)
- **UTS:** Hostname isolation (own hostname)
- **IPC:** Inter-process communication isolation
- **USER:** User ID isolation

**cgroups (Resource Limits):**
- **CPU:** Limit CPU usage
- **Memory:** Limit memory usage
- **Disk I/O:** Limit disk operations
- **Network:** Limit network bandwidth

---

### Docker Execution Flow 🔥

**What happens when you run `docker run nginx`?**

```
Step 1: Docker Client
├─ User types: docker run nginx
└─ Client sends request to Docker daemon

Step 2: Docker Daemon
├─ Checks if nginx image exists locally
├─ If not, pulls from Docker Hub
└─ Creates container from image

Step 3: containerd
├─ Receives request from daemon
├─ Prepares container environment
└─ Calls runc

Step 4: runc
├─ Creates namespaces (PID, NET, MNT, etc.)
├─ Sets up cgroups (CPU, memory limits)
├─ Mounts container filesystem
└─ Starts container process (nginx)

Step 5: Container Running
├─ Nginx process running in isolated environment
├─ Has own PID namespace (sees only its processes)
├─ Has own network interface
└─ Resource limits applied
```

---

### Docker Container Lifecycle 🔥

**Critical for Interviews!**

```
┌─────────┐
│ Created │ ◄─── docker create
└────┬────┘
     │
     ▼
┌─────────┐
│ Running │ ◄─── docker start / docker run
└────┬────┘
     │
     ├──► docker pause ──► ┌────────┐
     │                      │ Paused │
     │                      └───┬────┘
     │                          │
     │ ◄────── docker unpause ──┘
     │
     ├──► docker stop ───► ┌─────────┐
     │                      │ Stopped │
     │                      └───┬─────┘
     │                          │
     │ ◄────── docker start ────┘
     │
     ├──► docker kill ───► ┌─────────┐
     │                      │ Killed  │
     │                      └───┬─────┘
     │                          │
     ▼                          ▼
┌─────────┐              ┌─────────┐
│ Exited  │              │ Dead    │
└────┬────┘              └─────────┘
     │
     ▼
docker rm ──► ┌─────────┐
              │ Removed │
              └─────────┘
```

**Lifecycle Commands:**

| Command | Description | Signal |
|---------|-------------|--------|
| `docker create` | Create container without starting | - |
| `docker start` | Start stopped container | - |
| `docker run` | Create + start in one command | - |
| `docker pause` | Suspend all processes | SIGSTOP |
| `docker unpause` | Resume paused container | SIGCONT |
| `docker stop` | Gracefully stop (10s timeout) | SIGTERM |
| `docker kill` | Force stop immediately | SIGKILL |
| `docker restart` | Stop + start | SIGTERM + start |
| `docker rm` | Remove stopped container | - |

---

### Docker Terminology Quick Reference

| Term | Definition | Example |
|------|------------|---------|
| **Image** | Immutable template | `nginx:latest` |
| **Container** | Running instance of image | `nginx-web-1` |
| **Dockerfile** | Build instructions | `FROM nginx` |
| **Layer** | Read-only filesystem change | Each Dockerfile instruction |
| **Tag** | Image version identifier | `nginx:1.21` |
| **Repository** | Collection of related images | `nginx` |
| **Registry** | Storage for repositories | Docker Hub |
| **Volume** | Persistent data storage | `/var/lib/mysql` |
| **Network** | Communication layer | `bridge`, `host` |
| **Daemon** | Background service | `dockerd` |

---

## 3️⃣ Docker Images

### What is a Docker Image? 🔥

**Image = Layered Filesystem + Metadata**

**Components:**
1. **Base OS layer** (Ubuntu, Alpine, etc.)
2. **Application files/code**
3. **Dependencies** (libraries, packages)
4. **Environment variables**
5. **Startup command** (ENTRYPOINT/CMD)

**Key Characteristics:**
- **Immutable:** Cannot be changed once created
- **Layered:** Built from multiple read-only layers
- **Shareable:** Can be pushed to registries
- **Versioned:** Tagged with version identifiers

---

### Image Layers 🔥🔥

**Critical Concept!**

**How Layers Work:**

```
Image: myapp:1.0
├── Layer 5: CMD ["python", "app.py"]      ◄─ Top layer
├── Layer 4: RUN pip install -r requirements.txt
├── Layer 3: COPY . /app
├── Layer 2: WORKDIR /app
└── Layer 1: FROM python:3.11              ◄─ Base layer

Each layer is read-only and cached!
```

**Why Layers Matter:**

**1. Caching:**
- If layer hasn't changed, Docker reuses cached version
- Speeds up builds dramatically

**Example:**
```dockerfile
FROM python:3.11           # Layer 1 (cached if exists)
WORKDIR /app               # Layer 2 (cached if Layer 1 unchanged)
COPY requirements.txt .    # Layer 3 (cached if file unchanged)
RUN pip install -r requirements.txt  # Layer 4 (cached if Layer 3 unchanged)
COPY . .                   # Layer 5 (rebuilt if code changed)
CMD ["python", "app.py"]   # Layer 6 (rebuilt if Layer 5 changed)
```

**Best Practice:** Put frequently changing instructions at the bottom!

**2. Storage Efficiency:**
- Layers are shared between images
- If 10 images use `python:3.11`, it's stored only once

**3. Faster Distribution:**
- Only changed layers are transferred
- Reduces network bandwidth

---

### Image Naming Convention 🔥

**Format:** `[registry/][namespace/]repository[:tag]`

**Examples:**

```
nginx                          # Official image, latest tag
nginx:1.21                     # Specific version
nginx:1.21-alpine             # Specific version + variant
ubuntu:22.04                   # Ubuntu version 22.04
myusername/myapp:v1.0         # User's image on Docker Hub
localhost:5000/myapp:latest   # Private registry
gcr.io/project/myapp:v2       # Google Container Registry
```

**Components:**

| Component | Description | Example |
|-----------|-------------|---------|
| **Registry** | Where image is stored | `docker.io` (default), `gcr.io` |
| **Namespace** | User/organization | `myusername`, `library` (official) |
| **Repository** | Image name | `nginx`, `myapp` |
| **Tag** | Version identifier | `latest`, `1.21`, `v1.0` |

**Special Tags:**
- `latest`: Most recent version (default if no tag specified)
- `stable`: Stable release
- `alpine`: Lightweight variant (based on Alpine Linux)
- `slim`: Smaller variant (fewer packages)

---

### Working with Images

**1. Pull Image from Registry:**
```bash
# Pull latest version
docker pull nginx

# Pull specific version
docker pull nginx:1.21

# Pull from specific registry
docker pull gcr.io/project/myapp:v1
```

---

**2. List Images:**
```bash
# List all images
docker images

# Output:
# REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
# nginx        latest    605c77e624dd   2 weeks ago    141MB
# python       3.11      a5d7930b60cc   3 weeks ago    917MB

# List with filters
docker images nginx              # Only nginx images
docker images --filter "dangling=true"  # Untagged images
```

---

**3. Inspect Image:**
```bash
# View detailed information
docker inspect nginx:latest

# View layers
docker history nginx:latest

# Output shows each layer and its size
```

---

**4. Tag Image:**
```bash
# Create new tag for existing image
docker tag nginx:latest myregistry/nginx:v1.0

# Now same image has two tags
docker images
# REPOSITORY           TAG       IMAGE ID
# nginx                latest    605c77e624dd
# myregistry/nginx     v1.0      605c77e624dd  # Same ID!
```

---

**5. Remove Image:**
```bash
# Remove by name:tag
docker rmi nginx:latest

# Remove by image ID
docker rmi 605c77e624dd

# Remove all unused images
docker image prune

# Remove all images (careful!)
docker rmi $(docker images -q)
```

---

**6. Save and Load Images:**
```bash
# Save image to tar file (for offline transfer)
docker save nginx:latest > nginx.tar
docker save -o nginx.tar nginx:latest

# Load image from tar file
docker load < nginx.tar
docker load -i nginx.tar

# Export container filesystem (not full image)
docker export container_id > container.tar

# Import filesystem as image
docker import container.tar myimage:tag
```

**Difference:**
- `save/load`: Preserves layers and metadata (full image)
- `export/import`: Flattens to single layer (filesystem only)

---

### Image Layers in Detail 🔥

**View Image Layers:**
```bash
docker history nginx:latest
```

**Output:**
```
IMAGE          CREATED       CREATED BY                                      SIZE
605c77e624dd   2 weeks ago   /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B
<missing>      2 weeks ago   /bin/sh -c #(nop)  EXPOSE 80                    0B
<missing>      2 weeks ago   /bin/sh -c #(nop) COPY file:abc123 in /etc/n…   1.2kB
<missing>      2 weeks ago   /bin/sh -c apt-get update && apt-get install…   54MB
<missing>      2 weeks ago   /bin/sh -c #(nop)  ENV NGINX_VERSION=1.21.6     0B
<missing>      3 weeks ago   /bin/sh -c #(nop)  CMD ["bash"]                 0B
<missing>      3 weeks ago   /bin/sh -c #(nop) ADD file:xyz789 in /          77MB
```

**Key Observations:**
1. Each Dockerfile instruction creates a layer
2. Layers are stacked from bottom to top
3. Some layers add size (RUN, COPY, ADD)
4. Some layers add no size (CMD, ENV, EXPOSE)
5. `<missing>` means layer is from base image

---

### Image Storage Location

**Where are images stored?**

**Linux:**
```
/var/lib/docker/
├── image/          # Image metadata
├── overlay2/       # Layer data (default storage driver)
├── containers/     # Container metadata
└── volumes/        # Volume data
```

**Storage Drivers:**
- **overlay2:** Default, best performance (modern Linux)
- **aufs:** Older, deprecated
- **devicemapper:** Block-level, complex
- **btrfs:** Copy-on-write filesystem
- **zfs:** Advanced filesystem features

**Check storage driver:**
```bash
docker info | grep "Storage Driver"
# Output: Storage Driver: overlay2
```

---

### Image Best Practices 🔥

**1. Use Official Images:**
```dockerfile
# ✅ Good: Official image
FROM python:3.11

# ❌ Bad: Random user's image
FROM randomuser/python
```

**2. Use Specific Tags:**
```dockerfile
# ✅ Good: Specific version
FROM python:3.11.5-slim

# ❌ Bad: Latest tag (unpredictable)
FROM python:latest
```

**3. Use Lightweight Base Images:**
```dockerfile
# ✅ Best: Alpine (5-10 MB)
FROM python:3.11-alpine

# ✅ Good: Slim (50-100 MB)
FROM python:3.11-slim

# ❌ Heavy: Full image (500-1000 MB)
FROM python:3.11
```

**Size Comparison:**
| Base Image | Size |
|------------|------|
| `python:3.11` | 917 MB |
| `python:3.11-slim` | 125 MB |
| `python:3.11-alpine` | 47 MB |

**4. Minimize Layers:**
```dockerfile
# ❌ Bad: Multiple layers
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y git
RUN apt-get clean

# ✅ Good: Single layer
RUN apt-get update && \
    apt-get install -y curl git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

**5. Order Instructions by Change Frequency:**
```dockerfile
# ✅ Good: Least changing at top
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .          # Changes less often
RUN pip install -r requirements.txt
COPY . .                         # Changes most often
CMD ["python", "app.py"]
```

---

## 4️⃣ Docker Containers

### What is a Container? 🔥

**Container = Image + Writable Layer + Runtime Configuration**

**Think of it as:**
- **Image** = Class (blueprint)
- **Container** = Object (instance)

**Container Components:**
1. **Image layers** (read-only)
2. **Container layer** (writable, thin)
3. **Process** (running application)
4. **Network interface** (IP, ports)
5. **Volumes** (persistent data)

---

### Container Lifecycle in Detail 🔥🔥

**1. Created State:**
```bash
# Create container without starting
docker create --name myapp nginx

# Container exists but not running
docker ps -a
# STATUS: Created
```

**What happens:**
- Container filesystem prepared
- Network configured
- No process running yet

---

**2. Running State:**
```bash
# Start existing container
docker start myapp

# Or create + start in one command
docker run --name myapp nginx

# Check running containers
docker ps
# STATUS: Up 2 minutes
```

**What happens:**
- Container process starts
- Network activated
- Application begins execution

---

**3. Paused State:**
```bash
# Suspend all processes
docker pause myapp

# Check status
docker ps
# STATUS: Up 5 minutes (Paused)

# Resume
docker unpause myapp
```

**What happens:**
- Processes frozen (SIGSTOP)
- Memory state preserved
- No CPU usage
- Network still active

**Use case:** Temporarily free CPU without stopping

---

**4. Stopped State:**
```bash
# Graceful stop (SIGTERM, 10s timeout, then SIGKILL)
docker stop myapp

# Check status
docker ps -a
# STATUS: Exited (0) 10 seconds ago

# Restart
docker start myapp
```

**What happens:**
- SIGTERM sent to main process
- Process has 10 seconds to cleanup
- If still running, SIGKILL sent
- Container filesystem preserved

---

**5. Killed State:**
```bash
# Force stop immediately (SIGKILL)
docker kill myapp

# Check status
docker ps -a
# STATUS: Exited (137) 1 second ago
```

**What happens:**
- SIGKILL sent immediately
- No cleanup time
- Process terminated forcefully

**Exit codes:**
- 0: Normal exit
- 137: SIGKILL (128 + 9)
- 143: SIGTERM (128 + 15)

---

**6. Removed State:**
```bash
# Remove stopped container
docker rm myapp

# Force remove running container
docker rm -f myapp

# Remove all stopped containers
docker container prune
```

**What happens:**
- Container filesystem deleted
- Network configuration removed
- Volumes preserved (unless -v flag)

---

### Working with Containers 🔥

**1. Run Container (Most Common):**

**Basic:**
```bash
# Run nginx in foreground
docker run nginx

# Run nginx in background (detached)
docker run -d nginx

# Run with name
docker run -d --name my-nginx nginx

# Run with port mapping
docker run -d -p 8080:80 nginx
# Host port 8080 → Container port 80

# Run with environment variables
docker run -d -e MYSQL_ROOT_PASSWORD=secret mysql

# Run with volume
docker run -d -v mydata:/var/lib/mysql mysql

# Run with network
docker run -d --network mynet nginx

# Run with resource limits
docker run -d --memory="512m" --cpus="1.0" nginx

# Run with restart policy
docker run -d --restart=always nginx
```

**Advanced:**
```bash
# Run with all options
docker run -d \
  --name my-app \
  -p 8080:80 \
  -e APP_ENV=production \
  -v /host/data:/app/data \
  --network app-net \
  --memory="1g" \
  --cpus="2.0" \
  --restart=unless-stopped \
  myapp:v1.0

# Run with interactive terminal
docker run -it ubuntu bash
# -i: Keep STDIN open
# -t: Allocate pseudo-TTY

# Run with automatic removal
docker run --rm nginx
# Container removed after exit

# Run with user
docker run --user 1000:1000 nginx
# Run as specific user:group

# Run with working directory
docker run -w /app nginx

# Run with hostname
docker run --hostname myapp nginx

# Run with DNS
docker run --dns 8.8.8.8 nginx

# Run with extra hosts
docker run --add-host myhost:192.168.1.100 nginx
```

---

**2. List Containers:**
```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# List with custom format
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"

# List with filters
docker ps --filter "status=running"
docker ps --filter "name=nginx"
docker ps --filter "ancestor=nginx"  # From nginx image

# List container IDs only
docker ps -q

# List with size
docker ps -s
```

---

**3. Container Logs:**
```bash
# View logs
docker logs myapp

# Follow logs (like tail -f)
docker logs -f myapp

# Show timestamps
docker logs -t myapp

# Show last 100 lines
docker logs --tail 100 myapp

# Show logs since timestamp
docker logs --since 2024-01-01T00:00:00 myapp

# Show logs until timestamp
docker logs --until 2024-01-01T23:59:59 myapp

# Combine options
docker logs -f --tail 50 --since 1h myapp
```

---

**4. Execute Commands in Running Container:**
```bash
# Execute command
docker exec myapp ls /app

# Interactive bash shell
docker exec -it myapp bash

# Execute as specific user
docker exec -u root myapp apt-get update

# Execute with environment variable
docker exec -e VAR=value myapp env

# Execute in specific directory
docker exec -w /app myapp ls
```

---

**5. Inspect Container:**
```bash
# View all details (JSON)
docker inspect myapp

# View specific field
docker inspect -f '{{.State.Status}}' myapp
docker inspect -f '{{.NetworkSettings.IPAddress}}' myapp
docker inspect -f '{{.Config.Env}}' myapp

# View container processes
docker top myapp

# View resource usage (live)
docker stats myapp

# View filesystem changes
docker diff myapp
# A: Added, D: Deleted, C: Changed
```

---

**6. Copy Files:**
```bash
# Copy from container to host
docker cp myapp:/app/file.txt /host/path/

# Copy from host to container
docker cp /host/file.txt myapp:/app/

# Copy directory
docker cp myapp:/app /host/backup/
```

---

**7. Attach to Container:**
```bash
# Attach to container's main process
docker attach myapp

# Detach without stopping: Ctrl+P, Ctrl+Q
```

---

**8. Container Resource Management:**
```bash
# Update resource limits
docker update --memory="1g" --cpus="2.0" myapp

# View resource usage
docker stats myapp

# View all containers' resource usage
docker stats
```

---

### Container Restart Policies 🔥

**Critical for Production!**

| Policy | Description | Use Case |
|--------|-------------|----------|
| `no` | Never restart (default) | Development, testing |
| `on-failure[:max-retries]` | Restart only on failure | Production apps |
| `always` | Always restart (even after reboot) | Critical services |
| `unless-stopped` | Always restart unless manually stopped | Most production apps |

**Examples:**
```bash
# Never restart
docker run -d --restart=no nginx

# Restart on failure (max 3 times)
docker run -d --restart=on-failure:3 nginx

# Always restart
docker run -d --restart=always nginx

# Restart unless stopped
docker run -d --restart=unless-stopped nginx

# Update restart policy
docker update --restart=always myapp
```

**Behavior:**

```
Scenario: Container exits with code 0 (success)
├─ no: Don't restart
├─ on-failure: Don't restart (exit 0 is success)
├─ always: Restart
└─ unless-stopped: Restart

Scenario: Container exits with code 1 (error)
├─ no: Don't restart
├─ on-failure: Restart (up to max retries)
├─ always: Restart
└─ unless-stopped: Restart

Scenario: Docker daemon restarts
├─ no: Don't restart
├─ on-failure: Don't restart
├─ always: Restart
└─ unless-stopped: Restart

Scenario: Container manually stopped (docker stop)
├─ no: Stay stopped
├─ on-failure: Stay stopped
├─ always: Restart (even after manual stop!)
└─ unless-stopped: Stay stopped (respects manual stop)
```

**Best Practice:** Use `unless-stopped` for most production apps

---

### Container Networking Basics

**Container IP Address:**
```bash
# Get container IP
docker inspect -f '{{.NetworkSettings.IPAddress}}' myapp

# Access from host
curl http://172.17.0.2:80
```

**Port Mapping:**
```bash
# Map single port
docker run -d -p 8080:80 nginx
# Host:Container

# Map multiple ports
docker run -d -p 8080:80 -p 8443:443 nginx

# Map all exposed ports to random host ports
docker run -d -P nginx

# Map to specific host IP
docker run -d -p 127.0.0.1:8080:80 nginx

# Map to random host port
docker run -d -p 80 nginx
```

---

### Container Storage Basics

**Writable Layer:**
- Each container has thin writable layer on top of image
- Changes written here (new files, modified files)
- Deleted when container removed
- Not suitable for persistent data

**View container size:**
```bash
docker ps -s
# SIZE: 2B (virtual 141MB)
# 2B: Writable layer size
# 141MB: Image size
```

---

## 5️⃣ Dockerfile Mastery

### What is a Dockerfile? 🔥🔥

**Dockerfile = Recipe for Building Images**

**Key Points:**
- Text file with instructions
- Each instruction creates a layer
- Executed top to bottom
- Case-insensitive (but UPPERCASE convention)

---

### Dockerfile Instructions 🔥🔥🔥

**Critical for Interviews!**

---

#### 1. FROM - Base Image

**Syntax:**
```dockerfile
FROM <image>[:<tag>] [AS <name>]
```

**Purpose:** Specify base image

**Examples:**
```dockerfile
# Official image
FROM python:3.11

# Specific version
FROM python:3.11.5-slim

# Alpine variant
FROM python:3.11-alpine

# Multi-stage build (named stage)
FROM golang:1.21 AS builder

# Scratch (empty base, for compiled binaries)
FROM scratch
```

**Best Practices:**
- Use official images
- Use specific tags (not `latest`)
- Use slim/alpine for smaller size

---

#### 2. WORKDIR - Set Working Directory

**Syntax:**
```dockerfile
WORKDIR /path/to/directory
```

**Purpose:** Set working directory for subsequent instructions

**Examples:**
```dockerfile
WORKDIR /app

# Creates directory if doesn't exist
WORKDIR /app/src

# Relative paths
WORKDIR /app
WORKDIR src  # Now in /app/src
```

**Best Practice:** Always use absolute paths

---

#### 3. COPY - Copy Files

**Syntax:**
```dockerfile
COPY [--chown=<user>:<group>] <src>... <dest>
```

**Purpose:** Copy files from host to image

**Examples:**
```dockerfile
# Copy single file
COPY app.py /app/

# Copy multiple files
COPY app.py config.py /app/

# Copy directory
COPY ./src /app/src

# Copy with wildcard
COPY *.py /app/

# Copy with ownership
COPY --chown=1000:1000 app.py /app/

# Copy everything (use .dockerignore!)
COPY . /app/
```

**Best Practices:**
- Use .dockerignore to exclude files
- Copy requirements file separately (for caching)
- Use specific paths (not `.` unless necessary)

---

#### 4. ADD - Copy Files (Advanced)

**Syntax:**
```dockerfile
ADD [--chown=<user>:<group>] <src>... <dest>
```

**Purpose:** Copy files + extract tar + download URLs

**Examples:**
```dockerfile
# Same as COPY
ADD app.py /app/

# Extract tar automatically
ADD archive.tar.gz /app/
# Extracts to /app/

# Download from URL
ADD https://example.com/file.txt /app/
```

**COPY vs ADD:**

| Feature | COPY | ADD |
|---------|------|-----|
| Copy files | ✅ | ✅ |
| Extract tar | ❌ | ✅ |
| Download URL | ❌ | ✅ |
| Recommended | ✅ | ❌ (use COPY unless need extraction) |

**Best Practice:** Use COPY unless you need tar extraction

---

#### 5. RUN - Execute Commands

**Syntax:**
```dockerfile
# Shell form
RUN <command>

# Exec form
RUN ["executable", "param1", "param2"]
```

**Purpose:** Execute commands during build

**Examples:**
```dockerfile
# Shell form (uses /bin/sh -c)
RUN apt-get update && apt-get install -y curl

# Exec form (no shell)
RUN ["apt-get", "update"]

# Multi-line with backslash
RUN apt-get update && \
    apt-get install -y \
        curl \
        git \
        vim && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Multiple RUN commands (creates multiple layers)
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get clean
```

**Best Practices:**
- Combine commands with `&&` to reduce layers
- Clean up in same layer (apt-get clean, rm cache)
- Use `\` for readability

---

#### 6. CMD - Default Command

**Syntax:**
```dockerfile
# Exec form (preferred)
CMD ["executable", "param1", "param2"]

# Shell form
CMD command param1 param2

# As parameters to ENTRYPOINT
CMD ["param1", "param2"]
```

**Purpose:** Default command when container starts

**Examples:**
```dockerfile
# Exec form
CMD ["python", "app.py"]

# Shell form
CMD python app.py

# With ENTRYPOINT
ENTRYPOINT ["python"]
CMD ["app.py"]
```

**Key Points:**
- Only one CMD per Dockerfile (last one wins)
- Can be overridden: `docker run myimage python other.py`
- Exec form doesn't invoke shell (no variable expansion)

---

#### 7. ENTRYPOINT - Container Executable

**Syntax:**
```dockerfile
# Exec form (preferred)
ENTRYPOINT ["executable", "param1"]

# Shell form
ENTRYPOINT command param1
```

**Purpose:** Configure container as executable

**Examples:**
```dockerfile
# Simple
ENTRYPOINT ["python", "app.py"]

# With CMD (CMD provides default args)
ENTRYPOINT ["python"]
CMD ["app.py"]

# Shell form
ENTRYPOINT python app.py
```

**Key Points:**
- Only one ENTRYPOINT per Dockerfile
- Cannot be overridden easily (need --entrypoint flag)
- CMD provides default arguments to ENTRYPOINT

---

#### CMD vs ENTRYPOINT 🔥🔥

**Critical for Interviews!**

| Feature | CMD | ENTRYPOINT |
|---------|-----|------------|
| **Purpose** | Default command | Main executable |
| **Override** | Easy (`docker run image cmd`) | Hard (need `--entrypoint`) |
| **Use case** | Flexible commands | Fixed executable |
| **With other** | Provides args to ENTRYPOINT | Uses CMD as default args |

**Examples:**

**Scenario 1: Flexible Command (CMD only)**
```dockerfile
FROM python:3.11
COPY app.py .
CMD ["python", "app.py"]
```
```bash
docker run myimage                    # Runs: python app.py
docker run myimage python other.py    # Runs: python other.py (overridden)
docker run myimage bash               # Runs: bash (overridden)
```

**Scenario 2: Fixed Executable (ENTRYPOINT only)**
```dockerfile
FROM python:3.11
COPY app.py .
ENTRYPOINT ["python", "app.py"]
```
```bash
docker run myimage                    # Runs: python app.py
docker run myimage --debug            # Runs: python app.py --debug (args appended)
docker run myimage bash               # Runs: python app.py bash (not what you want!)
```

**Scenario 3: Best of Both (ENTRYPOINT + CMD)**
```dockerfile
FROM python:3.11
COPY app.py other.py .
ENTRYPOINT ["python"]
CMD ["app.py"]
```
```bash
docker run myimage                    # Runs: python app.py
docker run myimage other.py           # Runs: python other.py (CMD overridden)
docker run myimage other.py --debug   # Runs: python other.py --debug
```

**Best Practice:**
- Use ENTRYPOINT for main executable
- Use CMD for default arguments
- Combine both for flexibility

---

#### 8. ENV - Environment Variables

**Syntax:**
```dockerfile
ENV <key>=<value> ...
```

**Purpose:** Set environment variables

**Examples:**
```dockerfile
# Single variable
ENV APP_ENV=production

# Multiple variables
ENV APP_ENV=production \
    APP_PORT=8080 \
    APP_DEBUG=false

# Old syntax (deprecated)
ENV APP_ENV production
```

**Usage:**
```dockerfile
ENV APP_HOME=/app
WORKDIR $APP_HOME
COPY . $APP_HOME
```

**Key Points:**
- Available during build and runtime
- Can be overridden: `docker run -e APP_ENV=dev myimage`
- Persists in image (visible in `docker inspect`)

---

#### 9. ARG - Build Arguments

**Syntax:**
```dockerfile
ARG <name>[=<default value>]
```

**Purpose:** Variables available only during build

**Examples:**
```dockerfile
# With default
ARG PYTHON_VERSION=3.11
FROM python:${PYTHON_VERSION}

# Without default
ARG APP_VERSION
LABEL version=$APP_VERSION

# Multiple args
ARG USER=appuser
ARG UID=1000
RUN useradd -u $UID $USER
```

**Usage:**
```bash
# Provide at build time
docker build --build-arg PYTHON_VERSION=3.10 .
docker build --build-arg APP_VERSION=1.0.0 .
```

**ARG vs ENV:**

| Feature | ARG | ENV |
|---------|-----|-----|
| **Available during** | Build only | Build + Runtime |
| **Persists in image** | ❌ No | ✅ Yes |
| **Override at** | Build time | Run time |
| **Use case** | Build configuration | Runtime configuration |

---

#### 10. EXPOSE - Document Ports

**Syntax:**
```dockerfile
EXPOSE <port> [<port>/<protocol>...]
```

**Purpose:** Document which ports the container listens on

**Examples:**
```dockerfile
# Single port
EXPOSE 80

# Multiple ports
EXPOSE 80 443

# With protocol
EXPOSE 80/tcp
EXPOSE 53/udp

# Port range
EXPOSE 8000-8010
```

**Key Points:**
- **Documentation only** (doesn't actually publish ports)
- Still need `-p` flag: `docker run -p 8080:80 myimage`
- Used by `-P` flag: `docker run -P myimage` (maps to random host ports)

---

#### 11. VOLUME - Mount Points

**Syntax:**
```dockerfile
VOLUME ["/path/to/volume"]
```

**Purpose:** Create mount point for external storage

**Examples:**
```dockerfile
# Single volume
VOLUME /var/lib/mysql

# Multiple volumes
VOLUME /var/log /var/cache

# JSON format
VOLUME ["/data", "/logs"]
```

**Key Points:**
- Creates anonymous volume if not specified at runtime
- Data persists after container deletion
- Better to specify volumes at runtime: `docker run -v mydata:/var/lib/mysql`

---

#### 12. USER - Set User

**Syntax:**
```dockerfile
USER <user>[:<group>]
USER <UID>[:<GID>]
```

**Purpose:** Set user for subsequent instructions and runtime

**Examples:**
```dockerfile
# Create user and switch
RUN useradd -m appuser
USER appuser

# With group
USER appuser:appgroup

# With UID:GID
USER 1000:1000

# Switch back to root
USER root
```

**Best Practice:**
- Don't run as root in production
- Create non-root user
- Switch to it before CMD/ENTRYPOINT

---

#### 13. LABEL - Metadata

**Syntax:**
```dockerfile
LABEL <key>=<value> <key>=<value> ...
```

**Purpose:** Add metadata to image

**Examples:**
```dockerfile
# Single label
LABEL version="1.0"

# Multiple labels
LABEL version="1.0" \
      description="My app" \
      maintainer="user@example.com"

# Standard labels
LABEL org.opencontainers.image.version="1.0" \
      org.opencontainers.image.authors="user@example.com"
```

**View labels:**
```bash
docker inspect -f '{{.Config.Labels}}' myimage
```

---

#### 14. HEALTHCHECK - Container Health

**Syntax:**
```dockerfile
HEALTHCHECK [OPTIONS] CMD command
HEALTHCHECK NONE  # Disable healthcheck
```

**Purpose:** Tell Docker how to test if container is healthy

**Options:**
- `--interval=DURATION` (default: 30s)
- `--timeout=DURATION` (default: 30s)
- `--start-period=DURATION` (default: 0s)
- `--retries=N` (default: 3)

**Examples:**
```dockerfile
# HTTP check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost/ || exit 1

# Custom script
HEALTHCHECK --interval=5m --timeout=3s \
  CMD /app/healthcheck.sh

# Database check
HEALTHCHECK CMD pg_isready -U postgres || exit 1

# Disable inherited healthcheck
HEALTHCHECK NONE
```

**Exit codes:**
- 0: healthy
- 1: unhealthy
- 2: reserved (don't use)

**Check health:**
```bash
docker ps  # Shows health status
docker inspect --format='{{.State.Health.Status}}' myapp
```

---

#### 15. ONBUILD - Trigger Instructions

**Syntax:**
```dockerfile
ONBUILD <INSTRUCTION>
```

**Purpose:** Add trigger instruction executed when image is used as base

**Examples:**
```dockerfile
# In base image
FROM python:3.11
ONBUILD COPY requirements.txt .
ONBUILD RUN pip install -r requirements.txt

# Child image
FROM mybase:latest
# ONBUILD instructions execute here automatically
COPY . .
CMD ["python", "app.py"]
```

**Use case:** Create reusable base images

---

#### 16. STOPSIGNAL - Stop Signal

**Syntax:**
```dockerfile
STOPSIGNAL signal
```

**Purpose:** Set signal to stop container

**Examples:**
```dockerfile
# Use SIGTERM (default)
STOPSIGNAL SIGTERM

# Use SIGQUIT
STOPSIGNAL SIGQUIT

# Numeric value
STOPSIGNAL 9  # SIGKILL
```

---

#### 17. SHELL - Default Shell

**Syntax:**
```dockerfile
SHELL ["executable", "parameters"]
```

**Purpose:** Override default shell

**Examples:**
```dockerfile
# Default Linux shell
SHELL ["/bin/sh", "-c"]

# Use bash
SHELL ["/bin/bash", "-c"]

# Windows PowerShell
SHELL ["powershell", "-command"]

# Use with RUN
SHELL ["/bin/bash", "-c"]
RUN echo $HOME  # Now uses bash
```

---

### Complete Dockerfile Example 🔥

**Production-Ready Python Web App:**

```dockerfile
# Stage 1: Builder
FROM python:3.11-slim AS builder

# Build arguments
ARG APP_VERSION=1.0.0

# Metadata
LABEL version="${APP_VERSION}" \
      description="My Python Web App" \
      maintainer="user@example.com"

# Install build dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        gcc \
        python3-dev && \
    rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Copy requirements first (for caching)
COPY requirements.txt .

# Install Python dependencies
RUN pip install --no-cache-dir --user -r requirements.txt

# Stage 2: Runtime
FROM python:3.11-slim

# Copy Python packages from builder
COPY --from=builder /root/.local /root/.local

# Set PATH
ENV PATH=/root/.local/bin:$PATH

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    APP_ENV=production \
    APP_PORT=8000

# Create non-root user
RUN useradd -m -u 1000 appuser && \
    mkdir -p /app && \
    chown -R appuser:appuser /app

# Set working directory
WORKDIR /app

# Copy application code
COPY --chown=appuser:appuser . .

# Switch to non-root user
USER appuser

# Expose port
EXPOSE 8000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8000/health || exit 1

# Volume for data
VOLUME /app/data

# Default command
ENTRYPOINT ["python"]
CMD ["app.py"]
```

---

### .dockerignore File 🔥

**Purpose:** Exclude files from build context

**Example `.dockerignore`:**
```
# Version control
.git
.gitignore
.gitattributes

# Python
__pycache__
*.pyc
*.pyo
*.pyd
.Python
*.so
*.egg
*.egg-info
dist
build
.venv
venv/
ENV/

# Node
node_modules/
npm-debug.log

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Docker
Dockerfile
.dockerignore
docker-compose.yml

# Documentation
README.md
docs/
*.md

# Tests
tests/
*.test.js
*.spec.js

# Logs
*.log
logs/

# Temporary files
*.tmp
tmp/
temp/
```

**Benefits:**
- Faster builds (smaller context)
- Smaller images
- Better security (no secrets in image)

---

### Multi-Stage Builds 🔥🔥

**Critical for Production!**

**Purpose:** Separate build environment from runtime environment

**Benefits:**
- Smaller final image
- No build tools in production
- Better security
- Faster deployments

**Example 1: Go Application**

```dockerfile
# Stage 1: Build
FROM golang:1.21 AS builder

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o main .

# Stage 2: Runtime
FROM alpine:latest

RUN apk --no-cache add ca-certificates
WORKDIR /root/

# Copy only binary from builder
COPY --from=builder /app/main .

CMD ["./main"]
```

**Size Comparison:**
- Single stage (golang:1.21): ~1 GB
- Multi-stage (alpine): ~15 MB
- **Reduction: 98.5%!**

---

**Example 2: Node.js Application**

```dockerfile
# Stage 1: Dependencies
FROM node:18 AS dependencies

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Stage 2: Build
FROM node:18 AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 3: Runtime
FROM node:18-alpine

WORKDIR /app

# Copy dependencies from dependencies stage
COPY --from=dependencies /app/node_modules ./node_modules

# Copy built files from builder stage
COPY --from=builder /app/dist ./dist
COPY package*.json ./

EXPOSE 3000
CMD ["node", "dist/index.js"]
```

---

**Example 3: Python Application**

```dockerfile
# Stage 1: Builder
FROM python:3.11 AS builder

WORKDIR /app
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

# Stage 2: Runtime
FROM python:3.11-slim

# Copy installed packages
COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH

WORKDIR /app
COPY . .

CMD ["python", "app.py"]
```

---

### Distroless Images 🔥

**What:** Minimal images with only app and runtime dependencies

**Benefits:**
- Smallest possible size
- Minimal attack surface
- No shell, package manager, or debugging tools
- Production-grade security

**Available Distroless Images:**
- `gcr.io/distroless/static` - Static binaries (Go, Rust)
- `gcr.io/distroless/base` - glibc, libssl
- `gcr.io/distroless/python3` - Python runtime
- `gcr.io/distroless/java` - Java runtime
- `gcr.io/distroless/nodejs` - Node.js runtime

**Example: Go Application**

```dockerfile
# Build stage
FROM golang:1.21 AS builder
WORKDIR /app
COPY . .
RUN CGO_ENABLED=0 go build -o main .

# Runtime stage
FROM gcr.io/distroless/static

COPY --from=builder /app/main /
CMD ["/main"]
```

**Example: Python Application**

```dockerfile
# Build stage
FROM python:3.11 AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --user -r requirements.txt

# Runtime stage
FROM gcr.io/distroless/python3

COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH

WORKDIR /app
COPY . .
CMD ["app.py"]
```

**Limitations:**
- ❌ No shell (can't `docker exec` into container)
- ❌ No debugging tools
- ❌ Only for production

**Debugging Distroless:**
```bash
# Use debug variant for troubleshooting
FROM gcr.io/distroless/python3:debug

# Or use multi-stage with debug stage
FROM gcr.io/distroless/python3:debug AS debug
FROM gcr.io/distroless/python3 AS production
```

---

### Dockerfile Best Practices 🔥🔥

**1. Order Instructions by Change Frequency:**
```dockerfile
# ✅ Good: Least changing at top
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .  # Most frequently changing
CMD ["python", "app.py"]

# ❌ Bad: Frequently changing at top
FROM python:3.11-slim
COPY . /app  # Changes often, invalidates cache
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

---

**2. Combine RUN Commands:**
```dockerfile
# ✅ Good: Single layer
RUN apt-get update && \
    apt-get install -y curl git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# ❌ Bad: Multiple layers
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y git
RUN apt-get clean
```

---

**3. Use .dockerignore:**
```dockerfile
# Always create .dockerignore
# Exclude unnecessary files
```

---

**4. Don't Install Unnecessary Packages:**
```dockerfile
# ✅ Good: Only what's needed
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    apt-get clean

# ❌ Bad: Installs recommended packages
RUN apt-get install -y curl
```

---

**5. Use Multi-Stage Builds:**
```dockerfile
# ✅ Good: Separate build and runtime
FROM golang:1.21 AS builder
# ... build steps ...
FROM alpine:latest
COPY --from=builder /app/main .
```

---

**6. Run as Non-Root User:**
```dockerfile
# ✅ Good: Non-root user
RUN useradd -m appuser
USER appuser

# ❌ Bad: Running as root
USER root
```

---

**7. Use Specific Tags:**
```dockerfile
# ✅ Good: Specific version
FROM python:3.11.5-slim

# ❌ Bad: Latest tag
FROM python:latest
```

---

**8. Leverage Build Cache:**
```dockerfile
# ✅ Good: Dependencies cached separately
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .

# ❌ Bad: Everything copied together
COPY . .
RUN pip install -r requirements.txt
```

---

**9. Minimize Layers:**
```dockerfile
# ✅ Good: Fewer layers
RUN apt-get update && apt-get install -y curl git

# ❌ Bad: More layers
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y git
```

---

**10. Add Metadata:**
```dockerfile
# ✅ Good: Labeled
LABEL version="1.0" \
      maintainer="user@example.com" \
      description="My app"
```

---

## 6️⃣ Docker Volumes & Data Persistence

### Why Volumes? 🔥

**Problem:** Container filesystem is ephemeral
- Data lost when container deleted
- Not shared between containers
- Poor I/O performance

**Solution:** Volumes
- Persist data beyond container lifecycle
- Share data between containers
- Better I/O performance
- Managed by Docker

---

### Types of Mounts 🔥🔥

**Critical for Interviews!**

**1. Volumes (Recommended)**
**2. Bind Mounts**
**3. tmpfs Mounts**

---

### 1. Volumes 🔥

**What:** Docker-managed storage

**Location:** `/var/lib/docker/volumes/` (Linux)

**Characteristics:**
- ✅ Managed by Docker
- ✅ Persists after container deletion
- ✅ Can be shared between containers
- ✅ Works on all platforms (Linux, Windows, Mac)
- ✅ Can be backed up easily
- ✅ Better performance than bind mounts

**Create Volume:**
```bash
# Create named volume
docker volume create mydata

# List volumes
docker volume ls

# Inspect volume
docker volume inspect mydata

# Output:
# [
#     {
#         "CreatedAt": "2024-01-01T00:00:00Z",
#         "Driver": "local",
#         "Mountpoint": "/var/lib/docker/volumes/mydata/_data",
#         "Name": "mydata"
#     }
# ]
```

**Use Volume:**
```bash
# Named volume
docker run -d -v mydata:/var/lib/mysql mysql

# Anonymous volume (Docker generates name)
docker run -d -v /var/lib/mysql mysql

# Read-only volume
docker run -d -v mydata:/var/lib/mysql:ro mysql

# Volume with driver options
docker run -d -v mydata:/data:rw,z mysql
```

**Volume Drivers:**
- `local` (default): Local filesystem
- `nfs`: Network filesystem
- `cifs`: Windows shares
- Third-party: AWS EBS, Azure Disk, etc.

**Remove Volume:**
```bash
# Remove specific volume
docker volume rm mydata

# Remove all unused volumes
docker volume prune

# Remove volume when removing container
docker rm -v mycontainer
```

---

### 2. Bind Mounts 🔥

**What:** Mount host directory/file into container

**Location:** Any path on host

**Characteristics:**
- ✅ Direct access to host filesystem
- ✅ Real-time changes (great for development)
- ✅ Can mount specific files
- ❌ Host path must exist
- ❌ Less portable (path may not exist on other hosts)
- ❌ Security concerns (container can modify host files)

**Use Bind Mount:**
```bash
# Mount directory
docker run -d -v /host/path:/container/path nginx

# Mount file
docker run -d -v /host/config.conf:/etc/nginx/nginx.conf nginx

# Read-only
docker run -d -v /host/path:/container/path:ro nginx

# Current directory ($(pwd))
docker run -d -v $(pwd):/app node

# Windows path
docker run -d -v C:\Users\me\app:/app node
```

**Development Workflow:**
```bash
# Mount source code for live reload
docker run -d \
  -v $(pwd)/src:/app/src \
  -v $(pwd)/public:/app/public \
  -p 3000:3000 \
  myapp:dev

# Code changes on host immediately reflected in container!
```

---

### 3. tmpfs Mounts 🔥

**What:** Mount in memory (RAM)

**Location:** Memory only (not on disk)

**Characteristics:**
- ✅ Very fast (RAM speed)
- ✅ Secure (no disk traces)
- ✅ Temporary data
- ❌ Lost when container stops
- ❌ Not shared between containers
- ❌ Linux only

**Use tmpfs:**
```bash
# Mount tmpfs
docker run -d --tmpfs /app/cache nginx

# With size limit
docker run -d --tmpfs /app/cache:size=100m nginx

# Multiple tmpfs mounts
docker run -d \
  --tmpfs /app/cache \
  --tmpfs /app/tmp \
  nginx
```

**Use Cases:**
- Sensitive temporary data (passwords, tokens)
- Cache that doesn't need persistence
- Temporary processing files

---

### Comparison Table 🔥🔥

**Critical for Interviews!**

| Feature | Volume | Bind Mount | tmpfs |
|---------|--------|------------|-------|
| **Managed by** | Docker | User | Memory |
| **Location** | `/var/lib/docker/volumes/` | Any host path | RAM |
| **Persists** | ✅ Yes | ✅ Yes | ❌ No |
| **Shareable** | ✅ Yes | ✅ Yes | ❌ No |
| **Performance** | Good | Good | Excellent |
| **Portable** | ✅ Yes | ❌ No | ❌ No |
| **Host path required** | ❌ No | ✅ Yes | ❌ No |
| **Use case** | Databases, production | Development, config | Cache, secrets |

---

### Volume Commands 🔥

**Create:**
```bash
docker volume create mydata
docker volume create --driver local mydata
docker volume create --opt type=nfs --opt device=:/path mydata
```

**List:**
```bash
docker volume ls
docker volume ls --filter "dangling=true"  # Unused volumes
docker volume ls --filter "name=my"        # Filter by name
```

**Inspect:**
```bash
docker volume inspect mydata
docker volume inspect --format '{{.Mountpoint}}' mydata
```

**Remove:**
```bash
docker volume rm mydata
docker volume rm volume1 volume2 volume3
docker volume prune  # Remove all unused
docker volume prune --filter "label=env=dev"
```

---

### Volume Use Cases 🔥

**1. Database Data:**
```bash
# MySQL
docker run -d \
  --name mysql \
  -v mysql-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  mysql:8

# PostgreSQL
docker run -d \
  --name postgres \
  -v postgres-data:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=secret \
  postgres:15
```

---

**2. Shared Data Between Containers:**
```bash
# Create volume
docker volume create shared-data

# Container 1 writes data
docker run -d --name writer -v shared-data:/data alpine \
  sh -c "while true; do date >> /data/log.txt; sleep 1; done"

# Container 2 reads data
docker run -d --name reader -v shared-data:/data:ro alpine \
  sh -c "while true; do tail -f /data/log.txt; done"
```

---

**3. Backup and Restore:**
```bash
# Backup volume to tar
docker run --rm \
  -v mydata:/data \
  -v $(pwd):/backup \
  alpine tar czf /backup/mydata-backup.tar.gz -C /data .

# Restore volume from tar
docker run --rm \
  -v mydata:/data \
  -v $(pwd):/backup \
  alpine tar xzf /backup/mydata-backup.tar.gz -C /data
```

---

**4. Development with Live Reload:**
```bash
# Node.js development
docker run -d \
  -v $(pwd)/src:/app/src \
  -v $(pwd)/public:/app/public \
  -v node_modules:/app/node_modules \  # Anonymous volume for node_modules
  -p 3000:3000 \
  node:18 npm run dev
```

---

### Volume Best Practices 🔥

**1. Use Named Volumes for Production:**
```bash
# ✅ Good: Named volume
docker run -v mydata:/data mysql

# ❌ Bad: Anonymous volume
docker run -v /data mysql
```

---

**2. Use Bind Mounts for Development:**
```bash
# ✅ Good: Live code reload
docker run -v $(pwd):/app myapp:dev

# ❌ Bad: Volume for development (no live reload)
docker run -v mydata:/app myapp:dev
```

---

**3. Use Read-Only When Possible:**
```bash
# ✅ Good: Read-only config
docker run -v $(pwd)/config:/etc/app:ro myapp

# ❌ Bad: Writable config (security risk)
docker run -v $(pwd)/config:/etc/app myapp
```

---

**4. Backup Important Data:**
```bash
# Regular backups
docker run --rm -v mydata:/data -v $(pwd):/backup \
  alpine tar czf /backup/backup-$(date +%Y%m%d).tar.gz -C /data .
```

---

**5. Clean Up Unused Volumes:**
```bash
# Remove unused volumes regularly
docker volume prune
```

---

**6. Use Volume Drivers for Advanced Storage:**
```bash
# NFS volume
docker volume create --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.1.100,rw \
  --opt device=:/path/to/dir \
  nfs-volume

# AWS EBS (with plugin)
docker volume create --driver rexray/ebs \
  --opt size=10 \
  ebs-volume
```

---

## 7️⃣ Docker Networking

### Docker Networking Overview 🔥🔥

**Critical for Interviews!**

**Purpose:** Enable communication between:
- Container ↔ Container
- Container ↔ Host
- Container ↔ External world

**Key Concepts:**
- **Network Driver:** Determines how containers communicate
- **Network:** Isolated communication channel
- **DNS:** Containers resolve each other by name

---

### Network Drivers 🔥🔥🔥

**Critical for Interviews!**

**1. Bridge (Default)**
**2. Host**
**3. None**
**4. Overlay**
**5. Macvlan**

---

### 1. Bridge Network 🔥

**What:** Default network for containers on same host

**Characteristics:**
- ✅ Containers can communicate by name or IP
- ✅ Isolated from host network
- ✅ Port mapping required for external access
- ✅ Built-in DNS resolution
- ✅ Best for single-host setups

**How It Works:**
```
┌─────────────────────────────────────┐
│           Host Machine               │
│  ┌───────────────────────────────┐  │
│  │      Bridge Network           │  │
│  │  ┌──────────┐  ┌──────────┐  │  │
│  │  │Container1│  │Container2│  │  │
│  │  │  web     │  │   db     │  │  │
│  │  │172.17.0.2│  │172.17.0.3│  │  │
│  │  └──────────┘  └──────────┘  │  │
│  └───────────────────────────────┘  │
│              ▲                       │
│              │ Port mapping          │
│              ▼                       │
│         Host: 8080 → Container: 80  │
└─────────────────────────────────────┘
```

**Default Bridge:**
```bash
# Containers on default bridge
docker run -d --name web nginx
docker run -d --name db mysql

# Containers can communicate by IP
docker exec web ping 172.17.0.3

# But NOT by name (no DNS on default bridge)
docker exec web ping db  # Fails!
```

**Custom Bridge (Recommended):**
```bash
# Create custom bridge
docker network create mynet

# Run containers on custom bridge
docker run -d --name web --network mynet nginx
docker run -d --name db --network mynet mysql

# Containers can communicate by name!
docker exec web ping db  # Works!
docker exec db ping web  # Works!
```

**Why Custom Bridge?**
- ✅ Automatic DNS resolution (container names)
- ✅ Better isolation
- ✅ Can connect/disconnect containers on the fly

**Commands:**
```bash
# Create network
docker network create mynet
docker network create --driver bridge mynet
docker network create --subnet 192.168.1.0/24 mynet

# Run container on network
docker run -d --network mynet --name web nginx

# Connect running container
docker network connect mynet existing-container

# Disconnect container
docker network disconnect mynet existing-container

# Inspect network
docker network inspect mynet

# List networks
docker network ls

# Remove network
docker network rm mynet
```

---

### 2. Host Network 🔥

**What:** Container shares host's network stack

**Characteristics:**
- ✅ No network isolation
- ✅ Best performance (no NAT overhead)
- ✅ Container uses host's IP
- ❌ Port conflicts possible
- ❌ Less secure
- ❌ Linux only

**How It Works:**
```
┌─────────────────────────────────────┐
│           Host Machine               │
│         IP: 192.168.1.100            │
│                                      │
│  ┌──────────────────────────────┐   │
│  │  Container (host network)    │   │
│  │  Uses host's network stack   │   │
│  │  IP: 192.168.1.100 (same!)   │   │
│  └──────────────────────────────┘   │
│                                      │
│  No port mapping needed!             │
│  Container port 80 = Host port 80    │
└─────────────────────────────────────┘
```

**Usage:**
```bash
# Run with host network
docker run -d --network host nginx

# No port mapping needed!
# Container's port 80 directly accessible on host

# Access: http://localhost:80
curl http://localhost:80
```

**Use Cases:**
- High-performance applications
- Network monitoring tools
- When you need host's network interface

**Limitations:**
- Cannot run multiple containers on same port
- Less isolation (security concern)

---

### 3. None Network 🔥

**What:** No networking

**Characteristics:**
- ✅ Complete network isolation
- ✅ Maximum security
- ❌ No network interface
- ❌ Cannot communicate with anything

**Usage:**
```bash
# Run with no network
docker run -d --network none alpine

# Container has no network interface
docker exec container ip addr
# Only loopback (lo)
```

**Use Cases:**
- Isolated computation
- Security-sensitive tasks
- Batch processing (no network needed)

---

### 4. Overlay Network 🔥

**What:** Multi-host networking (Docker Swarm/Kubernetes)

**Characteristics:**
- ✅ Spans multiple Docker hosts
- ✅ Containers on different hosts can communicate
- ✅ Encrypted by default (optional)
- ✅ Built-in load balancing
- ❌ Requires Swarm mode or Kubernetes

**How It Works:**
```
┌─────────────────┐       ┌─────────────────┐
│   Host 1        │       │   Host 2        │
│  ┌───────────┐  │       │  ┌───────────┐  │
│  │Container1 │  │       │  │Container2 │  │
│  │   web     │  │       │  │    db     │  │
│  └─────┬─────┘  │       │  └─────┬─────┘  │
│        │        │       │        │        │
│  ┌─────▼──────┐ │       │  ┌─────▼──────┐ │
│  │  Overlay   │◄├───────┤─►│  Overlay   │ │
│  │  Network   │ │       │  │  Network   │ │
│  └────────────┘ │       │  └────────────┘ │
└─────────────────┘       └─────────────────┘
      Containers can communicate across hosts!
```

**Usage:**
```bash
# Initialize Swarm
docker swarm init

# Create overlay network
docker network create --driver overlay myoverlay

# Deploy service on overlay
docker service create --name web --network myoverlay nginx
```

---

### 5. Macvlan Network

**What:** Assign MAC address to container (appears as physical device)

**Use Cases:**
- Legacy applications expecting physical network
- Need direct access to physical network
- VLAN integration

---

### Network Driver Comparison 🔥🔥

**Critical for Interviews!**

| Driver | Isolation | DNS | Multi-Host | Performance | Use Case |
|--------|-----------|-----|------------|-------------|----------|
| **Bridge** | ✅ Yes | ✅ Yes (custom) | ❌ No | Good | Most apps (single host) |
| **Host** | ❌ No | ❌ No | ❌ No | Best | High performance |
| **None** | ✅ Max | ❌ No | ❌ No | N/A | Isolated tasks |
| **Overlay** | ✅ Yes | ✅ Yes | ✅ Yes | Good | Multi-host (Swarm/K8s) |
| **Macvlan** | ✅ Yes | ❌ No | ❌ No | Good | Legacy apps |

---

### Container Communication 🔥

**1. Container to Container (Same Network):**
```bash
# Create network
docker network create mynet

# Run containers
docker run -d --name web --network mynet nginx
docker run -d --name db --network mynet mysql

# Communicate by name
docker exec web ping db
docker exec web curl http://db:3306
```

---

**2. Container to Host:**
```bash
# From container, access host
docker exec container curl http://host.docker.internal:8080

# Special DNS name: host.docker.internal (Docker Desktop)
# On Linux: Use host's IP (172.17.0.1 on default bridge)
```

---

**3. Container to External:**
```bash
# Containers can access internet by default
docker exec container curl https://google.com

# Unless network is isolated (none)
```

---

**4. External to Container:**
```bash
# Publish port
docker run -d -p 8080:80 nginx

# Access from host
curl http://localhost:8080

# Access from external machine
curl http://host-ip:8080
```

---

### Port Mapping 🔥

**Syntax:** `-p [host-ip:]host-port:container-port[/protocol]`

**Examples:**
```bash
# Map single port
docker run -d -p 8080:80 nginx
# Host:8080 → Container:80

# Map multiple ports
docker run -d -p 8080:80 -p 8443:443 nginx

# Map to specific host IP
docker run -d -p 127.0.0.1:8080:80 nginx
# Only accessible from localhost

# Map to random host port
docker run -d -p 80 nginx
# Docker assigns random port

# Map all exposed ports to random ports
docker run -d -P nginx

# Map UDP port
docker run -d -p 53:53/udp dns-server

# Map port range
docker run -d -p 8000-8010:8000-8010 myapp

# View port mappings
docker port container-name
```

---

### DNS Resolution 🔥

**Custom Bridge Networks:**
- ✅ Automatic DNS resolution
- Containers resolve each other by name
- Built-in DNS server

**Example:**
```bash
docker network create mynet
docker run -d --name web --network mynet nginx
docker run -d --name db --network mynet mysql

# Resolve by name
docker exec web nslookup db
# Returns db's IP address

# Ping by name
docker exec web ping db
```

**Default Bridge:**
- ❌ No automatic DNS
- Must use `--link` (deprecated) or IP addresses

---

### Network Commands 🔥

**Create:**
```bash
docker network create mynet
docker network create --driver bridge mynet
docker network create --subnet 192.168.1.0/24 --gateway 192.168.1.1 mynet
docker network create --internal mynet  # No external access
```

**List:**
```bash
docker network ls
docker network ls --filter "driver=bridge"
docker network ls --filter "name=my"
```

**Inspect:**
```bash
docker network inspect mynet
docker network inspect --format '{{.IPAM.Config}}' mynet
```

**Connect/Disconnect:**
```bash
# Connect container to network
docker network connect mynet container-name

# Connect with alias
docker network connect --alias web mynet container-name

# Connect with IP
docker network connect --ip 192.168.1.100 mynet container-name

# Disconnect
docker network disconnect mynet container-name
```

**Remove:**
```bash
docker network rm mynet
docker network prune  # Remove all unused networks
```

---

### Network Best Practices 🔥

**1. Use Custom Bridge Networks:**
```bash
# ✅ Good: Custom bridge with DNS
docker network create mynet
docker run --network mynet --name web nginx

# ❌ Bad: Default bridge (no DNS)
docker run --name web nginx
```

---

**2. Isolate Applications:**
```bash
# ✅ Good: Separate networks per app
docker network create app1-net
docker network create app2-net

docker run --network app1-net app1-web
docker run --network app2-net app2-web
```

---

**3. Use Internal Networks for Databases:**
```bash
# ✅ Good: Internal network (no external access)
docker network create --internal db-net
docker run --network db-net mysql

# Database not accessible from outside!
```

---

**4. Minimize Port Exposure:**
```bash
# ✅ Good: Only necessary ports
docker run -p 80:80 nginx

# ❌ Bad: Exposing all ports
docker run -P nginx
```

---

**5. Use Host Network Sparingly:**
```bash
# ✅ Good: Bridge network (isolated)
docker run --network mynet nginx

# ❌ Bad: Host network (unless necessary)
docker run --network host nginx
```

---

## 8️⃣ Docker Compose

### What is Docker Compose? 🔥🔥

**Docker Compose = Multi-Container Application Management**

**Purpose:**
- Define multi-container apps in YAML file
- Start/stop all services with single command
- Manage dependencies between services
- Configure networks and volumes

**Think of it as:**
- Dockerfile = Single container recipe
- Docker Compose = Multi-container orchestration

---

### Why Docker Compose? 🔥

**Without Compose:**
```bash
# Create network
docker network create mynet

# Create volumes
docker volume create db-data
docker volume create web-data

# Run database
docker run -d \
  --name db \
  --network mynet \
  -v db-data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=secret \
  mysql:8

# Run backend
docker run -d \
  --name api \
  --network mynet \
  -e DB_HOST=db \
  myapi:latest

# Run frontend
docker run -d \
  --name web \
  --network mynet \
  -p 80:80 \
  -v web-data:/app/data \
  myweb:latest

# Tedious! Many commands! Easy to forget!
```

**With Compose:**
```yaml
# docker-compose.yml
version: "3.9"
services:
  db:
    image: mysql:8
    volumes:
      - db-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret

  api:
    image: myapi:latest
    depends_on:
      - db
    environment:
      DB_HOST: db

  web:
    image: myweb:latest
    ports:
      - "80:80"
    volumes:
      - web-data:/app/data
    depends_on:
      - api

volumes:
  db-data:
  web-data:
```

```bash
# Start everything!
docker-compose up -d

# Stop everything!
docker-compose down
```

---

### Docker Compose File Structure 🔥🔥

**Critical for Interviews!**

**Basic Structure:**
```yaml
version: "3.9"  # Compose file version

services:       # Define containers
  service1:
    # Service configuration
  service2:
    # Service configuration

networks:       # Define networks (optional)
  network1:
    # Network configuration

volumes:        # Define volumes (optional)
  volume1:
    # Volume configuration

configs:        # Define configs (optional)
  config1:
    # Config configuration

secrets:        # Define secrets (optional)
  secret1:
    # Secret configuration
```

---

### Service Configuration 🔥🔥

**Complete Example:**
```yaml
version: "3.9"

services:
  web:
    # Image
    image: nginx:latest
    
    # Or build from Dockerfile
    build:
      context: ./web
      dockerfile: Dockerfile
      args:
        VERSION: "1.0"
    
    # Container name
    container_name: my-web
    
    # Hostname
    hostname: web-server
    
    # Restart policy
    restart: unless-stopped
    
    # Ports
    ports:
      - "8080:80"           # host:container
      - "8443:443"
      - "127.0.0.1:8000:8000"  # bind to specific IP
    
    # Expose (documentation only)
    expose:
      - "80"
      - "443"
    
    # Environment variables
    environment:
      - APP_ENV=production
      - APP_DEBUG=false
      APP_KEY: secret123    # Alternative syntax
    
    # Environment file
    env_file:
      - .env
      - .env.prod
    
    # Volumes
    volumes:
      - web-data:/app/data              # named volume
      - ./config:/etc/nginx:ro          # bind mount (read-only)
      - /var/run/docker.sock:/var/run/docker.sock  # socket
    
    # Networks
    networks:
      - frontend
      - backend
    
    # Dependencies
    depends_on:
      - db
      - cache
    
    # Command
    command: nginx -g "daemon off;"
    
    # Entrypoint
    entrypoint: /docker-entrypoint.sh
    
    # Working directory
    working_dir: /app
    
    # User
    user: "1000:1000"
    
    # Resource limits
    deploy:
      resources:
        limits:
          cpus: "2.0"
          memory: 1G
        reservations:
          cpus: "1.0"
          memory: 512M
    
    # Health check
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    
    # Logging
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
    
    # DNS
    dns:
      - 8.8.8.8
      - 8.8.4.4
    
    # Extra hosts
    extra_hosts:
      - "host.docker.internal:host-gateway"
      - "myhost:192.168.1.100"
    
    # Labels
    labels:
      com.example.description: "Web server"
      com.example.version: "1.0"

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true  # No external access

volumes:
  web-data:
    driver: local
```

---

### Common Service Patterns 🔥

**1. Web Application with Database:**
```yaml
version: "3.9"

services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html:ro
    depends_on:
      - api
    networks:
      - frontend

  api:
    build: ./api
    environment:
      DB_HOST: db
      DB_USER: root
      DB_PASSWORD: secret
    depends_on:
      - db
    networks:
      - frontend
      - backend

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: myapp
    volumes:
      - db-data:/var/lib/mysql
    networks:
      - backend

networks:
  frontend:
  backend:
    internal: true

volumes:
  db-data:
```

---

**2. Microservices Architecture:**
```yaml
version: "3.9"

services:
  nginx:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - user-service
      - product-service
      - order-service

  user-service:
    build: ./services/user
    environment:
      DB_HOST: user-db
    depends_on:
      - user-db

  product-service:
    build: ./services/product
    environment:
      DB_HOST: product-db
    depends_on:
      - product-db

  order-service:
    build: ./services/order
    environment:
      DB_HOST: order-db
      USER_SERVICE_URL: http://user-service:3000
      PRODUCT_SERVICE_URL: http://product-service:3000
    depends_on:
      - order-db
      - user-service
      - product-service

  user-db:
    image: postgres:15
    environment:
      POSTGRES_DB: users
      POSTGRES_PASSWORD: secret
    volumes:
      - user-db-data:/var/lib/postgresql/data

  product-db:
    image: postgres:15
    environment:
      POSTGRES_DB: products
      POSTGRES_PASSWORD: secret
    volumes:
      - product-db-data:/var/lib/postgresql/data

  order-db:
    image: postgres:15
    environment:
      POSTGRES_DB: orders
      POSTGRES_PASSWORD: secret
    volumes:
      - order-db-data:/var/lib/postgresql/data

volumes:
  user-db-data:
  product-db-data:
  order-db-data:
```

---

**3. Development Environment:**
```yaml
version: "3.9"

services:
  app:
    build:
      context: .
      target: development  # Multi-stage build
    volumes:
      - ./src:/app/src:cached  # Live reload
      - ./public:/app/public:cached
      - node_modules:/app/node_modules  # Don't overwrite
    ports:
      - "3000:3000"
      - "9229:9229"  # Debugger port
    environment:
      NODE_ENV: development
      DEBUG: "app:*"
    command: npm run dev

  db:
    image: postgres:15
    ports:
      - "5432:5432"  # Expose for local tools
    environment:
      POSTGRES_DB: myapp_dev
      POSTGRES_PASSWORD: dev
    volumes:
      - db-dev-data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  mailhog:  # Email testing
    image: mailhog/mailhog
    ports:
      - "1025:1025"  # SMTP
      - "8025:8025"  # Web UI

volumes:
  node_modules:
  db-dev-data:
```

---

### Docker Compose Commands 🔥🔥

**Critical for Interviews!**

**Start Services:**
```bash
# Start all services (foreground)
docker-compose up

# Start all services (background/detached)
docker-compose up -d

# Start specific services
docker-compose up web db

# Rebuild images before starting
docker-compose up --build

# Force recreate containers
docker-compose up --force-recreate

# Scale services
docker-compose up --scale web=3
```

---

**Stop Services:**
```bash
# Stop services (containers remain)
docker-compose stop

# Stop specific services
docker-compose stop web

# Stop and remove containers, networks
docker-compose down

# Stop and remove containers, networks, volumes
docker-compose down -v

# Stop and remove containers, networks, images
docker-compose down --rmi all
```

---

**View Status:**
```bash
# List running services
docker-compose ps

# List all services (including stopped)
docker-compose ps -a

# View logs
docker-compose logs

# Follow logs
docker-compose logs -f

# Logs for specific service
docker-compose logs web

# Last 100 lines
docker-compose logs --tail=100

# View resource usage
docker-compose top
```

---

**Execute Commands:**
```bash
# Execute command in running service
docker-compose exec web ls /app

# Interactive shell
docker-compose exec web bash

# Run one-off command (creates new container)
docker-compose run web python manage.py migrate

# Run without dependencies
docker-compose run --no-deps web bash
```

---

**Build:**
```bash
# Build all images
docker-compose build

# Build specific service
docker-compose build web

# Build without cache
docker-compose build --no-cache

# Build with arguments
docker-compose build --build-arg VERSION=1.0 web
```

---

**Other Commands:**
```bash
# Validate compose file
docker-compose config

# View config with resolved values
docker-compose config --resolve-image-digests

# Pull images
docker-compose pull

# Push images
docker-compose push

# Restart services
docker-compose restart

# Pause services
docker-compose pause

# Unpause services
docker-compose unpause

# View port mappings
docker-compose port web 80
```

---

### Environment Variables 🔥

**1. In Compose File:**
```yaml
services:
  web:
    environment:
      - APP_ENV=production
      - APP_DEBUG=false
```

**2. From .env File:**
```bash
# .env
APP_ENV=production
APP_DEBUG=false
DB_PASSWORD=secret
```

```yaml
services:
  web:
    env_file:
      - .env
```

**3. Variable Substitution:**
```yaml
services:
  web:
    image: nginx:${NGINX_VERSION:-latest}
    ports:
      - "${WEB_PORT:-80}:80"
    environment:
      APP_ENV: ${APP_ENV}
```

```bash
# .env
NGINX_VERSION=1.21
WEB_PORT=8080
APP_ENV=production
```

---

### Depends On 🔥

**Simple Dependency:**
```yaml
services:
  web:
    depends_on:
      - db
  db:
    image: mysql:8
```

**With Health Check (Compose v3.9+):**
```yaml
services:
  web:
    depends_on:
      db:
        condition: service_healthy
  
  db:
    image: mysql:8
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5
```

**Note:** `depends_on` only controls startup order, not readiness!

---

### Networks in Compose 🔥

**Default Network:**
```yaml
# Compose creates default network automatically
services:
  web:
    image: nginx
  db:
    image: mysql
# Both on same network, can communicate by name
```

**Custom Networks:**
```yaml
services:
  web:
    networks:
      - frontend
  api:
    networks:
      - frontend
      - backend
  db:
    networks:
      - backend

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true  # No external access
```

**Network Aliases:**
```yaml
services:
  web:
    networks:
      frontend:
        aliases:
          - webserver
          - www
```

---

### Volumes in Compose 🔥

**Named Volumes:**
```yaml
services:
  db:
    volumes:
      - db-data:/var/lib/mysql

volumes:
  db-data:  # Docker manages
```

**Bind Mounts:**
```yaml
services:
  web:
    volumes:
      - ./html:/usr/share/nginx/html:ro
      - ./config:/etc/nginx:ro
```

**Volume Configuration:**
```yaml
volumes:
  db-data:
    driver: local
    driver_opts:
      type: nfs
      o: addr=192.168.1.100,rw
      device: ":/path/to/dir"
```

---

### Production Compose File 🔥

**docker-compose.prod.yml:**
```yaml
version: "3.9"

services:
  web:
    image: myregistry/myapp:${VERSION}
    restart: always
    ports:
      - "80:80"
      - "443:443"
    environment:
      APP_ENV: production
      APP_DEBUG: "false"
    volumes:
      - web-data:/app/data
      - ./ssl:/etc/nginx/ssl:ro
    networks:
      - frontend
    deploy:
      resources:
        limits:
          cpus: "2.0"
          memory: 2G
        reservations:
          cpus: "1.0"
          memory: 1G
      replicas: 3
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 30s
      timeout: 10s
      retries: 3
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "5"

  db:
    image: postgres:15
    restart: always
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
      POSTGRES_DB: myapp
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
    secrets:
      - db_password
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    restart: always
    command: redis-server --appendonly yes
    volumes:
      - redis-data:/data
    networks:
      - backend

networks:
  frontend:
  backend:
    internal: true

volumes:
  web-data:
  db-data:
  redis-data:

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

**Usage:**
```bash
# Deploy
docker-compose -f docker-compose.prod.yml up -d

# Update
docker-compose -f docker-compose.prod.yml pull
docker-compose -f docker-compose.prod.yml up -d
```

---

### Docker Compose Best Practices 🔥

**1. Use Version 3.9:**
```yaml
version: "3.9"  # Latest stable
```

**2. Use .env for Secrets:**
```bash
# .env (add to .gitignore!)
DB_PASSWORD=secret
API_KEY=key123
```

**3. Separate Dev and Prod:**
```bash
docker-compose.yml          # Base
docker-compose.dev.yml      # Development overrides
docker-compose.prod.yml     # Production overrides
```

**4. Use Health Checks:**
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost/health"]
  interval: 30s
  timeout: 10s
  retries: 3
```

**5. Set Restart Policies:**
```yaml
restart: unless-stopped  # Production
restart: "no"            # Development
```

**6. Use Named Volumes:**
```yaml
volumes:
  - db-data:/var/lib/mysql  # Named
  # Not: /var/lib/mysql (anonymous)
```

**7. Limit Resources:**
```yaml
deploy:
  resources:
    limits:
      cpus: "2.0"
      memory: 2G
```

**8. Use Networks for Isolation:**
```yaml
networks:
  frontend:
  backend:
    internal: true  # DB not accessible from outside
```

---

## 9️⃣ Docker Registry & Image Management

### Docker Registry 🔥

**What:** Storage and distribution system for Docker images

**Types:**
1. **Docker Hub** (public registry)
2. **Private Registry** (self-hosted or cloud)
3. **Cloud Registries** (AWS ECR, Google GCR, Azure ACR)

---

### Docker Hub 🔥

**Login:**
```bash
docker login
# Username: myusername
# Password: ********
```

**Push Image:**
```bash
# Tag image
docker tag myapp:latest myusername/myapp:latest
docker tag myapp:latest myusername/myapp:v1.0

# Push
docker push myusername/myapp:latest
docker push myusername/myapp:v1.0
```

**Pull Image:**
```bash
docker pull myusername/myapp:latest
```

**Logout:**
```bash
docker logout
```

---

### Private Registry 🔥

**Run Private Registry:**
```bash
# Run registry
docker run -d -p 5000:5000 --name registry registry:2

# With persistent storage
docker run -d -p 5000:5000 \
  -v registry-data:/var/lib/registry \
  --name registry \
  registry:2
```

**Push to Private Registry:**
```bash
# Tag with registry address
docker tag myapp:latest localhost:5000/myapp:latest

# Push
docker push localhost:5000/myapp:latest
```

**Pull from Private Registry:**
```bash
docker pull localhost:5000/myapp:latest
```

---

### Image Management Commands 🔥

**List Images:**
```bash
docker images
docker images --filter "dangling=true"  # Untagged
docker images --filter "reference=nginx"
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
```

**Remove Images:**
```bash
docker rmi image:tag
docker rmi $(docker images -q)  # Remove all
docker image prune  # Remove dangling
docker image prune -a  # Remove all unused
```

**Image Size:**
```bash
docker images --format "{{.Repository}}:{{.Tag}}\t{{.Size}}"
```

---

## 🔟 Docker Security

### Security Best Practices 🔥🔥

**1. Use Official Images:**
```dockerfile
FROM python:3.11-slim  # Official
# Not: FROM randomuser/python
```

**2. Run as Non-Root:**
```dockerfile
RUN useradd -m appuser
USER appuser
```

**3. Scan Images:**
```bash
docker scan myapp:latest
```

**4. Use Secrets:**
```bash
# Don't hardcode secrets in Dockerfile!
# Use environment variables or Docker secrets
```

**5. Minimize Attack Surface:**
```dockerfile
# Use distroless or alpine
FROM gcr.io/distroless/python3
```

**6. Keep Images Updated:**
```bash
docker pull python:3.11-slim  # Get latest patches
```

---

## 1️⃣1️⃣ Docker Performance & Optimization

### Image Optimization 🔥

**1. Use Multi-Stage Builds**
**2. Minimize Layers**
**3. Use .dockerignore**
**4. Order Instructions Properly**
**5. Use Lightweight Base Images**

---

## 1️⃣2️⃣ Docker in Production

### Production Checklist 🔥

- [ ] Use specific image tags (not `latest`)
- [ ] Run as non-root user
- [ ] Set resource limits
- [ ] Configure health checks
- [ ] Set restart policies
- [ ] Use logging drivers
- [ ] Scan images for vulnerabilities
- [ ] Use secrets management
- [ ] Monitor containers
- [ ] Backup volumes regularly

---

## 1️⃣3️⃣ Docker vs Virtual Machines

**Covered in Section 1**

---

## 1️⃣4️⃣ Docker Troubleshooting

### Common Issues 🔥

**1. Container Won't Start:**
```bash
docker logs container-name
docker inspect container-name
```

**2. Port Already in Use:**
```bash
# Find process using port
lsof -i :8080
# Kill process or use different port
```

**3. Out of Disk Space:**
```bash
docker system prune -a
docker volume prune
```

**4. Network Issues:**
```bash
docker network inspect bridge
docker exec container ping google.com
```

---

## 1️⃣5️⃣ Interview Questions & Answers

### Basic Questions 🔥

**Q1: What is Docker?**
**A:** Docker is a platform for developing, shipping, and running applications in containers. Containers package application code with dependencies, ensuring consistency across environments.

**Q2: Docker vs VM?**
**A:** Containers share host OS kernel (lightweight, fast), VMs have full OS (heavy, slow). Containers: MBs, seconds startup. VMs: GBs, minutes startup.

**Q3: What is a Dockerfile?**
**A:** Text file with instructions to build Docker image. Each instruction creates a layer.

**Q4: CMD vs ENTRYPOINT?**
**A:** CMD provides default command (easily overridden). ENTRYPOINT sets main executable (hard to override). Best practice: Use both together.

**Q5: What are Docker volumes?**
**A:** Persistent data storage managed by Docker. Data survives container deletion.

---

### Advanced Questions 🔥🔥

**Q6: Explain Docker architecture.**
**A:** Client-server architecture. Client (CLI) → Daemon (dockerd) → containerd → runc → Kernel (namespaces + cgroups). Daemon manages images, containers, networks, volumes.

**Q7: What are multi-stage builds?**
**A:** Multiple FROM statements in Dockerfile. Separate build and runtime environments. Final image contains only runtime dependencies, dramatically reducing size.

**Q8: Bridge vs Host network?**
**A:** Bridge: Isolated network, port mapping required, DNS resolution. Host: Shares host network, no isolation, best performance.

**Q9: How to reduce image size?**
**A:** Use multi-stage builds, alpine/distroless base images, minimize layers, remove cache, use .dockerignore.

**Q10: How does Docker achieve isolation?**
**A:** Linux namespaces (PID, NET, MNT, UTS, IPC, USER) for isolation. cgroups for resource limits (CPU, memory, disk I/O).

---

**🎉 Congratulations! You've completed the comprehensive Docker guide!**

This guide covers everything from basics to advanced concepts, perfect for interviews and real-world applications. Practice hands-on to solidify your understanding!

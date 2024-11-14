# Docker Interview Questions and Answers

## Basic Level

### 1. What is Docker and what problem does it solve?
**Answer:** Docker is a platform for developing, shipping, and running applications in containers. It solves several key problems:
- **Consistency:** "Works on my machine" problem
- **Isolation:** Applications run in isolated environments
- **Portability:** Run anywhere Docker is installed
- **Resource Efficiency:** Lighter than traditional VMs
- **Speed:** Quick to start and deploy

### 2. What's the difference between a Container and a Virtual Machine?
**Answer:** Key differences include:

| Feature | Container | Virtual Machine |
|---------|-----------|-----------------|
| OS | Shares host OS kernel | Runs full OS copy |
| Size | Lightweight (MBs) | Heavy (GBs) |
| Startup Time | Seconds | Minutes |
| Resource Usage | Low overhead | More resource intensive |
| Isolation | Process-level | Hardware-level |
| Performance | Near-native | Slower than native |

### 3. Explain the basic Docker architecture
**Answer:** Docker uses a client-server architecture with these main components:
- **Docker Daemon:** Background service running on host
- **Docker Client:** CLI tool to interact with Docker
- **Docker Registry:** Stores Docker images
- **Docker Objects:** Images, containers, networks, volumes

### 4. What is a Dockerfile?
**Answer:** A Dockerfile is a text file containing instructions to build a Docker image. Example:
```dockerfile
FROM ubuntu:20.04
RUN apt-get update
COPY . /app
WORKDIR /app
CMD ["./start.sh"]
```

## Intermediate Level

### 5. Compare different storage types in Docker
**Answer:**

| Storage Type | Use Case | Persistence |
|--------------|----------|-------------|
| Volumes | Preferred for persistent data | Persists after container removal |
| Bind Mounts | Development, host file access | Depends on host file |
| tmpfs | Temporary, sensitive data | Memory only, non-persistent |

### 6. What's the difference between CMD and ENTRYPOINT?
**Answer:**
```dockerfile
# CMD can be overridden by runtime arguments
CMD ["node", "app.js"]

# ENTRYPOINT sets fixed starting point, arguments are appended
ENTRYPOINT ["nginx"]
```
Key differences:
- CMD provides defaults for executing container
- ENTRYPOINT configures container to run as executable
- CMD can be overridden at runtime, ENTRYPOINT is harder to override

### 7. Compare different multi-stage build patterns
**Answer:**
```dockerfile
# Builder pattern
FROM node:16 AS builder
WORKDIR /app
COPY . .
RUN npm run build

# Final image
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```
Benefits:
- Smaller final image
- Separation of build and runtime environments
- Security improvement (fewer vulnerabilities)

### 8. What's the difference between COPY and ADD?
**Answer:**

| Feature | COPY | ADD |
|---------|------|-----|
| Basic File Copying | Yes | Yes |
| Remote URL Support | No | Yes |
| Auto-extract Archives | No | Yes |
| Recommended Use | Preferred for simple copies | Special cases only |

## Advanced Level

### 9. Compare different container networking modes
**Answer:**

| Network Mode | Use Case | Isolation |
|-------------|-----------|-----------|
| Bridge | Default, isolated network | Container-to-container |
| Host | Direct host network access | None |
| None | Complete network isolation | Total |
| Custom Bridge | User-defined networks | Configurable |

Example creating custom network:
```bash
docker network create --driver bridge my-network
docker run --network my-network myapp
```

### 10. Compare different image layers optimization techniques
**Answer:**

1. Layer Caching:
```dockerfile
# Bad
COPY . /app
RUN npm install

# Good
COPY package*.json /app/
RUN npm install
COPY . /app
```

2. Multi-stage builds:
```dockerfile
# Development dependencies
FROM node:16 AS deps
WORKDIR /app
COPY package*.json ./
RUN npm install

# Production image
FROM node:16-alpine
COPY --from=deps /app/node_modules ./node_modules
COPY . .
```

### 11. What's the difference between different container restart policies?
**Answer:**

| Policy | Description | Use Case |
|--------|-------------|----------|
| no | Never restart | Short-lived tasks |
| on-failure | Restart on error exit | Background services |
| always | Always restart | Critical services |
| unless-stopped | Restart unless manually stopped | Most services |

### 12. Compare different methods of container resource constraints
**Answer:**
```bash
# Memory constraints
docker run -m 512m myapp

# CPU constraints
docker run --cpus=".5" myapp

# Both
docker run -m 512m --cpus=".5" myapp
```

## Expert Level

### 13. Compare different container isolation techniques
**Answer:**

| Feature | Description | Use Case |
|---------|-------------|----------|
| Namespaces | Process isolation | Basic containment |
| Cgroups | Resource limits | Resource management |
| Capabilities | Permission control | Security |
| Seccomp | System call filtering | Security hardening |

### 14. Compare different Docker security scanning tools
**Answer:**

| Tool | Focus | Integration |
|------|-------|-------------|
| Trivy | Vulnerability scanning | CI/CD friendly |
| Clair | Static analysis | Self-hosted |
| Snyk | Dependencies | Developer-focused |
| Docker Scan | Basic scanning | Built-in |

### 15. What's the difference between different container logging methods?
**Answer:**

| Method | Pros | Cons |
|--------|------|------|
| json-file | Default, simple | Limited features |
| syslog | Central logging | Requires setup |
| journald | System integration | Host dependency |
| fluentd | Rich features | Complex setup |

Example:
```bash
# JSON file logging
docker run --log-driver json-file --log-opt max-size=10m myapp

# Syslog
docker run --log-driver syslog --log-opt syslog-address=udp://1.2.3.4:5678 myapp
```

### 16. Compare different Docker image tag strategies
**Answer:**

| Strategy | Example | Use Case |
|----------|---------|----------|
| Semantic | v1.2.3 | Release versions |
| Git SHA | git-abc123 | Development |
| Environment | prod-v1 | Deployment |
| Date | 2024-01-15 | Daily builds |

### 17. What's the difference between different health check implementations?
**Answer:**

1. Dockerfile HEALTHCHECK:
```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost/ || exit 1
```

2. Runtime health check:
```bash
docker run --health-cmd='curl -f http://localhost/ || exit 1' \
  --health-interval=30s \
  --health-timeout=3s \
  myapp
```

### 18. Compare different container metrics collection methods
**Answer:**

| Method | Pros | Cons |
|--------|------|------|
| stats API | Built-in | Basic metrics |
| cAdvisor | Detailed | Resource overhead |
| Prometheus | Comprehensive | Complex setup |
| DataDog | Full featured | Commercial |

Example collecting metrics:
```bash
# Basic stats
docker stats

# Prometheus endpoint
docker run -p 9090:9090 -v prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```
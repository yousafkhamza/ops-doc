# Kubernetes Interview Questions and Answers

## Basic Concepts

1. **Q: What is Kubernetes and what problems does it solve?**
   A: Kubernetes is a container orchestration platform that:
   - Automates container deployment and scaling
   - Manages application availability
   - Provides service discovery and load balancing
   - Handles storage orchestration
   - Supports declarative configuration

2. **Q: Explain core Kubernetes components**
   A: Main components include:
   - Control Plane:
     * API Server: API endpoint for cluster
     * etcd: Key-value store for cluster data
     * Scheduler: Assigns pods to nodes
     * Controller Manager: Maintains cluster state
   - Node Components:
     * kubelet: Node agent
     * kube-proxy: Network proxy
     * Container Runtime: (Docker, containerd)

## Kubernetes Objects

1. **Q: Explain the difference between Pod and Deployment**
   A:
   - Pod: Smallest deployable unit, contains one or more containers
   - Deployment: Manages ReplicaSets and provides declarative updates for Pods

2. **Q: Basic Pod manifest example?**
   A:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx-pod
   spec:
     containers:
     - name: nginx
       image: nginx:1.14.2
       ports:
       - containerPort: 80
   ```

## Services and Networking

1. **Q: Explain different Service types**
   A:
   - ClusterIP: Internal cluster communication
   - NodePort: Exposes port on each node
   - LoadBalancer: External load balancer
   - ExternalName: DNS CNAME record

2. **Q: Service manifest example?**
   A:
   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: my-service
   spec:
     type: ClusterIP
     selector:
       app: myapp
     ports:
     - protocol: TCP
       port: 80
       targetPort: 9376
   ```

## Storage

1. **Q: Explain PersistentVolume vs PersistentVolumeClaim**
   A:
   - PV: Cluster resource for storage
   - PVC: Request for storage by user
   Example PVC:
   ```yaml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: mysql-pvc
   spec:
     accessModes:
       - ReadWriteOnce
     resources:
       requests:
         storage: 10Gi
   ```

## Configuration and Security

1. **Q: What are ConfigMaps and Secrets?**
   A:
   - ConfigMap: Store non-confidential configuration
   - Secret: Store sensitive information (base64 encoded)
   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: app-config
   data:
     APP_COLOR: blue
     APP_MODE: prod
   ```

2. **Q: Explain RBAC in Kubernetes**
   A: Components:
   - Role/ClusterRole: Define permissions
   - RoleBinding/ClusterRoleBinding: Bind roles to users
   ```yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: Role
   metadata:
     name: pod-reader
   rules:
   - apiGroups: [""]
     resources: ["pods"]
     verbs: ["get", "list"]
   ```

## Advanced Concepts

1. **Q: Explain Horizontal Pod Autoscaling**
   A: HPA automatically scales pods based on metrics:
   ```yaml
   apiVersion: autoscaling/v2
   kind: HorizontalPodAutoscaler
   metadata:
     name: php-apache
   spec:
     scaleTargetRef:
       apiVersion: apps/v1
       kind: Deployment
       name: php-apache
     minReplicas: 1
     maxReplicas: 10
     metrics:
     - type: Resource
       resource:
         name: cpu
         target:
           type: Utilization
           averageUtilization: 50
   ```

2. **Q: What are StatefulSets used for?**
   A: Manage stateful applications with:
   - Stable network identities
   - Ordered deployment and scaling
   - Stable persistent storage

## Common Scenarios and Troubleshooting

1. **Q: Pod stuck in Pending state**
   A: Common causes and fixes:
   - Insufficient resources:
     ```bash
     kubectl describe pod <pod-name>
     # Check node resources
     kubectl describe nodes
     ```
   - Volume mount issues:
     * Verify PV/PVC status
     * Check storage class availability

2. **Q: Pod in CrashLoopBackOff**
   A: Troubleshooting steps:
   ```bash
   # Check pod logs
   kubectl logs <pod-name>
   # Check previous container logs
   kubectl logs <pod-name> --previous
   # Check pod events
   kubectl describe pod <pod-name>
   ```

3. **Q: Service not accessible**
   A: Debug checklist:
   - Verify service exists:
     ```bash
     kubectl get svc
     kubectl describe svc <service-name>
     ```
   - Check endpoints:
     ```bash
     kubectl get endpoints <service-name>
     ```
   - Verify pod labels match service selector
   - Test service DNS:
     ```bash
     kubectl run tmp-shell --rm -i --tty --image nicolaka/netshoot -- /bin/bash
     curl <service-name>.<namespace>.svc.cluster.local
     ```

4. **Q: Node NotReady**
   A: Troubleshooting steps:
   ```bash
   # Check node status
   kubectl describe node <node-name>
   # Check kubelet status
   systemctl status kubelet
   # Check kubelet logs
   journalctl -u kubelet
   ```

## Performance and Optimization

1. **Q: How to optimize resource usage?**
   A: Best practices:
   - Set resource requests and limits
   ```yaml
   resources:
     requests:
       memory: "64Mi"
       cpu: "250m"
     limits:
       memory: "128Mi"
       cpu: "500m"
   ```
   - Use HPA for scaling
   - Implement liveness and readiness probes

2. **Q: Explain Network Policies**
   A: Example of pod isolation:
   ```yaml
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

## Disaster Recovery

1. **Q: How to backup etcd?**
   A: Steps:
   ```bash
   # Create snapshot
   ETCDCTL_API=3 etcdctl snapshot save snapshot.db
   
   # Verify snapshot
   ETCDCTL_API=3 etcdctl snapshot status snapshot.db
   
   # Restore from snapshot
   ETCDCTL_API=3 etcdctl snapshot restore snapshot.db
   ```

2. **Q: Cluster backup strategy?**
   A: Key components to backup:
   - etcd data
   - Persistent volumes
   - Custom Resource Definitions
   - Secrets and ConfigMaps

## Advanced Troubleshooting

1. **Q: Debug networking issues**
   A: Tools and commands:
   ```bash
   # Test pod connectivity
   kubectl exec -it <pod-name> -- ping <target>
   
   # Check DNS resolution
   kubectl exec -it <pod-name> -- nslookup kubernetes.default
   
   # Network policy testing
   kubectl exec -it <pod-name> -- wget -qO- --timeout=2 http://service
   ```

2. **Q: Performance investigation**
   A: Monitoring tools:
   - Prometheus for metrics
   - Grafana for visualization
   - kubectl top pods/nodes
   ```bash
   # Get resource usage
   kubectl top pods --containers
   kubectl top nodes
   ```

----

# Enhanced Kubernetes Interview Questions and Answers

## Comparison-Based Questions

### 1. Compare Container Orchestration Platforms
| Feature | Kubernetes | Docker Swarm | OpenShift |
|---------|------------|--------------|-----------|
| Complexity | High | Low | Very High |
| Learning Curve | Steep | Gentle | Steeper |
| Scalability | Excellent | Good | Excellent |
| Enterprise Support | Community + Commercial | Limited | Red Hat |
| Auto-scaling | Yes | Limited | Yes |
| GUI Dashboard | Limited | No | Comprehensive |

### 2. Compare Kubernetes Service Types
| Feature | ClusterIP | NodePort | LoadBalancer | ExternalName |
|---------|-----------|----------|--------------|--------------|
| Accessibility | Cluster Internal | External via Node Port | External via LB | DNS CNAME |
| Default | Yes | No | No | No |
| Port Range | Any | 30000-32767 | Any | N/A |
| Use Case | Internal services | Dev/Test | Production | Service aliasing |

### 3. Compare Pod Controllers
| Feature | Deployment | StatefulSet | DaemonSet | Job |
|---------|------------|-------------|-----------|-----|
| Ordering | No | Yes | No | No |
| Scaling | Any number | Ordered | One per node | Completion based |
| Pod Identity | Random | Stable | Node-based | Batch |
| Use Case | Stateless apps | Stateful apps | Node services | Batch tasks |

## Scenario-Based Questions

### 1. High Availability Scenario
**Q: Design a highly available web application in Kubernetes**
**A: Implementation approach:**

```yaml
# Anti-affinity to spread pods across nodes
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 3
  template:
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - web-app
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: web-app
        image: nginx
        livenessProbe:
          httpGet:
            path: /healthz
            port: 80
        readinessProbe:
          httpGet:
            path: /ready
            port: 80
```

### 2. Scaling Scenario
**Q: Implement auto-scaling for a CPU-intensive application**
**A: Solution using HPA and resource limits:**

```yaml
# Deployment with resource requests
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cpu-app
spec:
  template:
    spec:
      containers:
      - name: cpu-app
        resources:
          requests:
            cpu: "500m"
          limits:
            cpu: "1000m"
---
# HPA configuration
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: cpu-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: cpu-app
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

### 3. Security Scenario
**Q: Implement pod security and network isolation**
**A: Multi-layer security approach:**

```yaml
# Pod Security Context
apiVersion: v1
kind: Pod
metadata:
  name: secure-pod
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
  containers:
  - name: secure-app
    securityContext:
      allowPrivilegeEscalation: false
      capabilities:
        drop:
          - ALL
---
# Network Policy
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: api-isolation
spec:
  podSelector:
    matchLabels:
      app: api
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 8080
```

### 4. Multi-Container Pod Patterns
**Q: Compare and implement different multi-container patterns**
**A: Examples of common patterns:**

```yaml
# Sidecar Pattern
apiVersion: v1
kind: Pod
metadata:
  name: sidecar-example
spec:
  containers:
  - name: main-app
    image: nginx
  - name: log-shipper
    image: fluent/fluentd
    volumeMounts:
    - name: logs
      mountPath: /var/log
---
# Ambassador Pattern
apiVersion: v1
kind: Pod
metadata:
  name: ambassador-example
spec:
  containers:
  - name: main-app
    image: app
  - name: ambassador
    image: ambassador
    ports:
    - containerPort: 9090
---
# Adapter Pattern
apiVersion: v1
kind: Pod
metadata:
  name: adapter-example
spec:
  containers:
  - name: main-app
    image: app
  - name: adapter
    image: adapter
    env:
    - name: FORMAT
      value: "prometheus"
```

### 5. Troubleshooting Scenario
**Q: Debug a service mesh connectivity issue**
**A: Systematic debugging approach:**

```bash
# 1. Check service mesh proxy
kubectl logs ${POD_NAME} -c istio-proxy

# 2. Verify service configuration
kubectl get virtualservice
kubectl get destinationrule

# 3. Test connectivity
kubectl exec -it ${POD_NAME} -c main-app -- curl localhost:15000/clusters

# 4. Check metrics
kubectl exec ${POD_NAME} -c istio-proxy -- pilot-agent request GET stats | grep upstream
```

### 6. Migration Scenario
**Q: Implement zero-downtime deployment**
**A: Rolling update strategy:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: zero-downtime-app
spec:
  replicas: 4
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    spec:
      containers:
      - name: app
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
        lifecycle:
          preStop:
            exec:
              command: ["/bin/sh", "-c", "sleep 10"]
```

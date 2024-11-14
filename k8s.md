### **Kubernetes Cluster Basics**

1. **What is Kubernetes?**
   - Kubernetes is an open-source platform for automating deployment, scaling, and managing containerized applications. It helps in efficiently running and orchestrating containers across multiple hosts.

2. **What are the key components of a Kubernetes cluster?**
   - **Master Node**: Manages the cluster and consists of the API server, scheduler, controller manager, and etcd.
   - **Worker Nodes**: Run application workloads and contain the kubelet, kube-proxy, and container runtime.
   - **etcd**: Stores configuration data and state information for the cluster.
   - **API Server**: Acts as the front-end for Kubernetes, exposing the Kubernetes API.
   - **Scheduler**: Assigns workloads to nodes based on resource availability.
   - **Controller Manager**: Maintains the cluster’s desired state by managing controllers like Node, Deployment, and Job controllers.

3. **How does Kubernetes manage desired state?**
   - Kubernetes uses the concept of a “desired state” and continuously works to ensure that the current state matches the desired state. Controllers constantly monitor resources and reconcile any differences between the current and desired states.

---

### **Kubernetes Pods and ReplicaSets**

4. **What is a Pod in Kubernetes?**
   - A Pod is the smallest deployable unit in Kubernetes and represents a single instance of a running process in a cluster. It can contain one or more containers sharing the same network and storage.

5. **What is a ReplicaSet, and why is it used?**
   - A ReplicaSet ensures a specified number of pod replicas are running at any given time. It’s used to maintain high availability and load balancing for applications by automatically scaling the number of replicas as needed.

6. **How do you scale a ReplicaSet?**
   - Use the `kubectl scale` command:
     ```bash
     kubectl scale replicaset my-replicaset --replicas=3
     ```
   - Or by editing the YAML definition:
     ```yaml
     replicas: 3
     ```

---

### **ConfigMaps and Secrets**

7. **What is a ConfigMap, and how is it used?**
   - A ConfigMap is an API object used to store configuration data as key-value pairs. It allows configuration data to be decoupled from container images, which improves reusability.
   - Example of creating a ConfigMap:
     ```bash
     kubectl create configmap my-config --from-literal=key1=value1
     ```

8. **How do you use a ConfigMap in a Pod?**
   - A ConfigMap can be used as environment variables or mounted as a file volume in a Pod.
   - Example in a Pod specification:
     ```yaml
     envFrom:
       - configMapRef:
           name: my-config
     ```

9. **What is a Secret, and how does it differ from a ConfigMap?**
   - A Secret is similar to a ConfigMap but is intended for sensitive data (like passwords, API keys, etc.). Secrets store base64-encoded data and require more secure handling.
   - ConfigMaps are for non-sensitive configurations, while Secrets are for secure information.

---

### **Kubernetes Services**

10. **What is a Service in Kubernetes, and what are the different types?**
    - A Service in Kubernetes provides a stable IP and DNS name for a set of Pods, allowing applications to communicate.
    - **Types of Services**:
      - **ClusterIP**: Accessible only within the cluster.
      - **NodePort**: Exposes the service on each node's IP at a static port.
      - **LoadBalancer**: Exposes the service externally using a cloud provider’s load balancer.
      - **ExternalName**: Maps a Service to an external DNS name.

11. **How do you create a Service in Kubernetes?**
    - Define it in YAML and apply it with `kubectl`:
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: my-service
      spec:
        selector:
          app: MyApp
        ports:
          - protocol: TCP
            port: 80
            targetPort: 9376
        type: ClusterIP
      ```
    - Apply it with:
      ```bash
      kubectl apply -f service.yaml
      ```

---

### **Namespaces and Resource Management**

12. **What is a Namespace, and why is it used?**
    - Namespaces are virtual clusters within a Kubernetes cluster, used to separate resources and organize objects. They allow for easier management and are essential for multi-tenant environments.

13. **How do you list all objects in a specific Namespace?**
    - Use the `-n` option with `kubectl`:
      ```bash
      kubectl get all -n <namespace>
      ```

14. **What are Resource Quotas and LimitRanges in Kubernetes?**
    - **Resource Quotas**: Set limits on resource usage (like CPU and memory) within a Namespace.
    - **LimitRanges**: Define default resource requests and limits for Pods or Containers in a Namespace, ensuring a fair distribution of resources.

---

### **Kubernetes Deployments**

15. **What is a Deployment in Kubernetes?**
    - A Deployment provides declarative updates to applications, managing ReplicaSets and Pods. It allows rolling updates, rollback capabilities, and scaling of applications.

16. **How do you perform a rolling update on a Deployment?**
    - Modify the Deployment configuration and apply changes:
      ```bash
      kubectl apply -f deployment.yaml
      ```
    - Kubernetes automatically handles rolling out updates, ensuring minimal downtime.

17. **How do you roll back a Deployment to a previous version?**
    - Kubernetes keeps a history of changes, so you can roll back with:
      ```bash
      kubectl rollout undo deployment my-deployment
      ```

---

### **Kubernetes Networking and Load Balancing**

18. **What is kube-proxy, and what is its role in a Kubernetes cluster?**
    - `kube-proxy` is a network proxy that runs on each node, managing network communication between Pods and Services. It maintains network rules and load balances requests within the cluster.

19. **How does Kubernetes handle networking?**
    - Kubernetes uses CNI (Container Network Interface) plugins (e.g., Calico, Flannel) to manage networking, providing each Pod with its own IP and enabling network isolation.

20. **How is load balancing achieved in Kubernetes?**
    - Services like `ClusterIP`, `NodePort`, and `LoadBalancer` provide load balancing within the cluster or externally. For example, `LoadBalancer` works with cloud provider load balancers to distribute traffic across Pods.

---

### **Storage and Persistent Volumes**

21. **What is a PersistentVolume (PV), and how is it different from a PersistentVolumeClaim (PVC)?**
    - **PersistentVolume (PV)**: Represents a piece of storage in the cluster, provisioned by an administrator.
    - **PersistentVolumeClaim (PVC)**: A request by a user for storage. PVCs are linked to a PV to provide storage to Pods.

22. **How do you mount a PersistentVolume in a Pod?**
    - Define a PVC and reference it in the Pod specification:
      ```yaml
      volumes:
        - name: my-pv
          persistentVolumeClaim:
            claimName: my-pvc
      ```

---

### **High Availability and Node Management**

23. **What is a DaemonSet, and when would you use it?**
    - A DaemonSet ensures that a particular Pod runs on all (or some) nodes. It’s used for system services like log collection, monitoring, or node agents that need to run on every node.

24. **How do you cordon and drain a node in Kubernetes?**
    - **Cordon**: Marks a node as unschedulable (new Pods won’t be assigned to it).
      ```bash
      kubectl cordon <node-name>
      ```
    - **Drain**: Evicts all Pods from a node to perform maintenance.
      ```bash
      kubectl drain <node-name> --ignore-daemonsets --force
      ```

---

### **Cluster Management**

25. **How do you perform a health check on a Kubernetes cluster?**
    - Use `kubectl get componentstatuses` to check the health of the cluster’s core components.

26. **How do you manage cluster authentication and authorization?**
    - **Authentication**: Typically managed through kubeconfig files, using certificate-based authentication.
    - **Authorization**: Kubernetes uses Role-Based Access Control (RBAC) to define permissions for resources within a cluster.

---

### **Monitoring and Logging**

27. **How do you monitor a Kubernetes cluster?**
    - Kubernetes supports integrations with monitoring tools like **Prometheus** and **Grafana** to monitor cluster performance, node metrics, and resource utilization.

28. **Where are logs stored in Kubernetes?**
    - **Pod Logs**: Accessed via `kubectl logs <pod-name>`.
    - **Cluster Logs**: Kubernetes integrates with centralized logging systems like **ELK (Elasticsearch, Logstash, Kibana)** or **Fluentd** to aggregate and analyze logs from multiple Pods.

---

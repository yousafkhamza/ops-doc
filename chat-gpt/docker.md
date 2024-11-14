### **Basic Docker Interview Questions**

---

#### **1. What is Docker, and why is it used?**
   - **Answer**: Docker is a platform for developing, shipping, and running applications in lightweight, isolated containers. It packages an application and its dependencies into a standardized unit, enabling consistent behavior across different environments, which is ideal for microservices, DevOps, and CI/CD practices.

---

#### **2. What is the difference between Docker images and containers?**
   - **Answer**: 
     - **Docker Image**: A read-only template with instructions for creating a Docker container. It includes the application and all its dependencies.
     - **Docker Container**: A runtime instance of an image that is isolated, lightweight, and can be started, stopped, or deleted.

---

#### **3. How does Docker handle networking, and what are the common network drivers?**
   - **Answer**: Docker supports several network drivers:
     - **Bridge** (default for containers on the same host),
     - **Host** (shares the host's network namespace),
     - **None** (no network configuration),
     - **Overlay** (for multi-host networking, used in Docker Swarm).
   - Docker also allows configuring custom networks using these drivers to isolate or connect containers as needed.

---

#### **4. How do you check running Docker containers?**
   - **Answer**: Use the following command to list active containers:
     ```bash
     docker ps
     ```
   - To view both running and stopped containers:
     ```bash
     docker ps -a
     ```

---

#### **5. How does `docker-compose` work, and why is it used?**
   - **Answer**: `docker-compose` simplifies multi-container Docker applications by using a `docker-compose.yml` file to define and manage services, networks, and volumes. It allows you to spin up the entire environment with a single command:
     ```bash
     docker-compose up -d
     ```

---

### **Scenario-Based Docker Questions**

---

#### **1. Scenario: Application Crashes or Fails to Start**

- **Issue**: A container fails to start, or an application inside the container crashes.
- **Troubleshooting Steps**:
  - Check container logs to identify the root cause:
    ```bash
    docker logs <container_id>
    ```
  - Examine Docker events for more context:
    ```bash
    docker events --filter container=<container_id>
    ```
- **Common Fixes**:
  - Adjust resource constraints (e.g., memory limits).
  - Review Dockerfile or image configuration for misconfigurations.
  - Ensure the application dependencies are properly installed in the Docker image.

---

#### **2. Scenario: Container Port Not Accessible**

- **Issue**: You cannot access the application running inside a container.
- **Troubleshooting Steps**:
  - Check the container’s exposed ports and ensure they’re mapped correctly:
    ```bash
    docker ps
    ```
  - Verify that the host firewall allows access to the specified ports.
- **Common Fixes**:
  - Explicitly expose the port in your `docker run` command:
    ```bash
    docker run -d -p 8080:80 <image_name>
    ```
  - If using `docker-compose`, define port mappings in the `docker-compose.yml` file:
    ```yaml
    services:
      web:
        image: nginx
        ports:
          - "8080:80"
    ```

---

#### **3. Scenario: Removing Unused Containers, Images, and Volumes**

- **Question**: How can you clean up unused Docker resources?
- **Answer**:
  - Remove stopped containers:
    ```bash
    docker container prune
    ```
  - Remove dangling images:
    ```bash
    docker image prune
    ```
  - Remove unused volumes:
    ```bash
    docker volume prune
    ```
  - Remove unused networks:
    ```bash
    docker network prune
    ```
  - Use `docker system prune` to remove all unused data, including stopped containers, dangling images, and unused volumes.

---

#### **4. Scenario: Persistent Data Storage in Docker**

- **Question**: How can you make data persist across container restarts?
- **Answer**: Use Docker volumes or bind mounts to persist data.
  - **Example**:
    ```bash
    docker run -d -v /host/path:/container/path <image_name>
    ```
  - Volumes can also be defined in a `docker-compose.yml` file:
    ```yaml
    services:
      app:
        image: mysql
        volumes:
          - db-data:/var/lib/mysql
    volumes:
      db-data:
    ```

---

#### **5. Scenario: Restarting a Stopped Container**

- **Question**: If a container has been stopped, how would you restart it without recreating it?
- **Answer**: Use the `docker start` command to restart a stopped container:
  ```bash
  docker start <container_id>
  ```

---

#### **6. Scenario: Differentiating Between `COPY` and `ADD` in a Dockerfile**

- **Question**: What is the difference between `COPY` and `ADD` in a Dockerfile?
- **Answer**:
  - **`COPY`**: Copies files from the host into the container and is more limited in functionality.
  - **`ADD`**: Has additional features like decompressing `.tar` files and can accept URLs to copy remote files.
- **Best Practice**: Use `COPY` unless you need `ADD`'s specific functionalities, as `COPY` is simpler and more predictable.

---

#### **7. Scenario: Dockerfile Optimizations for Faster Builds**

- **Question**: How can you optimize Docker builds for faster performance?
- **Answer**:
  - **Multi-Stage Builds**: Reduce image size by copying only the necessary artifacts to the final image.
    ```dockerfile
    FROM golang:1.17 AS builder
    WORKDIR /app
    COPY . .
    RUN go build -o app

    FROM alpine:3.13
    COPY --from=builder /app/app /app
    CMD ["/app"]
    ```
  - **Caching Layers**: Place frequently changing instructions (e.g., `COPY` commands) at the bottom of the Dockerfile to maximize cache reuse.
  - **Avoid Unnecessary Files**: Use `.dockerignore` to exclude files not needed in the build.

---

#### **8. Scenario: Managing Resources (CPU, Memory) for Containers**

- **Question**: How can you limit the resources available to a Docker container?
- **Answer**:
  - Limit CPU usage:
    ```bash
    docker run --cpus=".5" <image_name>
    ```
  - Limit memory usage:
    ```bash
    docker run -m 512m <image_name>
    ```

---

#### **9. Scenario: Docker Compose Service Dependency Order**

- **Question**: How can you ensure a `web` service starts only after the `db` service is ready?
- **Answer**:
  - Use `depends_on` in the `docker-compose.yml` file to define service dependencies:
    ```yaml
    services:
      db:
        image: postgres
      web:
        image: my-web-app
        depends_on:
          - db
    ```

---

#### **10. Scenario: Debugging Docker Container Issues with Interactive Mode**

- **Question**: How can you open a shell session in a running container for troubleshooting?
- **Answer**: Use the `docker exec` command to start an interactive shell in the container.
  ```bash
  docker exec -it <container_id> /bin/bash
  ```

### **11. Difference Between `CMD` and `ENTRYPOINT` in Docker**

Both `CMD` and `ENTRYPOINT` in a Dockerfile define the command that runs when a container starts, but they have distinct behaviors and use cases.

---

| Aspect                 | `CMD`                                         | `ENTRYPOINT`                                    |
|------------------------|-----------------------------------------------|-------------------------------------------------|
| **Purpose**            | Provides default command or arguments.        | Defines the primary, immutable command.         |
| **Override Behavior**  | Can be overridden by arguments at runtime.    | Arguments at runtime are passed as parameters to `ENTRYPOINT`. |
| **Usage**              | Used for default commands that are easily overridden (e.g., default flags). | Used when you want a fixed command that is always executed (e.g., an executable script). |
| **Syntax**             | Written in two forms: `CMD ["executable", "param1", "param2"]` or `CMD command param1 param2` | Similar forms, typically `ENTRYPOINT ["executable", "param1"]` |
| **Combination with `CMD`** | `CMD` provides additional arguments if used with `ENTRYPOINT`. | Must be used with `CMD` if arguments are needed; `ENTRYPOINT` is the main command. |

---

### **Examples**

1. **Using `CMD`**:
   ```dockerfile
   FROM ubuntu:latest
   CMD ["echo", "Hello World"]
   ```
   - Here, `CMD` will run `echo Hello World` by default, but it can be overridden at runtime:
     ```bash
     docker run my_image echo "Goodbye World"
     ```

2. **Using `ENTRYPOINT`**:
   ```dockerfile
   FROM ubuntu:latest
   ENTRYPOINT ["echo", "Hello from"]
   ```
   - If you run this image with additional arguments, they will be appended to `ENTRYPOINT`:
     ```bash
     docker run my_image "ENTRYPOINT"
     ```
     - Output: `Hello from ENTRYPOINT`

3. **Combining `ENTRYPOINT` and `CMD`**:
   ```dockerfile
   FROM ubuntu:latest
   ENTRYPOINT ["echo"]
   CMD ["Hello World"]
   ```
   - `CMD` provides the default argument for `ENTRYPOINT`. You can still override it:
     ```bash
     docker run my_image "Goodbye World"
     ```
     - Output: `echo Goodbye World`

---

### **Best Practices**

- **Use `ENTRYPOINT`** when you have a primary command that should always run (e.g., running a specific script).
- **Use `CMD`** when you want to provide default arguments or commands that can easily be changed at runtime. 
- **Combine `ENTRYPOINT` with `CMD`** to define a primary command with optional default arguments, offering flexibility for runtime overrides. 

This distinction allows you to make Docker containers that are both predictable and flexible based on application needs.
---

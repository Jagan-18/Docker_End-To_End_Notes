# Docker Swarm Setup and Management
---
# What is Docker Swarm?
- **Docker Swarm** is a **native clustering** and **orchestration** tool for Docker.It allows you to create and **manage a cluster of Docker nodes** (machines running Docker) and deploy applications across them.
- Docker Swarm enables you to manage multiple Docker containers across multiple machines as if they were a single virtual Docker engine.
- In **Swarm mode,** Docker nodes can work together to form a **cluster** and manage the deployment and scaling of services, ensuring high availability and fault tolerance.
---
### **Key Concepts in Docker Swarm:**
1. **Swarm**:
   - A Swarm is a group of Docker hosts (machines) that are joined together and managed as a single entity. The Swarm mode allows you to deploy applications across multiple Docker engines in a coordinated manner.
     
2. **Node**:
   - A **node** is an individual Docker engine (either a physical or virtual machine) that participates in the Swarm cluster. There are two types of nodes:
     - **Manager nodes**: These are responsible for managing the Swarm cluster and making decisions about what containers run where. They handle the orchestration of services.
     - **Worker nodes**: These are responsible for running application containers as instructed by manager nodes. Worker nodes do not make orchestration decisions.
       
3. **Service**:
   - A **service** is the definition of a containerized application in Swarm. It is a long-running task (like a web application) that can be scaled, updated, and managed by the swarm. Services are defined with a Docker image, and the Swarm orchestrates the deployment of containers (tasks) from the service.

4. **Task**:
   - A **task** is a container that is running as part of a service in the swarm. Tasks are distributed across the nodes, and Docker Swarm ensures that the correct number of tasks (containers) are running at all times.

5. **Stack**:
   - A **stack** is a collection of services that are deployed together. You can define a stack in a `docker-compose.yml` file and deploy it in a Swarm cluster using `docker stack deploy`.

6. **Overlay Network**:
   - An **overlay network** allows services to communicate with each other across different Docker hosts in a Swarm. This is required to ensure that containers running on different nodes can talk to each other.
---
### **Key Features of Docker Swarm:**
1. **Cluster Management**:
   - Docker Swarm simplifies cluster management by treating multiple Docker hosts as a single entity. It abstracts the complexity of managing a distributed system and provides a unified interface for interacting with the cluster.

2. **High Availability**:
   - Docker Swarm provides **high availability** by replicating services across multiple nodes. If one node fails, Docker Swarm will reschedule the tasks to available nodes in the cluster, ensuring your application remains available.

3. **Service Discovery**:
   - Swarm automatically provides **service discovery** for services running in the cluster. Each service can be accessed by its name, and Swarm handles the routing of requests to the appropriate container, even if it’s running on a different node in the cluster.

4. **Load Balancing**:
   - Swarm provides built-in **load balancing** to distribute incoming traffic across containers. Each service gets a **virtual IP** and Swarm ensures that traffic is routed to the appropriate container.

5. **Scaling**:
   - Services in Docker Swarm can be easily **scaled up** or **scaled down** by adjusting the number of replicas (tasks) running for a particular service. Swarm will ensure the desired number of replicas are running and balance them across the cluster.

6. **Rolling Updates and Rollbacks**:
   - Docker Swarm supports **rolling updates**, allowing you to update services in a controlled manner, updating one container at a time. If something goes wrong, you can also perform a **rollback** to the previous version.

7. **Security**:
   - Docker Swarm provides secure communication between nodes using **TLS encryption**. All communication between manager nodes, worker nodes, and the Docker API is encrypted by default.
---

### **Docker Swarm vs Kubernetes:**

While **Docker Swarm** and **Kubernetes** both offer container orchestration features, there are some key differences:

| Feature                |        **Docker Swarm**                           |            **Kubernetes**                        |
|------------------------|---------------------------------------------------|--------------------------------------------------|
| **Ease of Use**         | Simple to set up and use.                        | More complex to set up, but highly flexible.     |
| **Learning Curve**      | Easier to learn and use.                         | Steeper learning curve.                          |
| **Scaling**             | Supports scaling of services easily.             | Offers advanced scaling capabilities.            |
| **Service Discovery**   | Built-in service discovery.                      | Advanced service discovery with DNS integration. |
| **Load Balancing**      | Built-in load balancing.                         | Advanced load balancing with more features.      |
| **Use Case**            | Great for smaller clusters and simple use cases. | Ideal for complex, large-scale applications.     |
---------------------------------------------------------------------------------------------------------------------------------

- **Kubernetes** is often preferred for larger, more complex deployments, but **Docker Swarm** is simpler to set up and is perfect for smaller-scale, simpler use cases.

---
### **Advantages of Docker Swarm**

1. **Simplified orchestration**: Docker Swarm provides an easy-to-use interface for managing clusters of containers.
2. **Scalability**: Docker Swarm makes it easy to scale applications horizontally by adding more replicas or nodes.
3. **High availability**: It ensures that services are available even when nodes fail.
4. **Easy setup**: Docker Swarm can be set up with just a few commands, and you can easily convert a regular Docker host into a swarm node.
5. **Docker integration**: As part of the Docker ecosystem, Swarm integrates seamlessly with Docker CLI and Docker Compose.
---
**Disadvantages of Docker Swarm**:

1. **Limited Features**: Lacks advanced features like autoscaling, pod scheduling, and multi-cloud support, unlike Kubernetes.
2. **Basic Networking**: Less complex networking options compared to Kubernetes (e.g., ingress controllers, service meshes).
3. **Less Resource Control**: Limited options for resource allocation (CPU/Memory limits).
4. **Stateful Application Management**: Lacks native support for managing stateful applications (e.g., persistent volumes).
5. **Minimal UI**: Basic UI compared to Kubernetes' more advanced management tools.
6. **Scaling Limits**: May not scale as efficiently as Kubernetes for large applications.
7. **Fewer Integrations**: Limited third-party integrations for monitoring, logging, and CI/CD.
8. **Upgrade Challenges**: Updating and managing large clusters can be more difficult than in Kubernetes.
---
---
# How to Set Up Docker Swarm

### Prerequisites
- Docker installed on all nodes
- Access to multiple instances (machines or VMs)

## Setting Up Docker Swarm

### Steps

1. **Initialize Docker Swarm on the first node:** This command initializes the first node as the manager.

2. **Join worker nodes:** Use the worker join token on each worker node.

3. **Join manager nodes:** Use the manager join token on each additional manager node.

4. **Verify the setup:** Check the status of the Swarm and list all nodes to ensure they are correctly joined.

5. **Check networks:** View the networks available in the Swarm.

### Commands

```bash
# Initialize Docker Swarm on the first node
docker swarm init

# Join worker nodes
docker swarm join --token <worker-token> <manager-ip>:<manager-port>

# Join manager nodes
docker swarm join --token <manager-token> <manager-ip>:<manager-port>

# Check Swarm status
docker info

# List nodes
docker node ls

# Check networks
docker network ls
```
---
## Deploying Services
### Steps

1. **Deploy a sample service:** Create a service with 3 replicas and publish it on port 8000.

2. **Scale the service up and down:** Adjust the number of replicas for the service.

3. **Deploy service only on worker nodes:** Ensure the service runs only on worker nodes.

4. **Deploy a global service:** Run the service on every node.

### Commands

```bash
# Deploy a sample service
docker service create --name app1 --replicas 3 --publish 8000:80 kiran2361993/rollingupdate:v10

# Scale the service up
docker service scale app1=6

# Scale the service down
docker service scale app1=1

# Deploy service only on worker nodes
docker service create --name app1 --constraint node.role==worker --replicas 6 --publish 8000:80 kiran2361993/rollingupdate:v10

# Deploy a global service (runs on every node)
docker service create --name monitor --publish 9100:9100 --mode global prom/node-exporter

docker service create --name cadvisor --publish 8888:8080 --mode global google/cadvisor:latest
```
---
## Managing Services and Nodes
### Steps

1. **Check service tasks:** List the tasks of a service.

2. **Put a node in maintenance mode:** Drain the node to prevent it from running tasks.

3. **Reactivate a node:** Set the node back to active status.

### Commands

```bash
# Check service tasks
docker service ps app1

# Put a node in maintenance mode
docker node update <node-id> --availability drain

# Reactivate a node
docker node update <node-id> --availability active
```

## Load Balancing

To see load balancing in action, run the following command in a loop. This shows how requests are distributed across different nodes.

```bash
while true; do curl -sL http://<ip>:8000/ | grep -I 'IP-A'; sleep 1; done
```

## Visualizing the Cluster

Use Swarm Visualizer from GitHub to visualize your cluster. Access the visualizer at `http://<manager-ip>:8080`.

```bash
docker run -it -d -p 8080:8080 --name swarm-visualizer -e HOST=<manager-ip> -e PORT=<manager-port> dockersamples/visualizer
```
---
### **Conclusion**

**Docker Swarm** is a powerful and easy-to-use container orchestration tool for managing multi-container applications in a distributed environment. It is a great choice for small to medium-sized applications where simplicity, scalability, and high availability are key requirements. While it is less feature-rich than **Kubernetes**, it offers a more straightforward setup and can meet the needs of many containerized applications.
---

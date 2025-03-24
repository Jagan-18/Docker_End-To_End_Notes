# Docker Interview Questions
---
# 1. What is docker?
- Docker is an open source containerization platform.
- It enables developers to package applications into lightweight, portable containers.
- Containers include everything an application needs to run—code, libraries, dependencies, and configuration files—making it easy to move across different environments.
  
**Key Benefits of Docker:**

**• Portability:** Containers can run across any system that has Docker installed.

**• Lightweight:** Containers are much smaller than virtual machines (VMs) because they share the host system's OS kernel.

**• Scalability:** Docker makes it easy to scale applications by running multiple instances of containers.

---
 # 2. How Container are different from the virtual machines?  (OR)  Docker vs. Virtualization

| **Containers**                                                                         | **Virtual Machines**                                                  |
|----------------------------------------------------------------------------------------|-----------------------------------------------------------------------|
| 1. Lightweight: Containers Share the host OS kernel and run in isolated environments   | 1. Heavy weight: Each VM includes a full OS and runs on a hypervisor. |
| 2. More efficient; only the application and its dependencies are packaged.             | 2. Less efficient; each VM includes an entire OS.                     |
| 3. Better performance due to lower overhead.                                           | 3.  Slower performance due to the OS overhead.                        |
| 4. Faster Startup (Seconds)                                                            | 4. Slower startup (Minutes)                                           | 
| 5. Provides process-level isolation.                                                   | 5. Provides strong isolation with a separate OS.                      |

---

# 3.what is docker lifecycle ?
- In my case, the Docker lifecycle process typically starts with writing a Dockerfile. For example, if a developer comes to me with a requirement to containerize an application,

1. **Write the Dockerfile**:  
   - When a requirement comes in to containerize an application, I start by writing a **Dockerfile**. This file contains the instructions needed to set up the environment and run the application.

2. **Build the Docker Image**:  
   - After the Dockerfile is complete, I use the **docker build** command to create a **Docker image** from the Dockerfile.

3. **Run the Docker Container**:  
   - Once the image is built, I use the **docker run** command to create and run a **container** from the image. This starts the application inside the container.

4. **Push to a Registry**:  
   - Finally, if everything works well, I push the image to an external registry like **Docker Hub** or **Quay.io** using the **docker push** command. This allows the image to be shared and used for deployments.

- Docker images act as a instructions to build a docker container. It can be compared to a snapshot in a VM.
  
```bash
#  Builds Docker images from a Dockerfile.
docker build -t <image-name> .

#  To runs container from docker images (OR) Creates and starts a container from a Docker image.
docker run -d --name <container-name> <image-name>

-  -d: Runs the container in detached mode (in the background)

# push the container image to public/private regestries to share the docker images.
docker push <container-name>

# Lists all running containers.
docker ps

# Executes a command inside a running container. Commonly used to interact with the container's environment.
docker exec -it <container-name> <command>

- -it: Interactive terminal for running commands in the container.
```
---
# 4. Whar are the different types of docker components? or  Can you pls explane Docker Architecture and its components?
- Docker has a client-server architecture, which consists of several key components that work together to enable containerization.

### **Docker Architecture and Components**:

1. **Docker Client**:

   - The Docker Client is the interface used to interact with Docker. It sends commands to the Docker Daemon (server) to perform tasks like building, running, and managing containers. Common Docker commands are executed here, such as docker run, docker build, and docker push

2. **Docker Daemon (Docker Engine)**:
   - The Docker Daemon is responsible for managing Docker containers, images, networks, and volumes. It listens for Docker API requests and performs actions like building images, running containers, and managing Docker objects.
     
   - It is often referred to as docker engine and runs in the background.

3. **Docker Images**:
   - A Docker image is a read-only template that contains instructions for creating a container. Images are built from Dockerfiles.

4. **Docker Containers**:
   - Containers are running instances of Docker images. They are isolated environments where your application runs. They share the host OS kernel but are separate from each other.

5. **Docker Registry**:
   - A **Registry** is where Docker images are stored. **Docker Hub** is the most popular public registry. You can push and pull images from registries to share and use them.

6. **Docker Volumes**:
   - Volumes are used to store data persistently, outside of containers. If a container is stopped or deleted, the data in a volume stays safe and can be reused by other containers.

7. **Docker Networks**:
   - Networks allow containers to communicate with each other. Docker automatically creates a network for containers, but you can also create custom networks for better control.
---
### **Summary**:
- **Docker Client**: The tool you use to interact with Docker.
- **Docker Daemon**: The server that handles Docker commands and runs containers.
- **Docker Images**: The templates that containers are created from.
- **Docker Containers**: The running instances of Docker images.
- **Docker Registry**: A place to store and share Docker images.
- **Docker Volumes**: Persistent storage for container data.
- **Docker Networks**: Allow containers to communicate with each other.
---

# 5. What is the difference between COPY and ADD in a Dockerfile?

|        **`docker COPY`**                                    |         **`docker ADD`**                                                                      |
|-------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| 1. Copies files/folders from the host to the container.     | 1. Copies files/folders from the host to the container, but with additional extracts archives |
| 2. Does not support URls                                    | 2. It can handle remote URLs as source paths and download files during the build.             |
| 3. Does **not** automatically extract tar files.            | 3. Automatically extracts tar files when (such as .tar, .tar.gz, .zip,etc)into the container. |                                           
| 4. More Secure and predictable.                             | 4. Less Secure (Due to extraction & Remote fetch)                                             |


### **When to Use Each**:
- **Use `docker COPY`** when you only need to copy files from your local machine into the container. It’s simpler and more predictable.
- **Use `docker ADD`** when you need extra functionality, like copying from a remote URL or extracting tar files automatically.

---


































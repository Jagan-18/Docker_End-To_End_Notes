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
   
- Docker images act as a instructions to build a docker container. It can be compared to a snapshot in a VM.

4. **Push to a Registry**:  
   - Finally, if everything works well, I push the image to an external registry like **Docker Hub** or **Quay.io** using the **docker push** command. This allows the image to be shared and used for deployments.

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






































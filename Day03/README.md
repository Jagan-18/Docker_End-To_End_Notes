
## Table of Contents

1. [Introduction to Docker Containers](#introduction-to-docker-containers)
2. [Understanding Data Persistence](#understanding-data-persistence)
3. [Volumes vs Bind Mounts](#volumes-vs-bind-mounts)
4. [Practical Examples](#practical-examples)
   - [Using Volumes](#using-volumes)
   - [Using Bind Mounts](#using-bind-mounts)
5. [Docker Client and Daemon](#docker-client-and-daemon)
6. [Conclusion](#conclusion)

## Introduction to Docker Containers

Containers are stateless by nature, meaning if a container is deleted, all data within it is lost. This is where data persistence comes into play, ensuring your data remains intact even if the container is removed. We achieve this through **Volumes** and **Bind Mounts**.

## Understanding Data Persistence

**Persistence** means ensuring that even if a container is deleted, the data is not lost. There are two primary ways to achieve data persistence:

1. **Volumes**: Managed by Docker, ideal for data that needs to persist beyond the container's lifecycle.
2. **Bind Mounts**: Directly mounts a directory or file from the host system, useful for advanced scenarios.

## Volumes vs Bind Mounts

- **Volumes**:
  - Managed by Docker (easy backup, restore, etc.)
  - Stored in a part of the host filesystem which is managed by Docker.
  - Preferred method for data persistence.
  - Portable across systems
  - Easy to backup and restore using Docker commands

- **Bind Mounts**:
   - Managed by the user (requires manual care)
  - Maps a file or directory on the host to a file or directory in the container.
  - More complex but provides flexibility to interact with the host system.
  - Not portable (depends on host path)
  - No built-in backup, manual intervention needed
    
# Key Difference:
- **Volumes:** Managed by Docker, and stored in Docker's default location. Recommended for persistent data.
- **Bind Mounts:** Linked directly to a directory/file on the host system. Useful for development or sharing specific files.
---
# To using Docker volumes with first run the MongoDB container without a volume and later add a volume to ensure the data persists

### Practical Example: Using Volumes for MongoDB

1. **List existing volumes:** To see the available volumes on your system, use the following command:
```bash
   docker volume ls
```
2. **Create a new volume:** Create a volume to store MongoDB data. This volume will be used for persistent storage.
```bash
   docker volume create mongodb
```
3. **Run a MongoDB container without a volume:** Start MongoDB without a volume to see what happens when data is not persisted.
 ```bash
   docker run -rm -d --name mongodb  -p 27017:27017 mongo:latest
 ```
-  **--rm:** Automatically remove the container when stopped.
-  **-d:** Run the container in detached mode (in the background).
-  **-p 27017:27017:** Expose port 27017 for MongoDB.
  
4. **Check running containers:** Verify the container is running
 ```bash
   docker ps
 ```
5. **Insert some data into MongoDB:** Access the MongoDB shell inside the container and insert some data
```bash
   docker exec -it mongodb mongosh
```
 ```bash
 # Inside the MongoDB shell:
   > show dbs;

 # Insert some data
   >  use hello;
   >  db.hello.insertOne({ message: "Hello World" });
   > show dbs;
   > db.hello.find();
  > exit;
```

6. **Stop and start the container:** Stop and restart the container to see the data is lost
```bash
   docker stop mongodb
   docker ps -a
   docker start mongodb
   docker exec -it mongodb mongosh
```
```bash
# In the mongosh shell:
> show dbs;  # The 'hello' database will be missing
> db.hello.find();  # No data will be found (empty)
> exit;
```
```bash
 docker stop mongodb
```
## Create a Volume and Ensure Data Persistence
- Now, let’s use a volume to make sure the MongoDB data is stored persistently.

7. **Run MongoDB with the volume:**
- Run the MongoDB container again, but this time use the volume mongodb to store the data persistently:

 ```bash
   docker run --rm -d --name mongodb -v mongodb:/data/db -p 27017:27017 mongo:latest
```
- **-v mongodb:/data/db:** Mount the mongodb volume to /data/db inside the container, ensuring data is saved on the host machine.

8. **Check running containers:** Verify the MongoDB container is running with the volume:
```bash
 docker ps
```
9. **Insert data again:** Insert data into MongoDB again:
```bash
 docker exec -it mongodb mongosh
```
- Inside the MongoDB shell:
```bash
  > show dbs;
  > use hello;
  > db.hello.insertOne({ message: "Hello World" });
  > show dbs;
  > db.hello.find();
  > exit;
```
10. **Stop and start the container with the volume:** Stop the container and restart it to verify that the data persists:
```bash
   docker stop mongodb
   docker ps -a
   docker start mongodb
   docker exec -it mongodb mongosh
```
- In the mongosh shell:
```bash
  > show dbs;  # The 'hello' database should still exist
  > db.hello.find();  # The inserted data will persist
  > exit;
```
#### Key Takeaway:
- **Without volume:** MongoDB data will be lost when the container stops (as seen in step 6).
- **With volume:** Data persists even after the container stops or restarts (as shown in steps 7–10).
---

# Using Bind Mounts in Docker, based on your example:
---
#### **1. Run a Container without Network Access (Using `--network none`)** 
- we’ll run a container with no network access. This container won't be able to access any external resources, including the Docker daemon.

#### Command:
```bash
docker run --rm -d --name app1 --network none kiran2361993/troubleshootingtools:v1
```
- **--rm**: Automatically removes the container when stopped.
- **-d**: Runs the container in detached mode.
- **--name app1**: Names the container `app1`.
- **--network none**: This isolates the container from any network.

#### Check running containers:
```bash
docker ps
```
#### Inside the container, run Docker commands (to simulate an error):
- Now, we access the container and try running Docker commands, which should give us an error because the container doesn't have network access to communicate with the Docker daemon.
```bash
docker exec -it app1 bash
```
- Inside the container:
```bash
docker ps  # This will give an error: "cannot connect to the Docker daemon at unix:///var/run/docker.sock."
```
#### Exit and stop the container:
```bash
exit
docker stop app1
```
---
### **2. Run a Container with Docker Socket Mounted:** In this step, we run a container that has access to the Docker daemon by binding the Docker socket.

#### Command:
```bash
docker run --rm -d --name app1 -v /var/run/docker.sock:/var/run/docker.sock --network none troubleshootingtools:v1
```
- **-v /var/run/docker.sock:/var/run/docker.sock:** Mounts the Docker socket from the host to the container, allowing the container to communicate with the Docker daemon.
- **--network none:** Keeps the container isolated from external networks.

#### Check running containers:
```bash
docker ps
```
#### Inside the container, run Docker commands (now we can see the container details):
```bash
docker exec -it app1 bash
```
- Inside the container:
```bash
docker ps  # Now, this command should list containers because the container has access to the Docker daemon
docker images  # Lists images available on the host
```

#### Inspect the container:
- Although the container can access the Docker daemon, it cannot retrieve the container's IP address because the container is isolated from networks.
```bash
docker inspect app1  # We can't see any IP address for our container, as it's on a "none" network.
```
#### Exit the container:
```bash
exit
```
---

### **Key Concepts:**
1. **Without Network Access (`--network none`)**:
   - The container has no network access, and Docker commands inside the container fail because it can't access the Docker daemon or the network.

2. **With Docker Socket Mounted**:
   - By mounting the Docker socket (`/var/run/docker.sock`), the container can communicate with the Docker daemon, allowing it to interact with other containers and list images, containers, etc.
   - However, despite having Docker access, the container won’t have any networking information (like IP addresses) if it’s isolated on a "none" network.
---

---
### **Using Portainer with Docker**
**Portainer** is a lightweight management UI that allows you to manage Docker environments (including containers, images, volumes, networks, and more) through a simple web interface. It's a great tool to simplify Docker container management and make it more user-friendly.

### **1. Run Portainer with Docker**
- You can run Portainer as a Docker container. The most common setup is to use Portainer with the Docker socket mounted to give it access to Docker’s API.
#### Command:
```bash
docker run -d \
  -p 9000:9000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name portainer_data:/data \
  portainer/portainer-ce:2.11.1
```
#### Explanation:
- `-d`: Run in detached mode.
- `-p 9000:9000`: Exposes Portainer's web interface on port 9000.
- `-v /var/run/docker.sock:/var/run/docker.sock`: Mounts the Docker socket so Portainer can interact with the Docker engine.
- `--name portainer`: Assigns the name `portainer` to the container.
- `portainer/portainer-ce`: The official Portainer Community Edition image.

### **2. Access Portainer Web UI**
- Once Portainer is running, you can access the web UI from your browser:

- Open a web browser and go to: `http://localhost:9000` (or replace `localhost` with the Docker host IP if you're running it on a remote machine).

### **3. Set Up Portainer**
- When you first access the Portainer web UI, you will be prompted to create an admin user.
1. **Create an admin username and password**.
2. **Select the environment**: You will need to connect Portainer to your Docker environment. For most Docker setups, you can simply select "Local" to manage the local Docker instance.
3. **Click "Connect"** to start managing your Docker environment.

### **4. Using Portainer**
- Once logged in, you’ll have access to the following features:
- **Dashboard**: View your Docker environment at a glance (container stats, images, volumes, networks).
- **Containers**: Create, stop, start, restart, remove, and view logs of your containers.
- **Images**: Manage Docker images (pull, remove, etc.).
- **Volumes**: Create and manage volumes for container storage.
- **Networks**: Create and manage Docker networks.
- **Stacks**: Define and deploy multi-container applications with Docker Compose files.
- **Settings**: Configure Portainer settings (authentication, data storage, etc.).

### **5. Stopping Portainer**
- To stop the Portainer container:
```bash
docker stop portainer
```
### **6. Restarting Portainer**
- If you want to restart Portainer, use:
```bash
docker start portainer
```
### **7. Removing Portainer**
- If you need to remove Portainer (for example, to upgrade to a newer version), first stop the container:
```bash
docker stop portainer
```
- Then remove the container:
```bash
docker rm portainer
```
- You can also remove the image (if you no longer need it):
```bash
docker rmi portainer/portainer-ce
```
---
### **Summary:**
- **Portainer** provides a simple and intuitive web UI to manage Docker environments.
- You can run Portainer as a container with access to your Docker socket (`/var/run/docker.sock`) to manage containers, images, networks, volumes, and more.
- Once set up, you can easily manage Docker through a browser interface.
---
---
## Docker Client and Daemon

- **Docker Client**:
  - Sends commands to the Docker daemon using the Docker socket.
  - Receives responses from the Docker daemon.

- **Docker Daemon**:
  - Background service running on the host OS.
  - Manages Docker objects like images, containers, networks, and volumes.
  - Communicates with the Docker client.
---
## Conclusion

By understanding and using volumes and bind mounts, you can ensure data persistence in your Docker containers. This guide provides a solid foundation to start working with Docker and manage data effectively.

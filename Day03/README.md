
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
  - Managed by Docker.
  - Stored in a part of the host filesystem which is managed by Docker.
  - Preferred method for data persistence.
  - Portable across systems
  - Easy to backup and restore using Docker commands

- **Bind Mounts**:
  - Maps a file or directory on the host to a file or directory in the container.
  - More complex but provides flexibility to interact with the host system.
  - Not portable (depends on host path)
  - No built-in backup, manual intervention needed

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
-  --rm: Automatically remove the container when stopped.
-  -d: Run the container in detached mode (in the background).
-  -p 27017:27017: Expose port 27017 for MongoDB.
  
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

### Using Bind Mounts

1. **Run a container without network access**:

   docker run --rm -d --name troubleshootingtools --network none troubleshootingtools:v10


2. **Run a container with Docker socket mounted**:

   docker run --rm -d --name troubleshootingtools -v /var/run/docker.sock:/var/run/docker.sock --network none troubleshootingtools:v10


3. **Inspect the container**:

   docker inspect troubleshootingtools


## Docker Client and Daemon

- **Docker Client**:
  - Sends commands to the Docker daemon using the Docker socket.
  - Receives responses from the Docker daemon.

- **Docker Daemon**:
  - Background service running on the host OS.
  - Manages Docker objects like images, containers, networks, and volumes.
  - Communicates with the Docker client.

## Conclusion

By understanding and using volumes and bind mounts, you can ensure data persistence in your Docker containers. This guide provides a solid foundation to start working with Docker and manage data effectively. Happy Dockerizing!

# Why We Need Separate Utilization for Docker Data
- The default directory for Docker is /var/lib/docker.
- As you continue downloading images and generating logs, this directory will consume more space and eventually get busy.
- To prevent this, we can store all our Docker data in a separate directory.

# Steps To Create EBS Volume and Attaching it to the instance
Create EBS volume GP2 and atatch it to Instance

lsblk 

fdisk /dev/xvdf  

n > p > w > 

lsblk 

mkfs.ext4 /dev/xvdf1 

copy UUID Somewhere 

mkdir /dockerdata 

nano /etc/fstab 

Paste Like below

----
# Steps to Create an EBS Volume and Attach It to an EC2 Instance
---
**1. Create an EBS Volume:**
1.Log into the AWS Management Console:
 - Navigate to the EC2 Dashboard in the AWS Management Console.
  
**2.Go to the "Volumes" Section:**
 - On the left sidebar, under Elastic Block Store, click on Volumes.

**3. Click "Create Volume":**
 - Click the Create Volume button at the top of the page.
  
**4. Configure the Volume:**
 - **Volume Type:** Choose a volume type (e.g., General Purpose SSD (gp3), Provisioned IOPS SSD (io1), or Magnetic).
 - **Size:**  Specify the size of the volume (in GiB).
 - **Availability Zone:**  Select the same Availability Zone (AZ) as your EC2 instance to which you want to attach the volume. If your EC2 instance is in us-west-2a, select the same AZ for the EBS volume.
 - **Snapshot** : Optionally, you can create the volume from an existing snapshot if needed.
 - **Encryption:**  You can enable encryption for the volume, if required.
  
**5.Click "Create Volume":**
 - After configuring the volume, click the Create Volume button. This will create the EBS volume in the specified AZ.
---
# 2.Attach the EBS Volume to an EC2 Instance
**1. Select the Created Volume:**
 - In the Volumes section, select the volume you just created from the list.
   
**2. Click "Actions" and Select "Attach Volume":**
 - Click on the Actions button and choose Attach Volume.
   
**3. Choose the EC2 Instance:**
 - In the Attach Volume dialog box, you will need to select the EC2 instance that you want to attach the volume to.
 - Instance ID: Select the instance from the dropdown list.
 - Device Name: AWS will automatically suggest a device name (e.g., /dev/sdf), but you can modify it if needed.
   
**4.Click "Attach":**
 - Click the Attach button to attach the EBS volume to your EC2 instance.

---
![image](https://github.com/saikiranpi/Mastering-Docker/assets/109568252/1ad08bf8-593e-4579-921c-0f7d8938c8ee)
---
### Why We Need a Custom Network for Containers
1. **Default Network Limitations:**
   - In Docker's default bridge network, containers can communicate with each other using IP addresses but **not by container names**.
   - This makes it more difficult to manage communication between containers, especially when you need to reference them by names rather than IPs.

2. **Solution: Custom Network**
   - To allow containers to communicate using their **names**, we need to create a **custom network**.
   - Docker provides the `--network` flag to specify the network when running containers.

---
### Steps to Create and Use a Custom Network:

#### 1. **Create a Custom Network**:
   - Use the following command to create a custom network:
     ```bash
     docker network create myapp --driver bridge
     ```
     - This creates a network called `myapp` using the `bridge` driver, which is the default networking mode for Docker.

#### 2. **Inspect the Custom Network**:
   - To verify and inspect the details of the custom network you just created:
     ```bash
     docker network inspect myapp
     ```
     - This command will show details such as connected containers, network settings, and more.

#### 3. **Run Containers on the Custom Network**:
   - When running containers, you can attach them to the custom network using the `--network` flag:
     ```bash
     docker run --rm -d --name app3 -p 8001:80 --network myapp kiran2361993:troubleshootingtools:v1
     docker run --rm -d --name app4 -p 8002:80 --network myapp kiran2361993:troubleshootingtools:v1
     ```
     - The `--rm` flag removes the container when it stops.
     - The `--name` option assigns a name to each container, which is important for communication.
     - The `-p` flag maps the container's internal port (`80`) to the host machine's port (`8001` and `8002` respectively).
     - Both containers will be attached to the custom `myapp` network.

#### 4. **Verify Communication Between Containers Using Names**:
   - After running the containers, you can log into one of the containers using:
     ```bash
     docker exec -it app3 bash
     ```
     - This will open an interactive shell inside the container `app3`.

   - From within the container, you can now **ping** the other container using its **name**:
     ```bash
     ping app4
     ```
     - Since both containers are on the same custom network (`myapp`), they will be able to communicate with each other by container names, not just IPs.

---


# Using the HOST Network Mode

The HOST network mode means the container shares the host's IP address. This eliminates the need for port forwarding when running the container. For example, when using Prometheus with Node Exporter, it utilizes the host IP for metric collection, making it easier to access the metrics.

Example Command

docker run --rm -d --name node-exporter --network host prom/node-exporter

You can access the Node Exporter from the host's public IP on port 9100. To inspect the Docker image and understand the default settings, use the following command:

docker image inspect prom/node-exporter:latest

# Using the NONE Network Mode

The NONE network mode means the container has no network interfaces enabled except for a loopback device. This is useful for highly isolated containers that do not require any network access.

Example Command

docker run --rm -d --name isolated-container --network none busybox

This command runs a BusyBox container with no network connectivity, providing complete network isolation.




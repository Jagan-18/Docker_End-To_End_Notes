# Docker File part 01

This repository contains a Dockerfile for setting up an environment with Terraform and Packer on an Ubuntu base image. Below are the steps and commands used in the Dockerfile and for running the container.

## Dockerfile Overview

The Dockerfile performs the following tasks:
1. Sets up an Ubuntu base image.
2. Installs necessary tools (jq, net-tools, curl, wget, unzip, nginx, iputils-ping).
3. Downloads and installs specific versions of Terraform and Packer.
4. Runs nginx in the foreground.

# Dockerfile Contents
---
```bash
# Use the official Ubuntu as a base image
FROM ubuntu:latest

# Label the image for identification
LABEL name="saikiran"

# Set environment variables (recommended to pass these during runtime)
ENV AWS_ACCESS_KEY_ID=SDFSDFSDFSDFSDFSDFSDFSDF \
    AWS_SECRET_KEY_ID=SDSDSDSDSDSDSDSDSDSDSDSD \
    AWS_DEFAULT_REGION=US-EAST-1A

# Build arguments for Terraform and Packer versions
ARG T_VERSION='1.6.6' \
    P_VERSION='1.8.0'

# Install necessary dependencies
RUN apt update && \
    apt install -y jq net-tools curl wget unzip nginx iputils-ping && \
    apt clean && rm -rf /var/lib/apt/lists/*  # Clean up the apt cache to reduce image size

# Download and install Terraform and Packer
RUN wget https://releases.hashicorp.com/terraform/${T_VERSION}/terraform_${T_VERSION}_linux_amd64.zip && \
    wget https://releases.hashicorp.com/packer/${P_VERSION}/packer_${P_VERSION}_linux_amd64.zip && \
    unzip terraform_${T_VERSION}_linux_amd64.zip && \
    unzip packer_${P_VERSION}_linux_amd64.zip && \
    chmod +x terraform packer && \
    mv terraform /usr/local/bin/ && mv packer /usr/local/bin/ && \
    terraform version && packer version

# Set the working directory for the container
WORKDIR /usr/share/nginx/html

# Command to run Nginx in the foreground
CMD ["nginx", "-g", "daemon off;"]
```
---

## Commands to Build and Run the Container:

### Building the Docker Image
1. To build the Docker image with a specific name and tag:
```bash
docker build -t your_image_name:v3 .
```
2. To build the Docker image without using the cache(useful when you want to rebuild from scratch):
```bash
docker build --no-cache -t your_image_name:v3 .
```
3. To pass build arguments for Terraform and Packer versions:
```bash
docker build --build-arg T_VERSION=1.4.0 --build-arg P_VERSION=1.5.0 -t your_image_name:v3 .
```
4. To build the Docker image with plain progress output:
```bash
docker build --progress=plain -t your_image_name:v3 .
```
---
### Running the Container
1. Running the Container and Accessing the Shell: If you want to run the container interactively to test Terraform and Packer:
```bash
docker run -it your_image_name:v3 /bin/bash
```
- After that, inside the container, you can check the versions:
```bash
./terraform version
./packer version
```
2. Setting Environment Variables During Container Run: To pass AWS credentials (or other sensitive data) when running the container:
```bash
docker run -p 80:80 \
    -e AWS_ACCESS_KEY_ID=your_access_key \
    -e AWS_SECRET_KEY_ID=your_secret_key \
    -e AWS_DEFAULT_REGION=your_region \
    your_image_name:v3

#(OR) To pass environment variables when running the container:

docker run -p 80:80 -e AWS_ACCESS_KEY_ID=your_access_key -e AWS_SECRET_KEY_ID=your_secret_key -e AWS_DEFAULT_REGION=your_region your_image_name:v3
```
## Useful Docker Commands

### Adding a Tag
To add a tag to an existing image:
```bash
docker tag your_image_name:v3 your_image_name:latest
```
### Docker History

To check the history of the Docker image:
```bash
docker history your_image_name:v3
```

### Cleanup unused Docker objects
Remove all unused containers, networks, images (both dangling and unused), and optionally, volumes.
```bash
docker system prune
```
---
## Dockerfile Instructions Explained

- **ADD**: Adds files from your host system to the image. Useful for adding archives which need to be extracted.
- **COPY**: Copies files from your host system to the image. Used for copying local files and directories.
- **ENTRYPOINT**: Configures a container to run as an executable.
- **WORKDIR**: Sets the working directory inside the image where subsequent commands will be run from.

---
# ADD :
1. It can copy files and extracts archives
2. It can handle remote URLs as source paths and download files during the build.
3. It can automatically unpack compressed archives (such as .tar, .tar.gz, .zip, etc.) into the container.
4. Less Secure (Due to extraction & Remote fetch)
5. Syntax:
```bash
ADD <src> <dest>
```
- **<src>:** The source file, directory, or URL. It can be a local file or a URL.
- **<dest>:** The destination path inside the Docker image.
---
# COPY:
1. The COPY command copies files or directories from your local filesystem (host) into the Docker image at a specified path.
2. Does not support URls
3. More Secure and predictable
4. Does not extract any files
5. Syntax:
```bash
COPY <src> <dest>
```
- **<src>:** The source file or directory on your host machine (relative to the Docker build context).
- **<dest>:** The destination path inside the Docker image.
---

---
# What is the difference between ADD and COPY in a Dockerfile?
## 1. CMD 
1. It Provides default command to run in a container
2. Can be **overridden** by providing a command in **docker run**.
3. More flexible , allows changes at runtime
   
## 2. ENTRYPOINT
1. Defines the main executable that always (command) to run.
2. Cannot be fully overridden (can append args) unless **--entrypoint** is specified
3. More rigid, enforces execution of specific command.
---
# Example Scenarios:
1. **Use CMD:** If you are building a Docker image for an application that can be run with different parameters, you might want to provide default parameters but allow flexibility to the user.
```bash
CMD ["python", "app.py", "--help"]
```
- This will run the command **python app.py --help,** but the **user can override** it with a different command when running the container.

2.**Use ENTRYPOINT:** If you have an application that needs to always run a specific command (e.g., a server), you can use **ENTRYPOINT** to define the main executable.
```bash
ENTRYPOINT ["python", "app.py"]
CMD ["--help"]
```
- This ensures that python app.py is always the main command, and --help is the default argument. The user can override --help with something else when running the container, e.g., docker run <container> --version.

#### In Summary:
- CMD is primarily used for providing default arguments, which can be overridden.
-  ENTRYPOINT defines the main command to execute, which is typically **not overridden**. You can use both together to specify a command with default arguments that can be overridden at runtime.
---
---
# How to pass build parameters in Dockerfiles.?

- **Build parameters or build arguments** are values that can be passed at build time in a Dockerfile.
- These are useful for passing custom values or settings (like versions or configuration options) that can influence the Docker image being built."

1.**Define and Use Build Arguments in a Dockerfile:**
  - In a Dockerfile, build arguments are defined using the ARG instruction. You can optionally set a default value for the argument. The argument can then be used in commands like RUN, ENV, or COPY to configure the image based on that value.

#### For example: Dockerfile
```bash
# Define build argument with a default value
ARG MY_BUILD_ARG=default_value

# Use the build argument in the Dockerfile
RUN echo "The build argument is: $MY_BUILD_ARG"
```
2. **Pass Build Arguments During Build:**
- When building the Docker image, you can pass the value of the build argument using the --build-arg flag with the docker build command. This allows you to override the default value of the argument at build time."

#### For example:
```bash
docker build --build-arg MY_BUILD_ARG=my_custom_value -t my-image .
```
#### Important Notes:
- ***Build arguments are only available during the image build process.** Once the image is built, the arguments will not be available at runtime.
- **Arguments can have default values**, but they can be overridden by specifying the --build-arg during the build process.
- **ARG values cannot be used in FROM instructions** in a multi-stage build unless they are defined earlier in the Dockerfile.
---
---
# what is different between environment and arguments in docker.?
- **Build arguments (ARG)** are only passed during the **build process** and **cannot be passed when running the container**. They are only available at build time and are not accessible in the running container.
- Can have default values, but can be overridden at build time
- Passed using --build-arg during the build.
---
- **Environment variables (ENV)** are available both **during build time and runtime** and can be modified when running the container.
- Can have default values, but can be overridden at runtime
- Passed using -e with docker run.
---
---
# what is Dangling images  in docker.?
- Dangling images in Docker refer to images that are **not tagged or are unused by any container**. These images are often the result of previous builds or layers that were created but no longer have a purpose or reference.
- You can list dangling images by using the following command:
  ```bash
  docker images -f "dangling=true"
  ```
-This will remove all dangling images and free up disk space. and This will show you all the images that are untagged and not used.
```bash
docker image prune
```
(OR) - This will remove all dangling images. If you want to remove all unused images (not just dangling ones), you can use:
```bash
docker image prune -a
```
---
### Difference Between Dangling and Unused Images
- **Dangling images:** These are not tagged and not associated with any containers. They often represent layers from previous builds that are no longer in use.
- **Unused images:** These are images that are not currently in use by any container (i.e., no running container is using them). They might still be tagged.
---
---
# What is Multi-Stage Build in Docker (OR) Can you explain the concept of multi-stage builds in Docker?
-  Multi-stage builds allow you to use multiple **FROM** statements in a single Dockerfile. Each **FROM** begins a new build stage.
- This is especially useful for separating build-time dependencies (like compilers or testing tools) from runtime dependencies, helping you reduce the size of the final image by leaving unnecessary build tools behind.
---
#### How Multi-Stage Builds Work:
1. The **first stage (build stage)** includes all the dependencies required for building the application (e.g., compilers, libraries, and other build tools).
2. The **second stage (runtime stage)** is the final image that only includes the runtime environment needed to execute the app.

- In the final image, only the files explicitly copied from the build stage will be included, leaving behind all unnecessary build tools and intermediate files.

#### Example of Multi-Stage Build:
```bash
# Stage 1: Build Stage
FROM node:16 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Runtime Stage
FROM node:16-slim
WORKDIR /app
COPY --from=builder /app/build /app
RUN npm install --only=production
CMD ["node", "app.js"]
```
- **Stage 1 (builder):** The image starts with a full Node.js image (node:16), installs dependencies, and builds the app.
- **Stage 2 (runtime):** The final image is based on a smaller image (node:16-slim), and it only includes the built application and necessary runtime dependencies.
This approach reduces the final image size by omitting build tools and other unnecessary files, which are only needed during the build process.
---
# How can you reduce the size of your Docker image?
- Reducing the size of a Docker image is important for efficiency, faster builds, and better deployment speeds. Here are some strategies to minimize Docker image sizes:
  
1. **Use a smaller base image:** Start with minimal base images like alpine instead of ubuntu or debian to keep the image lightweight.
**Example:**
```bash
FROM alpine:latest
```
2. **Clean up unnecessary files:** After installing packages, remove cache files or temporary files to reduce the image size.
**Example:**
```bash
RUN apt-get clean && rm -rf /var/lib/apt/lists/*
```
3.**Use multi-stage builds:** In multi-stage builds, you can separate the build environment (which may need extra tools) from the final runtime environment, copying only the necessary artifacts.
4.**Minimize layers:** Combine related commands into one RUN statement to reduce the number of layers in your image.

**Example:**
```bash
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    && rm -rf /var/lib/apt/lists/*
```
5. **Remove unnecessary dependencies:** Avoid installing unnecessary tools or dependencies that aren’t needed in the final container.
---

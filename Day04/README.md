# Docker File part 01

This repository contains a Dockerfile for setting up an environment with Terraform and Packer on an Ubuntu base image. Below are the steps and commands used in the Dockerfile and for running the container.

## Dockerfile Overview

The Dockerfile performs the following tasks:
1. Sets up an Ubuntu base image.
2. Installs necessary tools (jq, net-tools, curl, wget, unzip, nginx, iputils-ping).
3. Downloads and installs specific versions of Terraform and Packer.
4. Runs nginx in the foreground.

### Dockerfile Contents


FROM ubuntu:latest

LABEL name="saikiran"

ENV AWS_ACCESS_KEY_ID=SDFSDFSDFSDFSDFSDFSDFSDF \
    AWS_SECRET_KEY_ID=SDSDSDSDSDSDSDSDSDSDSDSD \
    AWS_DEFAULT_REGION=US-EAST-1A

ARG T_VERSION='1.6.6' \
    P_VERSION='1.8.0'

RUN apt update && apt install -y jq net-tools curl wget unzip \
    && apt install -y nginx iputils-ping 

RUN wget https://releases.hashicorp.com/terraform/${T_VERSION}/terraform_${T_VERSION}_linux_amd64.zip \
    && wget https://releases.hashicorp.com/packer/${P_VERSION}/packer_${P_VERSION}_linux_amd64.zip \
    && unzip terraform_${T_VERSION}_linux_amd64.zip  && unzip packer_${P_VERSION}_linux_amd64.zip \
    && chmod 777 terraform && chmod 777 packer \
    && ./terraform version && ./packer version 

CMD ["nginx", "-g", "daemon off;"]


## Docker Commands

### Building the Docker Image

To build the Docker image with a specific name and tag:

docker build -t your_image_name:v3 .


To build the Docker image without using the cache:

docker build --no-cache -t your_image_name:v3 .


To pass build arguments for Terraform and Packer versions:

docker build --build-arg T_VERSION=1.4.0 --build-arg P_VERSION=1.5.0 -t your_image_name:v3 .


To build the Docker image with plain progress output:

docker build --progress=plain -t your_image_name:v3 .


### Running the Container

To run the container and check the versions of Terraform and Packer:

docker run -it your_image_name:v3 /bin/bash
./terraform version
./packer version


### Setting Environment Variables

To pass environment variables when running the container:

docker run -p 80:80 -e AWS_ACCESS_KEY_ID=your_access_key -e AWS_SECRET_KEY_ID=your_secret_key -e AWS_DEFAULT_REGION=your_region your_image_name:v3


## Useful Docker Commands

### Adding a Tag

To add a tag to an existing image:

docker tag your_image_name:v3 your_image_name:latest


### Docker History

To check the history of the Docker image:

docker history your_image_name:v3

### Cleanup unused Docker objects

Remove all unused containers, networks, images (both dangling and unused), and optionally, volumes.

docker system prune

## Dockerfile Instructions Explained

- **ADD**: Adds files from your host system to the image. Useful for adding archives which need to be extracted.
- **COPY**: Copies files from your host system to the image. Used for copying local files and directories.
- **ENTRYPOINT**: Configures a container to run as an executable.
- **WORKDIR**: Sets the working directory inside the image where subsequent commands will be run from.

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

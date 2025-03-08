# Google Distroless Images and Docker Multi-Stage Builds
---
---
# What are Distroless Images?
- Distroless images are Docker images that only contain your application and the libraries it needs to run.
- They do not include extra tools like a **package manager or shell.** This makes the images smaller and more secure.
- Distroless images provide a more secure, efficient, and lightweight way to run your applications in production.
  
---
**Why does the issue happen?** 
- Distroless images are very minimal, so they don't have a shell or other utilities like **mkdir or sh** that you might need in a regular Dockerfile.

-If case we are build image we will get one Error:
```bash
"run mkdir /app. It means we are unable to start container process: exec: "/bin/sh": stat /bin/sh: no such file or directory."
```
- This happens because **distroless images don't have a shell** like /bin/sh or /bin/bash. When Docker tries to run a command (like mkdir /app), it can't find a shell to execute the command, so it gives that error.

#### How to fix it:
1.**Don't use shell commands** in your Dockerfile. For example, don't try to use RUN mkdir /app. Instead, create the necessary directories in your build process (before you use a distroless image).

2.**Use a 2-stage build:** You can use a bigger image (like node:alpine) to build your app, and then copy everything to a distroless image for production. This way, you don't need the shell in your final image.

#### Example:
```bash
# Stage 1: Build the app
FROM node:alpine AS build
WORKDIR /app
COPY . .
RUN npm install

# Stage 2: Use distroless image for the final app
FROM gcr.io/distroless/nodejs
COPY --from=build /app /app
CMD ["app.js"]
```
**In this example:**
- The first part builds your app in a bigger image.
- The second part uses the distroless image with only your app.
  
#### Summary:
1. Distroless images are minimal and don’t include a shell.

2.The error occurs because you’re trying to use shell commands, but the image doesn’t have a shell.

3. Fix it by using a 2-stage build or avoiding shell commands in your Dockerfile.
   
---
**Why Use Distroless Images?**
1. **Reduced Attack Surface:**  Since Distroless images contain only your application and its dependencies, they eliminate unnecessary tools and libraries that could be exploited by attackers.
   
2.**Smaller Image Size:** By excluding unnecessary components, Distroless images are significantly smaller in size compared to traditional Linux distribution-based images. This results in faster image pulls and reduced storage costs.

ReferURL : https://github.com/GoogleContainerTools/distroless

---
---
# Docker Multi-Stage Builds: 

- Docker multi-stage builds allow you to create more efficient Dockerfiles by using multiple **FROM** statements.
  
- Each stage in the build process can have its own base image and set of instructions, enabling you to compile code, run tests, and package your application without including unnecessary build tools and dependencies in the final image.
  
- Multi-stage builds are a powerful feature in Docker that help reduce image size, increase security, and simplify Dockerfiles by separating the build process from the runtime environment.

---
#### Why Use Multi-Stage Builds?

1. **Smaller Image Size:** By using a multi-stage build, you can remove unnecessary build tools and dependencies from the final image, reducing its size.

2. **Cleaner Dockerfile:** Multi-stage builds allow you to handle complex workflows, like installing dependencies, building code, and packaging the app, in a clean and organized manner.
   
3. **Improved Security:** Only the necessary artifacts are copied to the final image, leaving behind build tools or sensitive data (e.g., credentials, source code).

4. **Separation of Concerns:** You can use different images (like a build image for compiling code) in different stages, and they won’t interfere with your final image.
   
---
# How Does a Multi-Stage Build Work?
- A multi-stage build uses multiple `FROM` statements. Each stage has its own base image. You can use the `COPY --from=<stage>` command to copy files from one stage to the next.

### Example of a Multi-Stage Build

Let’s look at an example where we build a Node.js application using a multi-stage Dockerfile:

```dockerfile
# Stage 1: Build the app
FROM node:16 AS build

WORKDIR /app

# Install dependencies and build the app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Set up the final runtime image
FROM gcr.io/distroless/nodejs:16

# Copy the app from the build stage
COPY --from=build /app /app

# Set the working directory
WORKDIR /app

# Command to run the app
CMD ["app.js"]
```

### Explanation of the Example:

1. **Stage 1 (Build the app)**:
   - We use the `node:16` image to install dependencies and build the application. This stage includes everything necessary for building and compiling your code, but we don’t want these tools in the final image.
   
2. **Stage 2 (Final Image)**:
   - We switch to the `gcr.io/distroless/nodejs:16` image, which is a minimal, distroless image. We then copy only the built app from the first stage, leaving behind any unnecessary build tools.

### Benefits of This Approach:
- **Build tools** like `npm` and `node` are only in the first stage, and they don’t end up in the final image.
- The final image is **much smaller**, containing only the necessary application files.
- The final image is **more secure** because it lacks unnecessary build dependencies.

---

### Another Example (with Java)

Here’s an example of a multi-stage build for a Java application:

```dockerfile
# Stage 1: Build the Java application
FROM maven:3.8.1-openjdk-11 AS build

WORKDIR /app

# Copy the pom file and install dependencies
COPY pom.xml .
RUN mvn dependency:go-offline

# Copy the source code and build the app
COPY src ./src
RUN mvn package

# Stage 2: Create the final image
FROM openjdk:11-jre-slim

# Copy the JAR file from the build stage
COPY --from=build /app/target/myapp.jar /app/myapp.jar

# Run the application
CMD ["java", "-jar", "/app/myapp.jar"]
```

### Explanation:
1. **Stage 1 (Build the Java app)**:
   - The build stage uses the `maven` image to download dependencies and compile the Java application into a JAR file.
   
2. **Stage 2 (Final Image)**:
   - The final stage uses a much smaller `openjdk:11-jre-slim` image, containing only the JAR file and the necessary Java runtime to run the application.

### Benefits:
- The final image contains only the **JAR file** and the Java runtime, with no build dependencies.
- The final image is much smaller and **more efficient**.

---

### Conclusion:

**Multi-stage builds** are a powerful feature in Docker that help reduce image size, increase security, and simplify Dockerfiles by separating the build process from the runtime environment.

### Key Takeaways:
- **Multiple `FROM` statements**: You can use several base images for different stages.
- **Copying between stages**: Use the `COPY --from=<stage>` command to copy files from one stage to another.
- **Smaller, more efficient images**: The final image contains only what’s necessary to run your application, not to build it.

- Multi-stage builds are highly recommended for production environments, especially when using **distroless images** to keep the final image small and secure.

---

Clone https://github.com/spring-projects/spring-petclinic.git

And follow by watching the full video here : https://youtu.be/CMO0MziP2yQ

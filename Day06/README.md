# Google Distroless Images and Docker Multi-Stage Builds
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
Docker Multi-Stage Builds

Docker multi-stage builds allow you to create more efficient Dockerfiles by using multiple FROM statements. Each stage in the build process can have its own base image and set of instructions, enabling you to compile code, run tests, and package your application without including unnecessary build tools and dependencies in the final image.

Clone https://github.com/spring-projects/spring-petclinic.git

And follow by watching the full video here : https://youtu.be/CMO0MziP2yQ

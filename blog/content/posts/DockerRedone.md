---
title: "docker demo"
date: 2025-06-14T18:14:00+03:00
tags: ["docker", "devops"]
description: "A no bs introduction to basic concepts of docker"
categories: ["devops"]
---

::: {align="center"}
<img src="../Docker/7bc2a85eae43dd6f942ec4093c0265356a7769c9.png" alt="Docker Logo" width='80px' height='80px'>
:::

::: {align="center"}
<h1>

DOCKER
</h1>
:::

### 1. Virtualization vs Containerization

Virtualization is basically what happens in VMs using hypervisor. Every time you want to put an OS it takes resources from the host's computer (Like memory, storage and CPU cores) to install and run on those resources.

Whereas Containerization is a lightweight alternative to virtualization. It allows multiple application to run isolated by using containers and sharing the same host's OS and kernel.
Containers package the application along with its dependencies, libraries and runtime.

| Feature | Virtualization (VMs) | Containerization |
|----|----|----|
| OS Per Instance | Full OS | Shares host OS kernel |
| Boot Time | Minutes | Seconds |
| Resource Usage | High (RAM, CPU, Storage) | Low |
| Isolation Level | Strong (hypervisor-based) | Medium (namespace-based) |
| Portability | Moderate (OS-dependent) | High (containers are portable) |
| Example Tools | VirtualBox, VMware, KVM | Docker, Podman, Kubernetes (for orchestration) |

### 2. Docker Images

\`\`\`dockerfile fold title:Sample-Dockerfile
\# pull a base image which gives all required tools and librarires to run your code
FROM openjdk:17-jdk-alpine

# create a folder where the app code will be stored

WORKDIR /app

# Copy the required files to the app folder

COPY src/Main.java .

# compile

RUN javac Main.java

# run the app, CMD can be overwritten while docker run

CMD \["java","Main"\]
\`\`\`

To build this file
`docker build -t java-app .`
- **build**: to tell we want to build an image using a dockerfile
- `-t java-app`: to give a name java-app to the image
- `.`: this is the path to the dockerfile

To list all images: `docker images`

### 3. Docker Containers

To run the image: `docker run`
Running the image will spin up a container which will be running the java-app

To check all running container: `docker ps`

### 4. Docker Networking

Docker provides several built-in **network types** to allow containers to communicate with each other and with the outside world.

Types:
1. **Bridge:** (Default) Allows container-to-container communication on the same host via a private virtual network.
2. **Host:** Shares the host's network stack directly; container has no network isolation from the host.
3. **Overlay:** Enables communication between containers on different Docker hosts in a Swarm cluster.
4. **Macvlan:** Gives containers a unique MAC address, making them appear as separate physical devices on the network.
5. **Custom Bridge:** User-defined bridge network with automatic DNS resolution between containers by name.
6. **None:** Completely disables networking for the container; full isolation.

| Network Type | Use Case | Can Talk to Host? | Can Talk to Internet? | Use with Docker Swarm? |
|----|----|----|----|----|
| Bridge | Default for single-host use | ✔️ (with port mapping) | ✔️ | ❌ |
| Host | High performance, no isolation | ✔️ | ✔️ | ❌ |
| None | Full isolation | ❌ | ❌ | ❌ |
| Overlay | Multi-host container communication | ✔️ | ✔️ | ✔️ |
| Macvlan | Container as full LAN device | ✔️ (like a physical device) | ✔️ (depends) | ❌ |

Create network: `docker network create twotiernetwork -d bridge`
This creates a bridge network named twotiernetwork. (Note: -d here is for driver as bridge not detatched mode.)

Run an image with connection to the network.
`docker run -d -p 5000:5000 --network twotiernetwork twotierbackend:latest`

### 4. Docker Volumes

If a container has to be removed to restart then it will also loose its data.
To prevent this we store its data in a volume.

Create a volume: `docker volume create mysql-data`

`docker run -d --name mysql -v mysql-data:/var/lib/mysql mysql`
- This binds the mysql-data volume to where the mysql container stores its data (/var/lib/mysql this can be found through internet for other apps).

### Docker-Compose

Docker Compose is a tool used to define and run multi-container Docker applications using a simple YAML file docker-compose.yml

Why?
It lets you start, stop, and manage multiple containers (like app + database + cache) with a single command (docker compose up), making development and deployment easier.

Basically,
Docker Compose = One file + one command (to run multiple containers together)

More on ***<a href="Docker-Compose" class="wikilink">Docker-Compose</a>***.

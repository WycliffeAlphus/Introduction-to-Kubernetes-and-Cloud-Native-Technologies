# Module 3

## Containers and Docker Basics

In Module 2, we explored Kubernetes’ core concepts—pods, nodes, and clusters—and used `kubectl` to interact with a cluster. Now, we’ll zoom in on **containers**, the building blocks that Kubernetes manages. We’ll also introduce **Docker**, the tool used to create and run containers. By the end of this module, you’ll understand what containers are, how they work, and how to run a simple container yourself.

**Learning Objectives:**

- Understand what containers are and why they’re used.
- Learn the basics of Docker as a container runtime.
- Run a simple container using Docker or an online sandbox.

## What Are Containers?

Imagine you’re moving to a new city, and you need to pack your belongings. Instead of dragging your entire house (furniture, appliances, and all), you pack just what you need into a lightweight, portable box. In the world of software, a **container** is like that box—it holds everything an application needs to run, such as:

- The application code (e.g., a web server like Nginx).
- Libraries and dependencies (e.g., specific versions of software).
- Configuration files (e.g., settings for the app).

Unlike a full virtual machine (VM), which is like moving the entire house (including the operating system), containers are lightweight because they **share the host’s operating system.** This makes them fast, portable, and efficient.

## Why Containers?

- **Portability:** A container runs the same way on your laptop, a cloud server, or a data center.
- **Efficiency:** Containers use fewer resources than VMs, so you can run more apps on the same server.
- **Consistency:** Developers and operations teams use the same container, avoiding “it works on my machine” problems.
- **Scalability:** Containers make it easy to run multiple copies of an app, which Kubernetes loves!

**Analogy:** Think of a container as a lunchbox. It holds your sandwich (the app), condiments (libraries), and a napkin (configuration). You can carry it anywhere, and it’s ready to use without needing a full kitchen.

## What is Docker?

**Docker** is the most popular tool for creating, running, and managing containers. It’s like the chef who prepares and serves those lunchboxes. Docker provides:

- A way to **build** containers by defining their contents in a `Dockerfile`.
- A **runtime** to run containers on your computer or server.
- A **registry** (Docker Hub) to store and share containers, like a public library for lunchbox recipes.

Kubernetes uses Docker (or other runtimes like containerd) to run containers inside pods. 

## How Containers Work with Kubernetes

In Kubernetes, containers don’t run alone—they’re housed in **pods** (from Module 2). Here’s how it fits together:

- You create a container (e.g., an Nginx web server) using Docker.
- Kubernetes wraps one or more containers in a pod, giving them a shared environment (like an IP address).
- Kubernetes places the pod on a node in the cluster, managing its lifecycle (starting, stopping, scaling).

For example, if you want to run a website, you’d:

1. Package the website’s code and server (e.g., Nginx) into a container using Docker.
2. Tell Kubernetes to create a pod with that container.
3. Kubernetes schedules the pod to a node and ensures it keeps running.

## Hands-On Exercise: Running Your First Container

Let’s try running a container! You have two options: use an online sandbox (no setup required) or install Docker on your computer. For beginners, the sandbox is easier, so we’ll start there.

**Option 1: Use Play with Docker (Online Sandbox)**

1. Go to **Play with Docker** (https://labs.play-with-docker.com/).
2. Click “Start” and log in (free with a Docker Hub account, or create one).
3. Click “Add New Instance” to get a terminal.
4. Run this command to start an Nginx web server container:

```bash
docker run -d -p 80:80 nginx
```


- **What it does:** 
 - `docker run` starts a container.
 - `-d` runs it in the background.
 - `-p 80:80` maps port 80 on the host to port 80 in the container.
 - `nginx` is the container image (a pre-built web server from Docker Hub).




5. Click the “80” link that appears in the Play with Docker interface to see the Nginx welcome page.
6. Check running containers:
```bash
docker ps
```
- **What to expect:** You’ll see your Nginx container listed with its ID and status.

7. Stop the container:
```bash
docker stop <container-id>
```

- Replace `<container-id>` with the ID from `docker ps`.



**Option 2: Install Docker Locally (Optional)**

If you prefer to try this on your computer:

1. Install **Docker Desktop** (https://www.docker.com/products/docker-desktop/) for Windows or macOS, or Docker for Linux (follow instructions for your OS).
2. Open a terminal and run the same `docker run -d -p 80:80 nginx` command.
3. Visit `http://localhost` in your browser to see the Nginx page.
4. Use `docker ps` and `docker stop` as above.

**Note for Beginners:** If you’re not ready to run commands, just follow along. The sandbox is free and requires no setup, so give it a try if you can! We’ll use .these skills in Kubernetes later.

## Understanding the Docker Workflow

Here’s what happened in the exercise:

- **Image:** The `nginx` image is a blueprint for the container, like a recipe for a lunchbox. It’s stored on Docker Hub.
- **Container:** Running `docker run` creates a container from the image, like making a lunchbox from the recipe.
- **Port Mapping:** The `-p 80:80` flag makes the container’s web server accessible on your browser.

In the next module, we’ll use Docker to create containers and deploy them to Kubernetes with `kubectl`.

## Quiz

1. What is a container?

 - A) A full virtual machine with its own operating system.

 - B) A lightweight package with an app and its dependencies.

 - C) A Kubernetes cluster component.


2. What does Docker do?

 - A) Manages Kubernetes clusters.
 - B) Creates and runs containers.
 - C) Replaces Kubernetes.


3. What command runs a container in Docker?

- A) `docker start`

- B) `docker run`

- C) `docker build`



**Answers:** 1-B, 2-B, 3-B

**Further Reading**

Docker Get Started: https://docs.docker.com/get-started/

Docker Hub: https://hub.docker.com/ (explore public images like `nginx`)

Kubernetes Documentation: Containers (https://kubernetes.io/docs/concepts/containers/)

## What’s Next?

In Module 4, we’ll combine what you’ve learned about containers and Kubernetes by **deploying applications with Kubernetes**. You’ll use `kubectl` to deploy a simple app (like the Nginx web server) to a Kubernetes cluster and see it in action.


[Proceed to Module 4](https://wycliffealphus.github.io/Introduction-to-Kubernetes-and-Cloud-Native-Technologies/modules/03-Deploy.html) →

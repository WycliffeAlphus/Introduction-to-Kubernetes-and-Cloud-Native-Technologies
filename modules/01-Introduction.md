# Module 1

By the end of this module, you’ll understand:

- What cloud-native computing is and why it matters.
- The role of Kubernetes in managing applications.
- The basics of containers and orchestration.

## What is Cloud-Native Computing?

Imagine you’re running a restaurant. In a traditional setup, you might have one chef cooking every dish from scratch, which takes time and can’t handle a sudden rush of customers. Now, picture a modern kitchen where dishes are prepped in advance, stored in containers, and quickly assembled to serve hundreds of customers efficiently. This is what **cloud-native** computing does for software.

**Cloud-native** refers to building and running applications that take full advantage of cloud environments (like AWS, Google Cloud, or Azure). Instead of running one big application on a single server, cloud-native apps are:

- **Broken into small pieces** called **microservices.**
- **Packaged in containers**, which are lightweight, portable units.
- **Managed dynamically** to scale up or down based on demand.
- **Resilient**, meaning they can recover from failures automatically.

The **Cloud Native Computing Foundation (CNCF)** is a community that supports tools like Kubernetes to make this possible. Think of CNCF as a recipe book for modern software kitchens

## Why Kubernetes?

Kubernetes (often called **K8s**, because there are 8 letters between "K" and "s") is an open-source platform that acts like the head chef in our restaurant analogy. It manages all the containers (dishes) to ensure they’re prepared, served, and cleaned up efficiently.
Here’s why Kubernetes is so important:

**Scalability:** It can handle one customer or a million by adding or removing containers.
**Portability:** Your app can run on any cloud provider or even on your own servers.
**Reliability:** If a container crashes, Kubernetes replaces it automatically.
**Automation:** It simplifies tasks like updating apps or balancing traffic.

Kubernetes was created by Google, based on their internal systems, and is now maintained by the CNCF. It’s used by companies worldwide to run applications at scale.

## What Are Containers and Orchestration?

Let’s break down two key concepts:

**Containers**

A container is like a lunchbox for your application. It contains everything your app needs to run—code, libraries, and settings—in a single, portable package. Unlike a full virtual machine, containers are lightweight because they share the host computer’s operating system.

For example:

- Imagine a web app (like a to-do list website). A container would package the app’s code, the web server (e.g., Nginx), and its configuration.
- You can run this container on your laptop, a cloud server, or anywhere else, and it behaves the same.

**Docker** is the most popular tool for creating and running containers. We’ll explore Docker in Module 3.

**Orchestration**

If containers are lunchboxes, **orchestration** is the system that manages them. Imagine you have 100 lunchboxes to serve to customers. You need someone to:

- Decide which kitchen (server) prepares each lunchbox.
- Ensure there are enough lunchboxes during a lunch rush.
- Replace any lunchboxes that get damaged.
- Route customer orders to the right lunchbox.

Kubernetes is the **orchestrator** that handles these tasks for containers. It decides where containers run, scales them based on demand, and keeps them healthy.

### How Does Kubernetes Work? A Simple Analogy

Think of Kubernetes as a city planner for a bustling city of containers:

**Pods:** The smallest unit in Kubernetes, like a single apartment housing one or more containers.

**Nodes:** The buildings (servers) where pods live.

**Cluster:** The entire city, with multiple nodes working together.

**Control Plane:** The city hall that manages everything, telling nodes what to do.



We’ll dive deeper into these in Module 2.

### Hands-On Exercise: Explore a Kubernetes Playground

To get a feel for Kubernetes, try this:

1. Visit **Play with Kubernetes** (https://labs.play-with-k8s.com/), a free online sandbox.
2. Follow the instructions to start a simple Kubernetes cluster.
3. Run the command `kubectl get nodes` to see the servers (nodes) in your cluster.

- What to expect: You’ll see a list of nodes, showing their status (e.g., "Ready").
- This command introduces `kubectl`, the tool you’ll use to interact with Kubernetes.



**Note:** You don’t need to install anything yet. If you’re not ready for hands-on, just read along—we’ll cover setup in later modules.

## Quiz

1. What is the main benefit of cloud-native applications?
- A) They run only on one server.
- B) They can scale and recover automatically.
- C) They don’t use containers.


2. What does Kubernetes do?
- A) Writes application code.
- B) Manages and orchestrates containers.
- C) Replaces cloud providers.


3. What is a container?
- A) A full virtual machine.
- B) A lightweight package with an app and its dependencies.
- C) A type of database.


**Answers:** 1-B, 2-B, 3-B

## Further Reading

- CNCF Cloud Native Interactive Landscape: https://landscape.cncf.io/
- Kubernetes Official Documentation: https://kubernetes.io/docs/home/
- Free edX Course: Introduction to Kubernetes (https://www.edx.org/course/introduction-to-kubernetes)

## What’s Next?
In Module 2, we’ll explore Kubernetes’ core concepts, like pods, nodes, and clusters, and start using `kubectl` to interact with a cluster. Get ready to dive into the heart of Kubernetes!

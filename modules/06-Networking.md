# Module 6

## Networking in Kubernetes

In Module 5, we learned how to manage applications with deployments and expose them using services. Now, we’ll dive into networking in Kubernetes, exploring how pods communicate with each other and how applications are accessed from outside the cluster. By the end of this module, you’ll understand Kubernetes networking basics and set up a service to expose an application.

## Learning Objectives:

- Understand how Kubernetes networking works for pod-to-pod communication.

- Learn about services and their role in networking.
Explore the basics of ingress for external access.

- Expose an application using a service in a Kubernetes cluster.

## Kubernetes Networking Basics

Kubernetes networking is like the road system in a city (our cluster analogy from Module 2). It ensures that every apartment (pod) can talk to others and that customers (users) can reach the services they need. Kubernetes has a unique networking model that simplifies communication.

**Key Networking Principles**:

- **Every pod gets a unique IP address**: Pods can talk to each other directly, like apartments with their own phone numbers.

- **Pods can communicate across nodes**: Kubernetes ensures pods on different servers (nodes) can reach each other without complex routing.

- **Services provide stable endpoints**: Since pod IPs change (pods are temporary), services act like a permanent address for your app.

- **External access is managed:** Kubernetes controls how outside users reach your apps, using services or ingress.

**Analogy**: Think of pods as food trucks (from Module 5) moving around the city. Each has a temporary phone number (IP address). A service is like a central hotline that connects customers to the nearest truck, no matter where it’s parked.

## Deep Dive into Services

We introduced services in Module 5 as a way to expose pods. Let’s explore them further. A **service** is a Kubernetes resource that groups pods (based on labels) and provides a stable IP address or DNS name to access them.

**Types of Services**:

- **ClusterIP** (default): Creates a virtual IP address for pods, accessible only within the cluster. Useful for internal communication (e.g., a database accessed by a web app).

- **NodePort:** Exposes the service on a specific port of each node (e.g., 30080). Useful for testing or sandbox environments, as we used in Module 5.

- **LoadBalancer:** Exposes the service externally via a cloud provider’s load balancer (e.g., AWS ELB). We’ll skip this for now, as it requires a cloud setup.

- **ExternalName:** Maps a service to an external DNS name (rarely used for beginners).

**How Services Work:**

- A service uses a **selector** (e.g., `app: nginx`) to find pods with matching labels.

- It routes traffic to those pods, balancing the load across them.

- Kubernetes’ internal DNS resolves service names (e.g., `nginx-service`) to the service’s IP.

For example, if you have three Nginx pods, a service ensures traffic is distributed to them, even if one pod is replaced.

## Introduction to Ingress

An **ingress** is a Kubernetes resource that manages external access to services, typically for HTTP/HTTPS traffic. It’s like a city gateway that directs web visitors to the right service based on the URL.

**Why Use Ingress?**

- Services like NodePort are simple but not ideal for production (e.g., random ports like `30080`).

- Ingress allows you to use standard ports (80 for HTTP, 443 for HTTPS) and route traffic based on domain names (e.g., `myapp.com`).

It supports features like SSL and path-based routing (e.g., `/api` to one service, `/web` to another).

**How Ingress Works:**

- You need an **ingress controller** (e.g., Nginx Ingress Controller) running in the cluster.

- An ingress resource defines rules (e.g., “send `myapp.com` to the `nginx-service`”).

- The controller handles the routing.

We’ll introduce ingress conceptually here, as setting up an ingress controller in a sandbox is complex. In a real-world scenario, you’d use a cloud provider or local setup like Minikube.

## Hands-On Exercise: Expose an Application with a Service

Let’s deploy an Nginx application (like in Module 5) and expose it using a **NodePort** service, reinforcing how services enable networking. We’ll use a free sandbox to keep it beginner-friendly.

**Step 1: Set Up Your Environment**

1. Go to **[Play with Kubernetes](https://labs.play-with-k8s.com/)** 

2. Start a Kubernetes cluster (takes 1-2 minutes).

3. Verify the cluster:

```bash
kubectl get nodes
```
**What to expect:** Nodes listed with status `Ready`.



**Step 2: Create a Deployment**

Create a deployment with two Nginx pods. Save this as `nginx-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

Apply it:

```bash
kubectl apply -f nginx-deployment.yaml
```

Check the deployment and pods:

```bash
kubectl get deployments
kubectl get pods
```

**What to expect:** A deployment with 2/2 replicas and two pods running.

**Step 3: Expose the Deployment with a Service**

Create a NodePort service. Save this as `nginx-service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

Apply it:
```bash
kubectl apply -f nginx-service.yaml
```

Check the service:
```bash
kubectl get services
```

**What to expect**: `nginx-service` with a NodePort (e.g., `80:30080/TCP`).

**Step 4: Access the Application**

In Play with Kubernetes, click the link like `http://<node-ip>:30080` to see the Nginx welcome page. 

To test pod-to-pod communication (simulating internal networking):

1. Start a temporary pod to act as a client:

```bash
kubectl run curl-pod --image=radial/busyboxplus:curl --restart=Never --rm -it -- sh
```

2. Inside the pod’s shell, run:curl nginx-service

**What to expect:** The Nginx welcome page HTML, showing the client pod can reach the service’s stable address.



Exit the shell with `exit`.

**Step 5: Clean Up**

Delete the resources:

```bash
kubectl delete -f nginx-deployment.yaml

kubectl delete -f nginx-service.yaml
```


- **What to expect**: Deployment, pods, and service removed (confirm with **kubectl get pods** and **kubectl get services**).

**Optional: Using `kubectl` Commands**

For a quicker test:

```bash
kubectl create deployment nginx-deployment --image=nginx --replicas=2
kubectl expose deployment nginx-deployment --type=NodePort --port=80 --target-port=80 --name=nginx-service
```

- Access and clean up as shown in step 5.

**Note for Beginners:** YAML files are the standard for Kubernetes, but the command-line approach is simpler for learning. Try the YAML method if possible—it’s what you’ll use in practice. If you’re not ready for hands-on, just follow along.

**Optional: Local Setup with Minikube**

If you want to try locally:

1. Install **Minikube** (https://minikube.sigs.k8s.io/docs/start/) and kubectl.

2. Start Minikube: `minikube start`.

3. Apply the YAML files or use the `kubectl create/expose` commands.

4. Access the service: `minikube service nginx-service --url`.

5. Clean up: Delete resources, then `minikube stop`.

**Troubleshooting Tips**

- If the service isn’t accessible, check the selector (`app: nginx`) matches the pod labels (`kubectl describe service nginx-service`).

- If pods aren’t running, use `kubectl describe pod <pod-name>` for errors.

- Ensure the NodePort (e.g., `30080`) is open in the sandbox.

# Quiz

1. What ensures pods can communicate across nodes?

- A) Kubernetes’ networking model.
- B) The control plane.
- C) Docker.


2. What does a ClusterIP service do?

- A) Exposes pods to the internet.
- B) Provides a stable IP within the cluster.
- C) Runs containers.


3. What is an ingress used for?

- A) Managing pod replicas.
- B) Routing external HTTP traffic to services.
- C) Storing data.



**Answers:** 1-A, 2-B, 3-B


## Further Reading

- Kubernetes Documentation: [Networking](https://kubernetes.io/docs/concepts/services-networking/networking-overview/)

- Kubernetes Documentation: [Services](https://kubernetes.io/docs/concepts/services-networking/service/)

- Ingress Basics: https://kubernetes.io/docs/concepts/services-networking/ingress/

## What’s Next?

In Module 7, we’ll explore **Storage in Kubernetes**, learning how to provide persistent storage for applications using volumes and persistent volume claims. You’ll set up storage for a simple app.

[Proceed to Module 7](https://wycliffealphus.github.io/Introduction-to-Kubernetes-and-Cloud-Native-Technologies/modules/07-storage.html) →
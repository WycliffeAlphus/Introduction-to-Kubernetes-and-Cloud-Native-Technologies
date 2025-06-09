# Module 5

## Kubernetes Resources: Pods, Deployments, and Services

In Module 4, we deployed a simple Nginx web server to a Kubernetes cluster using `kubectl`. Now, we’ll explore three key Kubernetes resources—**pods**, **deployments**, and **services**—to understand how Kubernetes manages and exposes applications at scale. You’ll learn how to run multiple copies of an app and make it accessible within the cluster. By the end of this module, you’ll be able to create a deployment and expose it with a service.

## Learning Objectives:

- Review pods and their role in Kubernetes.

- Understand deployments for managing multiple pods.

- Use services to make applications accessible.

- Deploy and expose an application using `kubectl`.

## Revisiting Pods

As we learned in Module 2, a **pod** is the smallest unit in Kubernetes, like an apartment housing one or more containers. Pods are temporary—Kubernetes creates, deletes, or replaces them as needed. For example, if a pod running your web server crashes, Kubernetes can start a new one.

**Key Points:**

- Pods contain one or more containers (e.g., an Nginx container).

- Each pod gets a unique IP address within the cluster.

- Pods are managed by higher-level resources like deployments.

**Analogy:** Think of a pod as a single food truck serving a dish (your app). It’s great for one-off tasks, but to serve a crowd, you need a better system—like a chain of food trucks.

## What Are Deployments?

A **deployment** is a Kubernetes resource that manages multiple pods to ensure your application runs reliably and at scale. It’s like a franchise manager who oversees a chain of food trucks, making sure there are enough trucks, they’re serving the right food, and broken trucks are replaced.

## Why Use Deployments?

**Scalability:** Run multiple copies (replicas) of a pod to handle more users.

**Updates:** Roll out new versions of your app without downtime.

**Reliability:** Automatically replace failed pods.

For example, if you want three copies of your Nginx web server running, a deployment ensures all three pods are active and restarts them if they crash.

## What Are Services?

A **service** is a Kubernetes resource that makes your application accessible, either within the cluster or to the outside world. Since pods are temporary and their IP addresses change, a service provides a stable address to reach them.

**Analogy:** Imagine your food trucks (pods) keep moving around the city. A service is like a phone number that customers can call to find the nearest truck, no matter where it is.

**Types of Services** (we’ll focus on one for now):

**ClusterIP:** Creates a stable IP address for pods within the cluster (default type).

**NodePort:** Exposes the app on a port of each node (useful for external access in sandboxes).

**LoadBalancer:** Exposes the app externally via a cloud provider (we’ll cover this later).

In this module, we’ll use a **NodePort** service to access our app, as it’s simple and works well in sandboxes.

## Hands-On Exercise: Deploy and Expose an Nginx Application

Let’s deploy an Nginx web server using a deployment and expose it with a service. We’ll use a free online sandbox to keep it beginner-friendly, with optional instructions for local setups.

**Step 1: Set Up Your Environment**

1. Go to [**Play with Kubernetes**](https://labs.play-with-k8s.com/) 

2. Start a Kubernetes cluster as guided (takes 1-2 minutes).

3. Verify the cluster:
```bash
kubectl get nodes
```

- What to expect: A list of nodes with status `Ready`.



**Step 2: Create a Deployment**

Create a deployment with three replicas of an Nginx pod using a YAML file. Save this as `nginx-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
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

Apply the deployment:

```bash
kubectl apply -f nginx-deployment.yaml
```

**What it does:**

- Creates a deployment named `nginx-deployment`.

- Runs three pods (replicas: 3), each with an Nginx container.

- The selector ensures the deployment manages pods with the label app: nginx.



Check the deployment:
```bash
kubectl get deployments
```

**What to expect:** See `nginx-deployment` with 3/3 replicas ready.

Check the pods:
```bash
kubectl get pods
```

**What to expect:** Three pods (e.g., nginx-deployment-xxx-xxx) with status Running.

**Step 3: Expose the Deployment with a Service**

Create a service to access the Nginx pods. Save this as `nginx-service.yaml`:

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
Apply the service:
```bash
kubectl apply -f nginx-service.yaml
```

**What it does:**
- Creates a NodePort service named nginx-service.

- Routes traffic to pods with the label app: nginx (matching the deployment).

- Exposes the service on port `30080` of each node.



Check the service:
```bash
kubectl get services
```

- **What to expect:** See `nginx-service` with a `NodePort` like `80:30080/TCP`.

**Step 4: Access the Nginx Application**

In Play with Kubernetes, a link like `http://<node-ip>:30080` will appear. Click it to see the Nginx welcome page (“Welcome to nginx!”). In Katacoda, visit the provided node IP with port `30080` (e.g., `http://<node-ip>:30080`).

**Step 5: Clean Up**

Delete the deployment and service:

```bash
kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
```

**What to expect:** The deployment, pods, and service are removed (confirm with `kubectl get pods` and `kubectl get services`).

**Optional: Using kubectl Commands Instead of YAML**

For a quicker approach (less common in production but good for learning):
```
kubectl create deployment nginx-deployment --image=nginx --replicas=3
kubectl expose deployment nginx-deployment --type=NodePort --port=80 --target-port=80 --name=nginx-service
```

- Then access and clean up as shown in Step 5.

**Note for Beginners:** YAML files are the standard way to manage Kubernetes resources, but the command-line approach is simpler for quick tests. Try the YAML method if you can—it’s what you’ll see in real-world scenarios. If you’re not ready for hands-on, just follow along.

**Optional: Local Setup with Minikube**

If you want to try locally:

1. Install [**Minikube**](https://minikube.sigs.k8s.io/docs/start/) and [**kubectl** ](https://kubernetes.io/docs/tasks/tools/).

2. Start Minikube: `minikube start`.

3. Apply the YAML files or use the `kubectl create/expose` commands above.

4. Access the service: `minikube service nginx-service --url` (opens the URL in your browser).

5. Clean up: Delete the deployment and service, then `minikube stop`.

## Troubleshooting Tips

- If pods aren’t running, use `kubectl describe pod <pod-name>` to check for errors (e.g., image pull issues).

- If the service doesn’t work, verify the selector (`app: nginx`) matches the pod labels.

- Ensure the node port (e.g., `30080`) is accessible in your sandbox.

## Quiz

**1. What does a deployment do?**

- A) Runs a single pod.
- B) Exposes pods to the internet.
- C) Manages multiple pods for scalability and reliability.


**2. What is the purpose of a service?**

- A) Creates pods.
- B) Provides a stable address to access pods.
- C) Manages cluster nodes.


**3. What does the `replicas` field in a deployment specify?**

- A) The number of nodes in the cluster.
- B) The number of pods to run.
- C) The number of services.



**Answers:** 1-C, 2-B, 3-B

## Further Reading

- Kubernetes Documentation: [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

- Kubernetes Documentation: [Services](https://kubernetes.io/docs/concepts/services-networking/service/)

- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

## What’s Next?

In Module 6, we’ll explore **Networking in Kubernetes**, learning how pods communicate within a cluster and how to expose applications using services and ingress. You’ll set up a simple app with proper networking.

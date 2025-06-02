# Module 4

## Deploying Applications with Kubernetes

In Module 3, we learned about containers and ran one using Docker. Now, we’ll take that knowledge and use Kubernetes to deploy a simple application, like a web server, to a cluster. You’ll use `kubectl`, the command-line tool from Module 2, to tell Kubernetes what to do. By the end of this module, you’ll know how to deploy an application and check that it’s running.

**Learning Objectives:**

- Understand how to deploy applications using kubectl.
- Launch a simple web server (Nginx) in a Kubernetes cluster.
- Check the status of your application and access it.

## Why Deploy Applications with Kubernetes?

Think of Kubernetes as the manager of a busy restaurant. In Module 3, we prepared a dish (a container) using Docker. Now, Kubernetes will serve that dish to customers by placing it in a pod, assigning it to a node, and ensuring it stays available. Deploying with Kubernetes means:

- **Automation:** Kubernetes handles starting, stopping, and restarting your app.

- **Scalability:** It can run multiple copies of your app to handle more users.

- **Reliability:** If your app crashes, Kubernetes replaces it automatically.

In this module, we’ll deploy a simple **Nginx** web server (a popular container from Module 3) to see Kubernetes in action.

## Using `kubectl` to Deploy Applications

`kubectl` is your tool for talking to the Kubernetes cluster, like giving instructions to the restaurant manager. We’ll use it to create a pod running an Nginx container and make it accessible.

**Key `kubectl` Commands for Deployment**

`kubectl run`: Creates a pod with a container (simple for beginners).

`kubectl get pods`: Lists all pods in your namespace (a “neighborhood” in the cluster).

`kubectl describe pod <pod-name>`: Shows details about a pod, like its status or errors.

`kubectl delete pod <pod-name>`: Removes a pod from the cluster.

We’ll also introduce **YAML files**, which are like recipes that describe what Kubernetes should do. While `kubectl run` is great for quick tests, YAML files are the standard way to define deployments in Kubernetes.

## Hands-On Exercise: Deploy an Nginx Web Server

Let’s deploy an Nginx web server to a Kubernetes cluster using a free online sandbox. No local setup is needed, but I’ll include optional instructions for those who want to try locally with Minikube.

**Step 1: Set Up Your Environment**

1. Go to **Play with Kubernetes** (https://labs.play-with-k8s.com/) or **Katacoda Kubernetes Playground** (https://www.katacoda.com/courses/kubernetes/playground).

2. Start a Kubernetes cluster as guided by the platform (takes 1-2 minutes).

3. Verify your cluster is ready:
```bash
kubectl get nodes
```

**What to expect:** A list of nodes (e.g., `node01`) with status `Ready`.



**Step 2: Deploy the Nginx Pod**

Run this command to create a pod with an Nginx container:

```bash
kubectl run my-nginx --image=nginx --restart=Never
```

**What it does:**

- `kubectl run` creates a pod named `my-nginx`.
- `--image=nginx` uses the Nginx container image from Docker Hub.
- `--restart=Never` ensures we create a single pod (not a managed set, which we’ll cover in Module 5).


Check that the pod is running:

```bash
kubectl get pods
```

- **What to expect**: You’ll see `my-nginx` with a status like `Running` or `Pending` (wait a moment if it’s `Pending`).

Get more details:

```bash
kubectl describe pod my-nginx
```

**What to expect**: Details like the pod’s node, IP address, and any errors.

**Step 3: Access the Nginx Web Server**

To see the Nginx welcome page, we need to expose the pod. For simplicity, we’ll use port forwarding:

```bash
kubectl port-forward pod/my-nginx 8080:80
```

- **What it does:** Maps port 8080 on your local machine to port 80 in the pod (where Nginx listens).

- In Play with Kubernetes, a link like `http://<node-ip>:8080` will appear. Click it to see the Nginx welcome page (“Welcome to nginx!”).

- In Katacoda, open a new browser tab and visit `http://localhost:8080`.

**Step 4: Clean Up**

Delete the pod to free resources:

```bash
kubectl delete pod my-nginx
```

**What to expect:** The pod disappears (confirm with `kubectl get pods`).

**Optional: Using a YAML File**

For a taste of how professionals deploy apps, here’s the same pod defined in a YAML file. Create a file called `nginx-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx
spec:
  containers:
  - name: nginx
    image: nginx
```

Apply it:

```bash
kubectl apply -f nginx-pod.yaml
```

- **What it does:** Creates the same pod as `kubectl run`.

- Check with `kubectl get pods`, then delete with `kubectl delete -f nginx-pod.yaml`.

**Note for Beginners:** If you’re not ready to run these commands, just follow along. The sandbox is free and browser-based, so try it if you can! YAML files will become more important in later modules.


## Optional: Local Setup with Minikube

If you want to try this locally:

Install **Minikube** (https://minikube.sigs.k8s.io/docs/start/) and **kubectl** (https://kubernetes.io/docs/tasks/tools/).


Start Minikube:`minikube start`


Run the same `kubectl` commands as above.

Access the pod via `kubectl port-forward` and visit `http://localhost:8080`.

Stop Minikube when done:`minikube stop`



### Troubleshooting Tips

1. If the pod’s status is `CrashLoopBackOff`, use `kubectl describe pod my-nginx` to check for errors (e.g., wrong image name).
2. If port-forwarding doesn’t work, ensure the pod is `Running` (`kubectl get pods`).
3. In sandboxes, clusters expire after a few hours, so restart if needed.

## Quiz

1. What does `kubectl run` do?
- A) Lists all pods in the cluster.
- B) Creates a pod with a container.
- C) Deletes a pod.


2. How do you check the status of pods?
- A) `kubectl get nodes`
- B) `kubectl get pods`
- C) `kubectl describe cluster`


3. What does port-forwarding do?
- A) Deletes a pod.
- B) Maps a local port to a pod’s port to access it.
- C) Scales the pod to multiple copies.



**Answers:** 1-B, 2-B, 3-B

## Further Reading

- Kubernetes Documentation: Running a Pod; [https://kubernetes.io/docs/tasks/run-application/run-single-instance/](https://kubernetes.io/docs/tasks/run-application/run-single-instance/)

- kubectl Cheat Sheet: [https://kubernetes.io/docs/reference/kubectl/cheatsheet/](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

- Nginx Docker Image: [https://hub.docker.com/_/nginx](https://hub.docker.com/_/nginx)

## What’s Next?

In Module 5, we’ll dive deeper into **Kubernetes Resources: Pods, Deployments, and Services**, learning how to manage applications at scale and make them accessible within a cluster. You’ll create a deployment to run multiple copies of an app and expose it properly.

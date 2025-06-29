# Module 7

## Storage in Kubernetes

In Module 6, we learned how Kubernetes networking enables pod communication and external access to applications. Now, we’ll explore **storage in Kubernetes**, focusing on how to provide persistent storage for applications that need to save data, like databases or file-based apps. By the end of this module, you’ll understand Kubernetes storage concepts and set up storage for a simple application.

### Learning Objectives:

- Understand why persistent storage is needed in Kubernetes.

- Learn about volumes and persistent volumes (PVs).

- Explore persistent volume claims (PVCs) for requesting storage.

- Attach storage to an application in a Kubernetes cluster.

### Why Persistent Storage in Kubernetes?

Kubernetes pods are temporary, like food trucks in our city analogy (from Module 5). If a pod crashes or is deleted, its data disappears unless it’s stored somewhere permanent. For example:

- A web server (like Nginx) might not need to save data, as it serves static files.

- A database (like MySQL) needs to save data (e.g., user records) even if the pod restarts.

**Persistent storage** ensures data survives pod lifecycles. Kubernetes provides mechanisms to attach storage to pods, similar to plugging a USB drive into a laptop.

Think of a food truck (pod) that needs to store ingredients. Without a fridge (persistent storage), ingredients are lost when the truck closes. Kubernetes connects the truck to a shared fridge to keep things safe.

### Kubernetes Storage Concepts

Kubernetes handles storage using three key resources:

#### 1. Volumes

**A volume** is storage attached to a pod, available to its containers. Unlike a pod’s default storage (which is temporary), a volume can persist beyond the pod’s life.

- **Types of Volumes:** Kubernetes supports many volume types, like local disks, cloud storage (e.g., AWS EBS), or network storage (e.g., NFS).

- **Example:** A volume might store a database’s data files, shared by containers in the pod.

#### 2. Persistent Volumes (PVs)

**A persistent volume (PV)** is a piece of storage in the cluster, like a reserved fridge in our city. It’s created by an administrator and represents storage capacity (e.g., 10GB on a cloud disk).

##### - Key Points:

- PVs are cluster-wide resources, not tied to a single pod.

- They specify details like size, access mode (e.g., read-write), and storage type.

- PVs can be pre-provisioned or dynamically created by Kubernetes.


#### 3. Persistent Volume Claims (PVCs)

**A persistent volume claim (PVC)** is a request for storage by a user or application, like a food truck reserving space in the fridge. Pods use PVCs to access PVs.

#### - How It Works:

- A PVC specifies requirements (e.g., 5GB of storage, read-write access).

- Kubernetes matches the PVC to a suitable PV.

- The pod mounts the PVC, giving its containers access to the storage.

**Analogy**

- PV: A fridge with fixed capacity (e.g., 10GB).

- PVC: A reservation for fridge space (e.g., 5GB).

- Volume: The actual shelf in the fridge used by the food truck (pod).

#### Hands-On Exercise: Attach Storage to an Application

Let’s deploy a simple application (a web server with a file-based guestbook) and attach persistent storage using a PVC. We’ll use a **Play with Kubernetes** sandbox, which supports basic storage for learning. Note that sandbox storage is limited, so we’ll simulate persistence with a hostPath volume (a local directory on a node).

**Step 1: Set Up Your Environment**

1. Go to **Play with Kubernetes** (https://labs.play-with-k8s.com/).

2. Start a Kubernetes cluster (takes 1-2 minutes).

3. Verify the cluster:

```bash
kubectl get nodes
```

- What to expect: Nodes listed with status `Ready`.

**Step 2: Create a Persistent Volume (PV)**

Create a PV using a hostPath (a directory on the node). Save this as `pv.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data
```

Apply it:
```bash
kubectl apply -f pv.yaml
```

#### - **What it does**:

- Creates a PV named `my-pv` with 1GB capacity.
- Uses `hostPath` to store data in `/data` on a node (for sandbox simplicity).

- `ReadWriteOnce` means one pod can read and write to it.


Check the PV:
```bash
kubectl get pv
```

- What to expect: `my-pv` with status `Available`.

**Step 3: Create a Persistent Volume Claim (PVC)**

Create a PVC to request storage. Save this as `pvc.yaml`:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Apply it:
```bash
kubectl apply -f pvc.yaml
```

#### What it does:
- Requests 1GB of storage.

- Kubernetes binds the PVC to the my-pv PV.

Check the PVC:
```bash
kubectl get pvc
```

What to expect: `my-pvc` with status `Bound` to `my-pv`.

**Step 4: Deploy an Application with Storage**

Deploy a simple web server that writes to the storage. Save this as `guestbook-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: guestbook-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: guestbook
  template:
    metadata:
      labels:
        app: guestbook
    spec:
      containers:
      - name: guestbook
        image: nginx
        volumeMounts:
        - name: storage
          mountPath: /usr/share/nginx/html
      volumes:
      - name: storage
        persistentVolumeClaim:
          name: my-pvc
```
Apply it:
```bash
kubectl apply -f guestbook-deployment.yaml
```

##### - What it does:

- Creates a deployment with one pod running Nginx.
- Mounts the PVC (`my-pvc`) to `/usr/share/nginx/html`, where Nginx serves files.
- Files written to this directory are stored in the PV.

Check the deployment and pod:

```bash
kubectl get deployments
kubectl get pods
```

- What to expect: One pod running.

**Step 5: Expose the Application**

Create a NodePort service. Save this as `guestbook-service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: guestbook-service
spec:
  type: NodePort
  selector:
    app: guestbook
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

Apply it:
```bash
kubectl apply -f guestbook-service.yaml
```

Access the service via the sandbox’s link (e.g., `http://<node-ip>:30080`).

**What to expect**: The Nginx welcome page, served from the mounted storage.

**Step 6: Test Persistence (Sandbox Limitation)**

In a real cluster, you could delete the pod and verify data persists. In Play with Kubernetes, hostPath storage may not persist across nodes, but this exercise shows the setup process. To simulate:

##### 1. Exec into the pod:
```bash
kubectl exec -it <pod-name> -- sh
```

Replace `<pod-name>` with the pod name from `kubectl get pods`.


2. Create a test file:
```bash
echo "Hello, Kubernetes!" > /usr/share/nginx/html/test.html
```

3. Access `http://<node-ip>:30080/test.html` to see “Hello, Kubernetes!”.

**Step 7: Clean Up**

Delete the resources:
```bash
kubectl delete -f guestbook-deployment.yaml
kubectl delete -f guestbook-service.yaml
kubectl delete -f pvc.yaml
kubectl delete -f pv.yaml
```

- What to expect: All resources removed.

**Optional: Local Setup with Minikube**

If you want to try locally:

1. Install [Minikube](https://minikube.sigs.k8s.io/docs/start/) and kubectl.

2. Start Minikube: `minikube start`.

3. Apply the YAML files above.

4. Access the service: `minikube service guestbook-service --url`.

5. Test persistence by deleting the pod (`kubectl delete pod <pod-name>`) and checking if `test.html` remains.

6. Clean up: Delete resources, then `minikube stop`.

#### Troubleshooting Tips

- If the PVC isn’t bound, check the PV’s status (`kubectl describe pv my-pv`).

- If the pod fails, verify the volume mount path (`kubectl describe pod <pod-name>`).

- In sandboxes, hostPath may not persist; this is normal for learning environments.

## Quiz

#### 1. Why do applications need persistent storage?

A) To save data beyond a pod’s lifecycle.To run containers.

B) To run containers.

C) To create services.


#### 2. What is a Persistent Volume Claim (PVC)?

A) A type of pod.

B) A request for storage that binds to a PV.

C) A network endpoint.


#### 3. What does a volume do in a pod?
A) Exposes the pod externally.
B) Manages pod replicas.
C) Provides storage for containers.



**Answers:** 1-A, 2-B, 3-C

## Further Reading

1. Kubernetes Documentation: [Storage](https://kubernetes.io/docs/concepts/storage/)

2. [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

3. [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)

## What’s Next?

In **Module 8**, we’ll explore the Cloud-Native Ecosystem and CNCF Projects, learning about tools like Prometheus and Envoy and how they complement Kubernetes. You’ll get a big-picture view of cloud-native technologies.


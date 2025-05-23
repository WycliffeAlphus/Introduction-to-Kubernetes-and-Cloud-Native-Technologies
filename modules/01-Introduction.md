# Module 1: Introduction to Cloud-Native and Kubernetes

## � What You’ll Learn
By the end of this module, you’ll be able to:
- Explain cloud-native principles in simple terms.
- Deploy a containerized app using Kubernetes.
- Navigate the ecosystem confidently.

*No prior experience needed—just curiosity!*


## 🌩️ What is Cloud-Native Computing?
### Traditional vs. Cloud-Native
| Traditional Apps               | Cloud-Native Apps              |
|--------------------------------|--------------------------------|
| Single monolithic codebase     | Small, independent microservices |
| Manual scaling                 | Auto-scales based on demand    |
| Failure can crash the system   | Self-healing with redundancy   |

**Key Traits of Cloud-Native Apps:**
- **Microservices**: Broken into small, reusable components.
- **Containers**: Lightweight, portable packaging (like lunchboxes for code).
- **Dynamic Management**: Automatically scales up/down.
- **CNCF**: The [Cloud Native Computing Foundation](https://cncf.io) maintains tools like Kubernetes.

## ⚡ Why Kubernetes?
Kubernetes (**K8s**) is the "orchestrator" for your cloud-native apps. Think of it as:
- **Air Traffic Control** for containers.
- **Self-Healing** system (replaces crashed containers automatically).
- **Multi-Cloud Ready**: Runs on AWS, Google Cloud, or your laptop.

### ⚡ Jargon Decoder
| Term           | Meaning                          |
|----------------|----------------------------------|
| **K8s**        | Short for Kubernetes (K + 8 letters + s). |
| **Orchestration** | Automating container deployment/scaling. |
| **Node**       | A server (physical/virtual) running containers. |


## 📦 Containers and Orchestration
### Containers (Like Lunchboxes)
- Package your app + dependencies (e.g., code, libraries).
- Lightweight (share the host OS, unlike bulky VMs).
- **Docker** is the most popular tool (we’ll cover it in Module 3).

### Orchestration (Kubernetes’ Job)
1. **Deploys** containers to nodes.
2. **Scales** up/down based on traffic.
3. **Heals** failed containers automatically.



## 🏙️ Kubernetes Analogy: City Planner
| Kubernetes Term | Real-World Equivalent          |
|-----------------|--------------------------------|
| **Pod**         | Apartment (houses 1+ containers) |
| **Node**        | Apartment Building (server)    |
| **Cluster**     | Entire City (group of nodes)   |
| **Control Plane** | City Hall (manages everything) |



## 🛠️ Hands-On: Your First Kubernetes Command
### Try It Now (No Installation Needed)
1. Visit [Play with Kubernetes](https://labs.play-with-k8s.com/).
2. Click "Start" → "Add New Instance".
3. Run:
   ```sh
   kubectl get nodes
   ```
   **Expected Output:**
   ```
   NAME        STATUS   ROLES    AGE   VERSION
   node-1      Ready    <none>   1m    v1.25.0
   ```

⚠️ **Troubleshooting**:
- If `kubectl: not found`, try [Katacoda’s Kubernetes Lab](https://www.katacoda.com/courses/kubernetes) as a backup.


## ✅ Quiz
1. What’s the main benefit of cloud-native apps?  
   **A)** They run on a single server.  
   **B)** They scale and recover automatically.  
   **C)** They avoid containers.  

2. What does Kubernetes do?  
   **A)** Writes application code.  
   **B)** Manages containers.  
   **C)** Replaces cloud providers.  

3. A container is:  
   **A)** A full virtual machine.  
   **B)** A lightweight app package.  
   **C)** A database.  

Answers: *1-B, 2-B, 3-B*


## 📚 Further Reading
- [CNCF Interactive Landscape](https://landscape.cncf.io/) - Explore cloud-native tools.
- [Kubernetes Docs](https://kubernetes.io/docs/home/) - Official guides.
- [edX Kubernetes Course](https://www.edx.org/course/introduction-to-kubernetes) - Free beginner course.


## 🔜 What’s Next?
In **Module 2**, you’ll:
- Deploy your first Pod (a running container).
- Use `kubectl` to debug apps.
- See how Nodes form a Cluster.

➡️ *Sneak Peek*:
```sh
kubectl run nginx --image=nginx  # Launches a Pod!
```

[Proceed to Module 2](./modules/02-Core-Concepts.md) →
```

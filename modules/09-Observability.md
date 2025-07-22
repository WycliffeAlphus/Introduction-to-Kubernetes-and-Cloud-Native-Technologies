# Module 9

## Observability and Monitoring

In Module 8, we explored the cloud-native ecosystem and CNCF projects like Prometheus and Fluentd. Now, we’ll dive into observability, the practice of understanding what’s happening in your Kubernetes applications by monitoring metrics and collecting logs. We’ll focus on Prometheus for monitoring and Fluentd for logging, with a hands-on exercise to see them in action. By the end of this module, you’ll know how to monitor your applications and troubleshoot issues.

## Learning Objectives:

  * Understand observability and its importance in Kubernetes.
  * Learn how Prometheus monitors metrics in a cluster.
  * Explore Fluentd for collecting and analyzing logs.
  * Set up a simple monitoring example in a Kubernetes sandbox.

## What is Observability?

Observability is like having a dashboard in a restaurant kitchen (our Kubernetes analogy from earlier modules) that shows how the food trucks (pods) are performing. It answers questions like: Are the trucks serving customers on time? Are any trucks broken? What’s causing delays?

In Kubernetes, observability involves three pillars:

  * **Metrics:** Numerical data about performance (e.g., CPU usage, request latency).
  * **Logs:** Text records of events (e.g., errors in an app).
  * **Traces:** Tracks requests through microservices (advanced, not covered here).

### Why Observability Matters:

  * Detect issues early (e.g., a pod using too much memory).
  * Debug problems by checking logs.
  * Ensure applications are reliable and performant.

**Analogy:** Observability is the kitchen manager who watches gauges (metrics) and reads chef notes (logs) to keep the restaurant running smoothly.

## Prometheus: Monitoring Metrics

Prometheus is a CNCF-graduated project for monitoring. It collects metrics from Kubernetes clusters and applications, stores them, and lets you query or visualize them (often with Grafana, a dashboard tool).

### How Prometheus Works:

  * Kubernetes components and apps expose metrics (e.g., HTTP requests per second).
  * Prometheus scrapes these metrics at regular intervals.
  * You can query metrics or set alerts (e.g., “alert if CPU usage exceeds 80%”).

**Example:** For an Nginx web server, Prometheus tracks request counts or resource usage.

**Analogy:** Prometheus is a health inspector checking food trucks’ speed and efficiency, alerting you if something’s wrong.

## Fluentd: Collecting Logs

Fluentd is a CNCF project for logging. It collects logs from pods, processes them, and sends them to a central system (e.g., Elasticsearch or a file).

### How Fluentd Works:

  * Pods generate logs (e.g., error messages from an app).
  * Fluentd runs as a pod, collecting logs from other pods.
  * Logs are forwarded for analysis or debugging.

**Example:** If an Nginx pod logs a “404 Not Found” error, Fluentd captures it for review.

**Analogy:** Fluentd is a note-taker recording every chef’s activity log for later review.

## Grafana: Visualizing Metrics

Grafana visualizes Prometheus metrics in dashboards, showing graphs like pod CPU usage or app response times. We’ll explore Grafana conceptually, as sandbox setups may limit its availability.

## Hands-On Exercise: Set Up Basic Monitoring

Let’s deploy an Nginx application and monitor it using Prometheus in Killercoda, a free, browser-based sandbox that supports Kubernetes and monitoring scenarios, replacing Katacoda. Due to sandbox constraints, we’ll focus on Prometheus and simulate Fluentd logging conceptually.

### Step 1: Set Up Your Environment

1.  Go to Killercoda’s Kubernetes Playground ([https://killercoda.com/playgrounds/scenario/kubernetes](https://killercoda.com/playgrounds/scenario/kubernetes)).
2.  Start the scenario, which provides a Kubernetes cluster (takes 1-2 minutes).
3.  Verify the cluster:
    ```bash
    kubectl get nodes
    ```
    **What to expect:** Nodes listed with status `Ready`.

### Step 2: Deploy an Nginx Application

Create a deployment and service for Nginx. Save this as `nginx-deployment.yaml`:

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
---
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
kubectl apply -f nginx-deployment.yaml
```

Check the deployment and service:

```bash
kubectl get deployments
kubectl get services
```

**What to expect:** Two Nginx pods and a NodePort service.

### Step 3: Deploy Prometheus

Killercoda provides pre-configured scenarios for Prometheus. Use a simplified setup:

Create a Prometheus deployment. Save this as `prometheus.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      containers:
      - name: prometheus
        image: prom/prometheus
        ports:
        - containerPort: 9090
---
apiVersion: v1
kind: Service
metadata:
  name: prometheus-service
spec:
  type: NodePort
  selector:
    app: prometheus
  ports:
  - port: 9090
    targetPort: 9090
    nodePort: 30090
```

Apply it:

```bash
kubectl apply -f prometheus.yaml
```

Check Prometheus:

```bash
kubectl get pods
kubectl get services
```

**What to expect:** A Prometheus pod and a NodePort service.

### Step 4: Explore Prometheus Metrics

1.  Access the Prometheus UI via Killercoda’s link (e.g., `http://<node-ip>:30090`).
2.  In the “Graph” tab, query `http_requests_total` to see Nginx request metrics.
    **What to expect:** A graph or table showing request counts.
3.  Try `container_cpu_usage_seconds_total` for pod CPU usage.
    **What to expect:** CPU metrics for your Nginx pods.

### Step 5: Simulate Log Exploration (Conceptual)

Fluentd setup is complex in Killercoda due to resource limits. Simulate logging:

1.  Exec into an Nginx pod:
    ```bash
    kubectl exec -it <pod-name> -- sh
    ```
    Replace `<pod-name>` with a pod name from `kubectl get pods`.
2.  Check Nginx logs:
    ```bash
    cat /var/log/nginx/access.log
    ```
    **What to expect:** Access logs (e.g., requests to the Nginx server).
3.  Exit: `exit`.

**Note:** In production, Fluentd would collect these logs centrally.

### Step 6: Clean Up

Delete the resources:

```bash
kubectl delete -f nginx-deployment.yaml
kubectl delete -f prometheus.yaml
```

**What to expect:** All resources removed.

**Note for Beginners:** Killercoda simplifies Prometheus setup for learning. If you can’t access the UI or prefer not to run commands, follow along to grasp the concepts. In a real cluster, Prometheus and Fluentd run as pods with more configuration.

## Optional: Local Setup with Minikube

For a local setup (advanced):

1.  Install Minikube ([https://minikube.sigs.k8s.io/docs/start/](https://minikube.sigs.k8s.io/docs/start/)) and `kubectl`.
2.  Start Minikube: `minikube start`.
3.  Install Prometheus using Helm:
    ```bash
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm install prometheus prometheus-community/prometheus
    ```
4.  Apply the Nginx YAML.
5.  Access Prometheus: `kubectl port-forward svc/prometheus-server 9090:9090`.
6.  Visit `http://localhost:9090` to query metrics.
7.  Clean up: `helm uninstall prometheus`, delete Nginx resources, `minikube stop`.

## Troubleshooting Tips

  * If Prometheus metrics are missing, ensure the Nginx service is accessible (`kubectl get services`).
  * Generate traffic to the Nginx NodePort (e.g., `http://<node-ip>:30080`) to populate logs.
  * Check Prometheus pod status: `kubectl get pods`.

## Quiz

1.  What is observability in Kubernetes?

    A) Managing pod networking.

    B) Monitoring and troubleshooting apps with metrics and logs.

    C) Deploying containers.

2.  What does Prometheus collect?

    A) Logs from pods.

    B) Metrics like CPU usage and request rates.

    C) Persistent storage data.

3.  What is Fluentd used for?

    A) Visualizing metrics.

    B) Collecting and aggregating logs.
    
    C) Managing deployments.

**Answers:** 1-B, 2-B, 3-B

## Further Reading

  * Prometheus Documentation: [https://prometheus.io/docs/introduction/overview/](https://prometheus.io/docs/introduction/overview/)
  * Fluentd Documentation: [https://docs.fluentd.org/](https://docs.fluentd.org/)
  * Grafana Documentation: [https://grafana.com/docs/grafana/latest/](https://grafana.com/docs/grafana/latest/)
  * Kubernetes Observability Tutorial: [https://kubernetes.io/docs/tasks/debug/debug-cluster/](https://kubernetes.io/docs/tasks/debug/debug-cluster/)

## What’s Next?

In Module 10, we’ll wrap up with Next Steps and Certification Prep, covering how to prepare for the KCNA exam, explore career paths, and continue your cloud-native journey.
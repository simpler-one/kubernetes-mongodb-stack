# Declarative MongoDB & Mongo-Express Deployment on Kubernetes

This repository provides production-minded, declarative Kubernetes configurations to orchestrate a persistent MongoDB database state coupled with a web-based administration console via Mongo-Express.

The architecture implements core Kubernetes features, separating raw database configurations (`ConfigMaps`) and encoded administrative credentials (`Secrets`) from execution deployments.

---

# 🏗️ Architecture Design & Resource Mapping

The stack relies on four key components working over an internal cluster network:

- **`mongo-secret.yaml` (Secret):**  
  Securely holds Base64 encoded administrative usernames and passwords, injecting them safely as environment components to the pods.

- **`mongo-configmap.yaml` (ConfigMap):**  
  Decouples internal configuration details by storing the exact internal DNS target pointer (`mongodb-service`).

- **`mongo.yaml` (Deployment & Service):**  
  Deploys a single MongoDB container pod instance and routes internal cluster component traffic directly via an internal `ClusterIP` service.

- **`mongo-express.yaml` (Deployment & Service):**  
  Mounts a web-based UI administration layer connected via environment variable injection, exposing the application externally using a `LoadBalancer` service mapped onto port `8081`.

---

# 🚀 Step-by-Step Cluster Execution

## Prerequisites

- A running Kubernetes cluster such as:
  - Minikube
  - Kind
  - Docker Desktop Kubernetes Engine

- `kubectl` CLI tool initialized and configured to communicate with your cluster.

---

## 1. Initialize Cluster Configurations (Secrets & ConfigMaps)

Always apply configurations and secret storage files **before** deploying application layers so that pods can consume the required values during startup.

```bash
# Apply the encoded root administrative credentials
kubectl apply -f mongo-secret.yaml

# Apply the environment tracking mapping properties
kubectl apply -f mongo-configmap.yaml
```

---

## 2. Deploy the Data Persistence Core (MongoDB)

Launch the MongoDB deployment and its internal networking service.

```bash
kubectl apply -f mongo.yaml
```

---

## 3. Deploy the UI Admin Dashboard (Mongo-Express)

Expose the web-based MongoDB administration interface.

```bash
kubectl apply -f mongo-express.yaml
```

---

# 🔍 Verification & Access Strategy

## Check Component Status

Verify that all deployments, pods, and services are running correctly.

```bash
kubectl get all
```

---

# 🌐 Accessing the Web Dashboard Interface

Depending on your Kubernetes environment, use one of the following methods.

---

## A. If Using Minikube

Minikube can automatically expose the `LoadBalancer` service to your local machine.

```bash
minikube service mongo-express-service
```

---

## B. If Using Docker Desktop or Generic Kubernetes Environments

If your environment does not automatically expose external load balancer endpoints, use port forwarding:

```bash
kubectl port-forward svc/mongo-express-service 8081:8081
```

Then open your browser and navigate to:

```text
http://localhost:8081
```

---

# 🔐 Administrative Login Credentials

- **Username:** `admin`
- **Password:** `pass`

Database root connectivity is validated internally using Kubernetes Secrets and ConfigMaps.

---

# 🧹 Tear Down & Resource Cleanup

To completely remove all Kubernetes resources created by this project:

```bash
kubectl delete -f mongo-express.yaml
kubectl delete -f mongo.yaml
kubectl delete -f mongo-configmap.yaml
kubectl delete -f mongo-secret.yaml
```


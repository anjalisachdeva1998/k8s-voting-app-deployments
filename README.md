# Kubernetes Deployment-Based Setup for a Sample Distributed App

This repository contains a Deployment-based Kubernetes (K8s) setup for a sample distributed application. 

---

## 🎯 Project Objective

- Run a distributed application using Kubernetes Deployments
- Enable inter-component communication via Services
- Keep the setup minimal while introducing self-healing behavior and versioning

---

## 🏗️ Application Architecture

The application consists of multiple independent components that communicate with each other via internal services.

### Components

1. **Vote App**
   - Frontend application where users cast votes
   - Writes vote data to Redis

2. **Redis**
   - In-memory data store
   - Temporarily stores voting data

3. **Worker**
   - Background service
   - Reads vote data from Redis
   - Writes processed data to PostgreSQL

4. **PostgreSQL DB**
   - Persistent database
   - Stores final voting results

5. **Result App**
   - Frontend application
   - Reads results from PostgreSQL and displays them

![Application Architecture](Voting%20app.drawio.png)

---

## ☸️ Kubernetes Resources Created

### Deployments

Each application component is managed using a Kubernetes Deployment:

- Vote App Deployment
- Redis Deployment
- Worker Deployment
- PostgreSQL Deployment
- Result App Deployment

Deployments ensure that the desired number of Pods are running and provide self-healing capabilities.

---

### Services

Services enable communication between components and expose selected applications externally.

#### ClusterIP Services (Internal Communication)

- **Redis Service**
  - Used by the Vote App and Worker

- **PostgreSQL Service**
  - Used by the Worker and Result App

#### NodePort Services (External Access)

- **Vote App Service**
- **Result App Service**

---

## 🚀 How to Use This Repository

### Prerequisites

- A working Kubernetes cluster (Minikube, Kind, or similar)

### Deployment

Apply all Kubernetes manifests from the repository root:

```bash
kubectl apply -f deployments/
deployment.apps/db created
deployment.apps/redis created
deployment.apps/result created
deployment.apps/vote created
deployment.apps/worker created

kubectl apply -f services/
service/db created
service/redis created
service/result created
service/vote created

```

### Verification

```
k8s-voting-app-deployments% kubectl get deployment
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
db       1/1     1            1           17s
redis    1/1     1            1           17s
result   1/1     1            1           17s
vote     1/1     1            1           17s
worker   1/1     1            1           17s

k8s-voting-app-deployments% kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
db           ClusterIP   10.x.x.x    <none>        5432/TCP         9s
kubernetes   ClusterIP   10.x.x.x        <none>        443/TCP          16h
redis        ClusterIP   10.x.x.x     <none>        6379/TCP         9s
result       NodePort    10.x.x.x   <none>        8080:31001/TCP   9s
vote         NodePort    10.x.x.x    <none>        8080:31000/TCP   9s

k8s-voting-app-deployments% kubectl get all
NAME                          READY   STATUS    RESTARTS   AGE
pod/db-6d4d465497-98jv9       1/1     Running   0          27s
pod/redis-664fbf775b-wxsm9    1/1     Running   0          27s
pod/result-7b7f69957f-nzk9g   1/1     Running   0          27s
pod/vote-6d8f9ddcdd-qnpl6     1/1     Running   0          27s
pod/worker-89f44c667-945qz    1/1     Running   0          27s

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/db           ClusterIP   10.x.x.x    <none>        5432/TCP         15s
service/kubernetes   ClusterIP   10.x.x.x        <none>        443/TCP          16h
service/redis        ClusterIP   10.x.x.x     <none>        6379/TCP         15s
service/result       NodePort    10.x.x.x   <none>        8080:31001/TCP   15s
service/vote         NodePort    10.x.x.x    <none>        8080:31000/TCP   15s

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/db       1/1     1            1           27s
deployment.apps/redis    1/1     1            1           27s
deployment.apps/result   1/1     1            1           27s
deployment.apps/vote     1/1     1            1           27s
deployment.apps/worker   1/1     1            1           27s

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/db-6d4d465497       1         1         1       27s
replicaset.apps/redis-664fbf775b    1         1         1       27s
replicaset.apps/result-7b7f69957f   1         1         1       27s
replicaset.apps/vote-6d8f9ddcdd     1         1         1       27s
replicaset.apps/worker-89f44c667    1         1         1       27s
k8s-voting-app-deployments%

k8s-voting-app-deployments% kubectl get node -o wide
NAME       STATUS   ROLES           AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE            KERNEL-VERSION   CONTAINER-RUNTIME
minikube   Ready    control-plane   16h   v1.34.0   10.x.x.x    <none>        Buildroot 2025.02   6.6.95           docker://28.4.0

Go to above IP with respective NodePort to view apps in browser:
10.x.x.x:31000 for vote app
10.x.x.x:31001 for result app
```

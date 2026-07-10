# Kubernetes Commands Cheat Sheet

This document contains the Kubernetes commands used during the deployment and management of the HomeEase application on a local Minikube cluster.

---

# Minikube Commands

## Start Minikube

```bash
minikube start
```

Starts the local Kubernetes cluster.

---

## Check Minikube Status

```bash
minikube status
```

Displays the status of:

- Host
- Kubelet
- API Server
- kubeconfig

---

## Stop Minikube

```bash
minikube stop
```

Stops the Kubernetes cluster while preserving the cluster data.

---

## Delete Minikube Cluster

```bash
minikube delete
```

Deletes the entire Kubernetes cluster.

---

## List Images Inside Minikube

```bash
minikube image ls
```

Displays all container images available inside the Minikube cluster.

---

## Load Local Docker Image into Minikube

```bash
minikube image load homeease-backend:latest

minikube image load homeease-frontend:latest
```

Copies locally built Docker images into Minikube.

---

## Open a Service

```bash
minikube service frontend-service
```

Opens the application in the default browser.

---

# Cluster Information

## View Cluster Information

```bash
kubectl cluster-info
```

Displays Kubernetes master and service endpoints.

---

## View Nodes

```bash
kubectl get nodes
```

Lists all Worker/Control Plane nodes.

---

## Describe Node

```bash
kubectl describe node <node-name>
```

Displays detailed node information.

Example:

```bash
kubectl describe node minikube
```

---

# Pod Commands

## Create Pod

```bash
kubectl apply -f backend-pod.yaml
```

---

## List Pods

```bash
kubectl get pods
```

---

## List Pods with More Information

```bash
kubectl get pods -o wide
```

Shows:

- Pod IP
- Node
- Internal IP

---

## Watch Pod Status

```bash
kubectl get pods -w
```

Continuously watches Pod status.

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

Example:

```bash
kubectl describe pod backend-pod
```

---

## View Pod Logs

```bash
kubectl logs <pod-name>
```

Example:

```bash
kubectl logs backend-pod
```

---

## Follow Logs

```bash
kubectl logs -f <pod-name>
```

Shows logs in real-time.

---

## Enter Pod Shell

```bash
kubectl exec -it <pod-name> -- sh
```

Example:

```bash
kubectl exec -it backend-pod -- sh
```

---

## Delete Pod

```bash
kubectl delete pod <pod-name>
```

---

# Deployment Commands

## Create Deployment

```bash
kubectl apply -f backend-deployment.yaml
```

---

## List Deployments

```bash
kubectl get deployments
```

---

## Describe Deployment

```bash
kubectl describe deployment backend-deployment
```

---

## Scale Deployment

```bash
kubectl scale deployment backend-deployment --replicas=3
```

Scale back:

```bash
kubectl scale deployment backend-deployment --replicas=2
```

---

## Restart Deployment

```bash
kubectl rollout restart deployment backend-deployment
```

---

## Check Rollout Status

```bash
kubectl rollout status deployment backend-deployment
```

---

## View Rollout History

```bash
kubectl rollout history deployment backend-deployment
```

---

## Rollback Deployment

```bash
kubectl rollout undo deployment backend-deployment
```

---

## Delete Deployment

```bash
kubectl delete deployment backend-deployment
```

---

# ReplicaSet Commands

## List ReplicaSets

```bash
kubectl get replicasets
```

or

```bash
kubectl get rs
```

---

## Describe ReplicaSet

```bash
kubectl describe rs <replicaset-name>
```

---

# Service Commands

## Create Service

```bash
kubectl apply -f backend-service.yaml
```

---

## List Services

```bash
kubectl get services
```

---

## Describe Service

```bash
kubectl describe service backend-service
```

---

## Delete Service

```bash
kubectl delete service backend-service
```

---

# ConfigMap Commands

## Create ConfigMap

```bash
kubectl apply -f backend-configmap.yaml
```

---

## List ConfigMaps

```bash
kubectl get configmaps
```

---

## Describe ConfigMap

```bash
kubectl describe configmap backend-config
```

---

## Delete ConfigMap

```bash
kubectl delete configmap backend-config
```

---

# Secret Commands

## Create Secret

```bash
kubectl apply -f backend-secret.yaml
```

---

## List Secrets

```bash
kubectl get secrets
```

---

## Describe Secret

```bash
kubectl describe secret backend-secret
```

---

## Delete Secret

```bash
kubectl delete secret backend-secret
```

---

# Namespace Commands

## List Namespaces

```bash
kubectl get namespaces
```

---

## Create Namespace

```bash
kubectl create namespace dev
```

---

## Delete Namespace

```bash
kubectl delete namespace dev
```

---

# Resource Commands

## Display Everything

```bash
kubectl get all
```

---

## Delete All Resources from YAML

```bash
kubectl delete -f kubernetes/
```

---

## Apply All YAML Files

```bash
kubectl apply -f kubernetes/
```

---

## Reapply Updated Configuration

```bash
kubectl apply -f backend-deployment.yaml
```

---

# Debugging Commands

## View Events

```bash
kubectl get events
```

---

## Sort Events

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

---

## Show Resource Usage (Requires Metrics Server)

```bash
kubectl top pods

kubectl top nodes
```

---

# Docker Commands Used with Kubernetes

## Build Images

```bash
docker compose build
```

---

## Build Without Cache

```bash
docker compose build --no-cache
```

---

## List Images

```bash
docker images
```

---

## Remove Image

```bash
docker rmi homeease-backend:latest

docker rmi homeease-frontend:latest
```

---

## Remove Container

```bash
docker rm -f <container-id>
```

---

## Run Docker Compose

```bash
docker compose up
```

---

## Stop Docker Compose

```bash
docker compose down
```

---

# Typical Development Workflow

```text
Modify Application Code
        │
        ▼
docker compose build
        │
        ▼
minikube image load
        │
        ▼
kubectl apply -f kubernetes/
        │
        ▼
kubectl get pods
        │
        ▼
kubectl logs
        │
        ▼
minikube service frontend-service
```

---

# Frequently Used Commands

```bash
kubectl get nodes
kubectl get pods
kubectl get deployments
kubectl get services
kubectl get replicasets
kubectl get configmaps
kubectl get secrets
kubectl get all

kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- sh

kubectl rollout restart deployment backend-deployment
kubectl rollout status deployment backend-deployment
kubectl rollout history deployment backend-deployment
kubectl rollout undo deployment backend-deployment

minikube start
minikube status
minikube stop
minikube service frontend-service
docker compose build
```
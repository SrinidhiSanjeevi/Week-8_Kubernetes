# Kubernetes Networking Demo – HomeEase

# Objective

Demonstrate how a multi-tier application communicates inside a Kubernetes cluster and understand why Kubernetes provides Deployments, Services, Ingress, and Network Policies.

---

# Phase 1 – Start Kubernetes Cluster

## Start Minikube

```bash
minikube start --driver=docker
```

Explain

- Starts the Kubernetes cluster.
- Creates the Control Plane.
- Configures kubectl automatically.
- Starts kubelet, API Server, Scheduler, Controller Manager and etcd.

---

## Verify Cluster

```bash
minikube status
```

Expected

```
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

---

## Verify Nodes

```bash
kubectl get nodes
```

Explain

- Shows worker nodes.
- Minikube has one node.

---

## Check Kubernetes Components

```bash
kubectl get pods -n kube-system
```

Explain

```
kube-apiserver
etcd
kube-controller-manager
kube-scheduler
kube-proxy
coredns
storage-provisioner
```

---

# Phase 2 – Pod Networking

## Create Backend Pod

```bash
kubectl apply -f backend-pod.yaml
```

---

## Create Frontend Pod

```bash
kubectl apply -f frontend-pod.yaml
```

---

## View Pods

```bash
kubectl get pods
```

---

## View Pod IP Addresses

```bash
kubectl get pods -o wide
```

Explain

- Every Pod gets its own IP.
- IPs are assigned by the CNI plugin.
- Pods communicate using Pod IPs.
- Containers inside the same Pod communicate using localhost.

---

## Watch Pods

```bash
kubectl get pods -w
```

Explain

- Continuously watches Pod lifecycle.
- Useful for showing self-healing later.

---

# Phase 3 – Problem with Pods

Delete backend Pod

```bash
kubectl delete pod backend-pod
```

Explain

- Pod disappears.
- Since it is a standalone Pod, Kubernetes will not recreate it.
- Recreate it manually.

```bash
kubectl apply -f backend-pod.yaml
```

Check IP

```bash
kubectl get pods -o wide
```

Explain

Old IP

↓

New IP

Problem

Frontend still knows old IP.

Communication breaks.

---

# Phase 4 – Deployments

Create Deployment

```bash
kubectl apply -f backend-deployment.yaml
kubectl apply -f frontend-deployment.yaml
```

---

View Deployments

```bash
kubectl get deployments
```

---

View ReplicaSets

```bash
kubectl get replicasets
```

---

View Pods

```bash
kubectl get pods
```

Explain

Deployment

↓

ReplicaSet

↓

Pods

---

Delete One Backend Pod

```bash
kubectl delete pod <backend-pod-name>
```

Watch

```bash
kubectl get pods -w
```

Explain

Deployment automatically creates a new Pod.

Self-healing achieved.

But

New Pod gets a different IP.

Networking problem still exists.

---

# Phase 5 – ConfigMap & Secret

Create ConfigMap

```bash
kubectl apply -f backend-configmap.yaml
```

Check

```bash
kubectl get configmaps
```

Describe

```bash
kubectl describe configmap backend-config
```

---

Create Secret

```bash
kubectl apply -f backend-secret.yaml
```

Check

```bash
kubectl get secrets
```

Describe

```bash
kubectl describe secret backend-secret
```

---

Restart Deployment

```bash
kubectl rollout restart deployment backend-deployment
```

---

Verify Environment Variables

```bash
kubectl exec -it <backend-pod> -- printenv
```

---

# Phase 6 – Services

Create Backend Service

```bash
kubectl apply -f backend-service.yaml
```

Create Frontend Service

```bash
kubectl apply -f frontend-service.yaml
```

---

View Services

```bash
kubectl get svc
```

Explain

Frontend

NodePort

Backend

ClusterIP

---

Describe Backend Service

```bash
kubectl describe svc backend-service
```

Explain

Selector

↓

ClusterIP

↓

Endpoints

---

View Endpoints

```bash
kubectl get endpoints
```

---

View EndpointSlices

```bash
kubectl get endpointslice
```

Explain

Service selects Pods using labels.

EndpointSlice stores healthy Pod IPs.

---

Delete Backend Pod Again

```bash
kubectl delete pod <backend-pod>
```

Watch

```bash
kubectl get pods -w
```

Check EndpointSlice

```bash
kubectl get endpointslice
```

Explain

Pod recreated

↓

EndpointSlice updated

↓

Frontend continues working

because

Frontend knows

backend-service

NOT

Pod IP.

---

# Phase 7 – Scaling

Scale Backend

```bash
kubectl scale deployment backend-deployment --replicas=5
```

Verify

```bash
kubectl get deployments
```

Verify Pods

```bash
kubectl get pods
```

Explain

Deployment creates five Pods.

Service automatically updates EndpointSlice.

No frontend changes.

---

# Phase 8 – Ingress

Create Ingress

```bash
kubectl apply -f ingress.yaml
```

View

```bash
kubectl get ingress
```

Explain

Ingress provides

Single Entry Point

Routes

```
/
```

↓

Frontend

```
/api
```

↓

Backend

---

# Phase 9 – Network Policy

Create Policy

```bash
kubectl apply -f backend-networkpolicy.yaml
```

View

```bash
kubectl get networkpolicy
```

Describe

```bash
kubectl describe networkpolicy backend-policy
```

Explain

Only frontend Pods

↓

Backend Pods

Other Pods

↓

Blocked

Requires

Calico

or

Cilium

---

# Phase 10 – Open Application

Open Frontend

```bash
minikube service frontend-service
```

OR

```bash
minikube service frontend-service --url
```

Browser

↓

Frontend

↓

NGINX Reverse Proxy

↓

backend-service

↓

CoreDNS

↓

ClusterIP

↓

kube-proxy

↓

EndpointSlice

↓

Backend Pods

↓

MongoDB Atlas

---

# Useful Commands

```bash
kubectl get all
```

---

```bash
kubectl get pods -o wide
```

---

```bash
kubectl get svc
```

---

```bash
kubectl get endpoints
```

---

```bash
kubectl get endpointslice
```

---

```bash
kubectl describe deployment backend-deployment
```

---

```bash
kubectl describe service backend-service
```

---

```bash
kubectl logs <pod-name>
```

---

```bash
kubectl exec -it <pod-name> -- sh
```

---

```bash
kubectl rollout restart deployment backend-deployment
```

---

```bash
kubectl rollout history deployment backend-deployment
```

---

```bash
kubectl rollout undo deployment backend-deployment
```

---

```bash
kubectl delete pod <pod-name>
```

---

```bash
kubectl scale deployment backend-deployment --replicas=5
```

---

```bash
kubectl get events
```

---

```bash
kubectl get all
```

---

# Final Architecture

```
Browser
      │
      ▼
Ingress
      │
      ▼
Frontend Service (NodePort)
      │
      ▼
Frontend Pods
      │
      ▼
NGINX Reverse Proxy
      │
      ▼
backend-service (ClusterIP)
      │
      ▼
CoreDNS
      │
      ▼
ClusterIP
      │
      ▼
kube-proxy
      │
      ▼
EndpointSlice
      │
      ▼
Backend Pods
      │
      ▼
MongoDB Atlas
```
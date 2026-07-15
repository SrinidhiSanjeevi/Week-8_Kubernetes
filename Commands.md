# Kubernetes Commands Cheat Sheet

This document contains commonly used **Minikube** and **kubectl** commands used while deploying and managing applications in Kubernetes.

---

# Minikube Commands

## Start Minikube

```bash
minikube start --driver=docker
```

Starts the Kubernetes cluster using Docker as the container runtime.

---

## Check Cluster Status

```bash
minikube status
```

Shows the status of:

- Host
- Kubelet
- API Server
- kubeconfig

---

## Stop Minikube

```bash
minikube stop
```

Stops the Kubernetes cluster.

---

## Delete Minikube Cluster

```bash
minikube delete
```

Deletes the entire Minikube cluster.

---

## Get Minikube IP

```bash
minikube ip
```

Returns the IP address of the Minikube node.

---

## Open Kubernetes Dashboard

```bash
minikube dashboard
```

Launches the Kubernetes Dashboard.

---

## Enable Ingress

```bash
minikube addons enable ingress
```

Installs the NGINX Ingress Controller.

---

## List Enabled Addons

```bash
minikube addons list
```

Shows all available Minikube addons.

---

## Access NodePort Service

```bash
minikube service frontend-service
```

Opens the application in the browser.

---

## Get NodePort URL

```bash
minikube service frontend-service --url
```

Returns only the application URL.

---

# Cluster Information

## Cluster Information

```bash
kubectl cluster-info
```

Shows Kubernetes cluster endpoints.

---

## View Nodes

```bash
kubectl get nodes
```

Lists all worker nodes.

---

## Describe Node

```bash
kubectl describe node minikube
```

Displays detailed information about the node.

---

## View Kubernetes System Components

```bash
kubectl get pods -n kube-system
```

Shows:

- kube-apiserver
- etcd
- kube-controller-manager
- kube-scheduler
- kube-proxy
- CoreDNS
- Storage Provisioner

---

# Pod Commands

## Create Pod

```bash
kubectl apply -f backend-pod.yaml
```

---

## Create Frontend Pod

```bash
kubectl apply -f frontend-pod.yaml
```

---

## List Pods

```bash
kubectl get pods
```

---

## Show Pod IP Addresses

```bash
kubectl get pods -o wide
```

Displays:

- Pod IP
- Node
- Status

---

## Watch Pods

```bash
kubectl get pods -w
```

Continuously watches Pod changes.

---

## Describe Pod

```bash
kubectl describe pod backend-pod
```

Shows:

- Events
- Image
- IP
- Volumes
- Environment Variables

---

## View Pod Logs

```bash
kubectl logs backend-pod
```

---

## Stream Logs

```bash
kubectl logs -f backend-pod
```

---

## Execute Shell Inside Pod

```bash
kubectl exec -it backend-pod -- sh
```

---

## Print Environment Variables

```bash
kubectl exec -it backend-pod -- printenv
```

---

## Delete Pod

```bash
kubectl delete pod backend-pod
```

---

# Deployment Commands

## Create Deployment

```bash
kubectl apply -f backend-deployment.yaml
```

---

```bash
kubectl apply -f frontend-deployment.yaml
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

## Restart Deployment

```bash
kubectl rollout restart deployment backend-deployment
```

---

## Deployment History

```bash
kubectl rollout history deployment backend-deployment
```

---

## Rollback Deployment

```bash
kubectl rollout undo deployment backend-deployment
```

---

## Deployment Status

```bash
kubectl rollout status deployment backend-deployment
```

---

## Scale Deployment

```bash
kubectl scale deployment backend-deployment --replicas=5
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

---

## Describe ReplicaSet

```bash
kubectl describe replicaset <replicaset-name>
```

---

# Service Commands

## Create Backend Service

```bash
kubectl apply -f backend-service.yaml
```

---

## Create Frontend Service

```bash
kubectl apply -f frontend-service.yaml
```

---

## List Services

```bash
kubectl get svc
```

---

## Describe Service

```bash
kubectl describe svc backend-service
```

---

## Delete Service

```bash
kubectl delete svc backend-service
```

---

# Endpoint Commands

## List Endpoints

```bash
kubectl get endpoints
```

---

## Describe Endpoints

```bash
kubectl describe endpoints backend-service
```

---

## List EndpointSlices

```bash
kubectl get endpointslice
```

---

## Describe EndpointSlice

```bash
kubectl describe endpointslice
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

# Ingress Commands

## Create Ingress

```bash
kubectl apply -f ingress.yaml
```

---

## List Ingress

```bash
kubectl get ingress
```

---

## Describe Ingress

```bash
kubectl describe ingress homeease-ingress
```

---

## Delete Ingress

```bash
kubectl delete ingress homeease-ingress
```

---

# Network Policy Commands

## Create Network Policy

```bash
kubectl apply -f backend-networkpolicy.yaml
```

---

## List Network Policies

```bash
kubectl get networkpolicy
```

---

## Describe Network Policy

```bash
kubectl describe networkpolicy backend-policy
```

---

## Delete Network Policy

```bash
kubectl delete networkpolicy backend-policy
```

---


## Open Application

```bash
minikube service frontend-service
```

---

## Get Application URL

```bash
minikube service frontend-service --url
```
# Kubernetes Internal Architecture
## HomeEase – Architecture Reference

---

# Kubernetes Cluster Architecture

```text
                               User / kubectl
                                      │
                                      ▼
                           +----------------------+
                           |      API Server      | which is use cluster ip as deafult for internal communication 
                           |  (Cluster Gateway)   |
                           +----------------------+
                              │      │       │
                              │      │       │
                              │      │       __________     Stores State
                              ▼      ▼                 ▼
                     +------------+ +---------------+ +-----------+
                     | Scheduler  | | Controller    | |   etcd    |
                     |            | |    Manager    | | Cluster DB|
                     +------------+ +---------------+ +-----------+
                              │
                              │ Select Worker Node
                              ▼
====================================================================
                           Worker Node
====================================================================
          +-----------+      +-------------+      +--------------+
          | kubelet   | ---> | Container   | ---> |     Pod      |
          |           |      | Runtime     |      |    |
          +-----------+      +-------------+      +--------------+
                 │           (docker old verion ,conatinerd and cri-o)
                 ▼
          +---------------+
          | kube-proxy    |
          +---------------+
                 │
                 ▼
          +---------------+
          |  CNI Plugin   | creates virtual network,ip table,ip address,routes
          +---------------+
                 │
                 ▼
           Pod Networking
```

---

## Cluster Communication Flow

```text
kubectl apply deployment.yaml
              │
              ▼
        API Server
              │
              ▼
      Store Object in etcd
              │
              ▼
 Controller Manager watches changes
              │
              ▼
 Scheduler selects Worker Node
              │
              ▼
 kubelet receives Pod specification
              │
              ▼
Container Runtime pulls image
              │
              ▼
 CNI assigns Pod IP
              │
              ▼
 Pod becomes Running
```

---

# 3. Control Plane Components

```text
                   Control Plane

                 +-------------------+
                 |    API Server     |
                 +-------------------+
                    │      │
        Watch Objects│      │Store State
                    ▼      ▼
          +-----------------------+
          |        etcd           |
          +-----------------------+
                    ▲
                    │
          +-----------------------+
          | Controller Manager    |
          +-----------------------+
                    │
                    ▼
          +-----------------------+
          |      Scheduler        |
          +-----------------------+
                    │
                    ▼
               Worker Node

(Optional)

          +-----------------------+
          | Cloud Controller Mgr  |
          | AWS / Azure / GCP     |
          +-----------------------+
```

---

## Internal Control Plane Flow

```text
User

 │

 ▼

API Server
(Receives every request)

 │

 ▼

etcd
(Stores cluster configuration)

 │

 ▼

Controller Manager
(Detects desired vs current state)

 │

 ▼

Scheduler
(Chooses the best Worker Node)

 │

 ▼

API Server updates Node assignment

 │

 ▼

Worker Node receives Pod
```

---

# 4. Worker Node Architecture

```text
==================================================
              Kubernetes Worker Node
==================================================

                 kubelet
                    │
                    ▼
          Container Runtime
      (containerd / CRI-O)

                    │
                    ▼
              CNI Plugin
                    │
      ┌─────────────┴──────────────┐
      ▼                            ▼
 Assign Pod IP              Configure Routes
      │                            │
      └─────────────┬──────────────┘
                    ▼
                Linux Network
                    │
                    ▼
             +----------------+
             |      Pod       |
             | Container(s)   |
             +----------------+

                    ▲
                    │
              kube-proxy
                    │
        Service Traffic Routing
```

---

## Worker Node Internal Flow

```text
Scheduler

 │

 ▼

Selected Worker Node

 │

 ▼

kubelet

 │
 │ Receives Pod Spec
 ▼

Container Runtime

 │
 │ Pull Image
 ▼

Create Containers

 │

 ▼

CNI Plugin

 │
 ├── Create Network Namespace
 ├── Create veth Pair
 ├── Connect Linux Bridge
 ├── Assign Pod IP
 └── Configure Routing

 │

 ▼

Pod Ready

 │

 ▼

kube-proxy

 │

 ▼

Service Traffic Available
```

---

# Complete Kubernetes Internal Flow

```text
kubectl apply

      │

      ▼

API Server
      │
      ▼

etcd
      │
      ▼

Controller Manager
      │
      ▼

Scheduler
      │
      ▼

Worker Node
      │
      ▼

kubelet
      │
      ▼

Container Runtime
      │
      ▼

Pull Image
      │
      ▼

CNI Plugin
      │
      ▼

Assign Pod IP
      │
      ▼

Pod Running
      │
      ▼

Service Created
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

Backend Pod
```

---

#  Pod Lifecycle

---

# Complete Pod Creation Flow

## Architecture

```text
                 kubectl apply deployment.yaml
                            │
                            ▼
                    +------------------+
                    |    API Server    |
                    +------------------+
                            │
                 Validate & Store Object
                            │
                            ▼
                    +------------------+
                    |       etcd       |
                    | Cluster Database |
                    +------------------+
                            ▲
                            │
                 Watch Desired State
                            │
                            ▼
                +-----------------------+
                | Controller Manager    |
                +-----------------------+
                            │
               Create ReplicaSet / Pods
                            │
                            ▼
                  +------------------+
                  |    Scheduler     |
                  +------------------+
                            │
                 Select Worker Node
                            │
                            ▼
==============================================================
                    Worker Node
==============================================================
                            │
                            ▼
                    +------------------+
                    |     kubelet      |
                    +------------------+
                            │
                  Pull Container Image
                            │
                            ▼
                  +------------------+
                  | Container Runtime |
                  | containerd/CRI-O  |
                  +------------------+
                            │
                   Request Networking
                            │
                            ▼
                  +------------------+
                  |    CNI Plugin     |
                  +------------------+
                            │
                    Assign Pod IP
                            │
                            ▼
                     +--------------+
                     | Running Pod  |
                     +--------------+
```

---

# 6. Deployment → ReplicaSet → Pod Relationship

## Architecture

```text
                     Deployment
             (Desired State Manager)
                       │
                       ▼
                 +-------------+
                 | ReplicaSet  |
                 +-------------+
                  │     │     │
                  ▼     ▼     ▼
              +------+ +------+ +------+
              | Pod1 | | Pod2 | | Pod3 |
              +------+ +------+ +------+
                  │       │        │
                  ▼       ▼        ▼
             Container Container Container
```

---

## Scaling Flow

```text
Deployment

      │

Replicas = 3

      │

      ▼

ReplicaSet

      │

      ▼

Pod1

Pod2

Pod3


kubectl scale --replicas=5

      │

      ▼

ReplicaSet

      │

      ▼

Pod1

Pod2

Pod3

Pod4

Pod5
```

---

## Self-Healing Flow

```text
Pod2 Deleted

      │

      ▼

ReplicaSet Detects Change

      │

      ▼

New Pod Created

      │

      ▼

Desired Replicas Restored
```

---

# 7. Pod Networking

## Pod Network Architecture

```text
======================================================
                 Worker Node
======================================================

          +-------------------------+
          |      Linux Kernel       |
          +-------------------------+

               │              │

               ▼              ▼

         Network Namespace  Network Namespace

               │              │

               ▼              ▼

          +---------+    +---------+
          |  Pod A  |    |  Pod B  |
          |10.244.0.2|   |10.244.0.3|
          +---------+    +---------+

               │              │
               └──────┬───────┘
                      ▼

                  Linux Bridge
                      │
                      ▼

                 Physical Network

                      │
                      ▼

                 Other Worker Node

                      │
                      ▼

                 Pod C (10.244.1.2)
```

---

## Pod-to-Pod Communication 

by default pods can communicate with each other 

```text
Pod A

10.244.0.2

      │

      ▼

Linux Bridge

      │

      ▼

Node Routing

      │

      ▼

Linux Bridge

      │

      ▼

Pod C

10.244.1.2
```

---

## Pod Networking Components

```text
Pod

│

├── Network Namespace

├── veth Pair

├── Pod IP

├── Route Table

└── iptables Rules
```

---

# 8. CNI Plugin Architecture

## CNI Internal Architecture

```text
                 kubelet
                    │
                    ▼
          +----------------------+
          | Container Runtime    |
          +----------------------+
                    │
          Create Container
                    │
                    ▼
          +----------------------+
          |    CNI Plugin        |
          +----------------------+
                    │
     ┌──────────────┼───────────────┐
     │              │               │
     ▼              ▼               ▼
Create veth     Assign Pod IP   Configure Routes
     │              │               │
     └──────────────┼───────────────┘
                    ▼
          Connect Linux Bridge
                    │
                    ▼
             Pod Network Ready
```

---

## CNI Networking Flow

```text
Scheduler

      │

      ▼

Worker Node

      │

      ▼

kubelet

      │

      ▼

Container Runtime

      │

Create Container

      │

      ▼

CNI Plugin

      │

Create Network Namespace

      │

      ▼

Create veth Pair

      │

      ▼

Assign Pod IP

      │

      ▼

Configure Routes

      │

      ▼

Connect Linux Bridge

      │

      ▼

Pod Ready
```

---

# Service Creation Flow

Service YAML

      │

kubectl apply

      │

API Server

      │

etcd

      │

Controller Manager

      │

Service Created

      │

EndpointSlice Created

      │

CoreDNS Updated

      │

kube-proxy Updated

      │

Traffic Ready

---

# EndpointSlice Update Flow

New Pod Created

      │

Label = app=backend

      │

Matches Service Selector

      │

EndpointSlice Updated

      │

kube-proxy Updates Rules

      │

Traffic Starts

---

# 12. HomeEase Request Flow

## HomeEase Internal Request Architecture

```text
                        User
                         │
                         ▼
                    Browser
                         │
                         ▼
               Frontend Service
                 (NodePort)
                         │
         ┌───────────────┴──────────────┐
         ▼                              ▼
   Frontend Pod-1                Frontend Pod-2
         │
         │ HTTP Request
         ▼
      backend-service
        (ClusterIP)
         │
         ▼
       CoreDNS
         │
Resolve Service Name
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
  ┌──────┼─────────────┐
  ▼      ▼             ▼
Backend1 Backend2   Backend3
         │
         ▼
    MongoDB Atlas
```

---

## Complete Request Flow

```text
Browser

      │

      ▼

Frontend Service

      │

      ▼

Frontend Pod

      │

fetch("backend-service")

      │

      ▼

CoreDNS

      │

Resolve Service Name

      │

      ▼

ClusterIP

      │

      ▼

kube-proxy

      │

Select Healthy Backend Pod

      │

      ▼

EndpointSlice

      │

      ▼

Backend Pod

      │

      ▼

MongoDB Atlas

      │

      ▼

Response Returned
```

---

# 13. Scaling Architecture

## Initial State

```text
Deployment

      │

Desired Replicas = 3

      │

      ▼

ReplicaSet

      │

      ▼

Pod1

Pod2

Pod3
```

---

## After Scaling

```text
kubectl scale deployment backend --replicas=5

              │

              ▼

Deployment

              │

Desired Replicas = 5

              │

              ▼

ReplicaSet

              │

              ▼

Pod1

Pod2

Pod3

Pod4

Pod5
```

### Internal Flow

```text
Scale Command

      │

      ▼

API Server

      │

      ▼

etcd Updated

      │

      ▼

Controller Manager

      │

Desired = 5

      │

      ▼

ReplicaSet

      │

Creates

      ▼

New Pods

      │

      ▼

Scheduler

      │

      ▼

Worker Nodes

      │

      ▼

Pods Running
```


---

# 14. Self-Healing Architecture

## Pod Failure

```text
Deployment

      │

ReplicaSet

      │

 ┌───────────────┐

 ▼               ▼

Pod1          Pod2

                  X

             Pod Deleted
```

---

## Recovery

```text
ReplicaSet

      │

Detect Missing Pod

      │

      ▼

Create New Pod

      │

      ▼

Scheduler

      │

      ▼

Worker Node

      │

      ▼

Running Pod
```

### Internal Flow

```text
Pod Crash

      │

      ▼

kubelet Reports Failure

      │

      ▼

API Server

      │

      ▼

Controller Manager

      │

ReplicaSet

      │

Create Replacement Pod

      │

      ▼

Scheduler

      │

      ▼

New Pod Running
```


---

# 15. Rolling Update

## Current Version

```text
Deployment

Image = v1

      │

      ▼

Pod1(v1)

Pod2(v1)

Pod3(v1)
```

---

## Update Image

```text
kubectl set image deployment/backend backend=image:v2
```

---

## Rolling Update Flow

```text
Deployment

      │

New Image = v2

      │

      ▼

Create Pod(v2)

      │

Wait Until Ready

      │

Delete One Pod(v1)

      │

Create Another Pod(v2)

      │

Delete Another Pod(v1)

      │

Repeat

      ▼

All Pods Running v2
```


> **Rolling Update creates NEW Pods with the new version, verifies they are healthy, and then removes the old Pods one by one.** Existing Pods are **not updated in place**.

---

# 16. Rollback

```text
Deployment

Version v2

      │

Rollback

      │

      ▼

Previous ReplicaSet

      │

      ▼

Create Pods(v1)

      │

Delete Pods(v2)

      ▼

Application Restored
```

---

# 17. Ingress Architecture

## Production Traffic Flow

```text
                 Internet
                     │
                     ▼
              Ingress Controller
                     │
        ┌────────────┴────────────┐
        ▼                         ▼
 Frontend Service          Backend API
    (ClusterIP)             (ClusterIP)
        │                         │
        ▼                         ▼
 Frontend Pods             Backend Pods
```

---

## Ingress Internal Flow

```text
User Request

      │

      ▼

Ingress

      │

Read Host / Path Rules

      │

      ▼

Frontend Service

      │

      ▼

Frontend Pods
```

### Example

```text
example.com
        │
        ▼
Frontend Service

-------------------------

example.com/api
        │
        ▼
Backend Service
```

---

# Project Work Flow




Browser
      │
      ▼
NodePort

Frontend Service

      │
      ▼
Frontend Pod

      │

fetch("/api")

      │
      ▼
NGINX Reverse Proxy

      │

proxy_pass http://backend-service:5000

      │
      ▼
CoreDNS

Resolve

backend-service

↓

10.107.66.8

      │
      ▼
kube-proxy

      │
Reads EndpointSlice

      │
      ▼
Backend Pod

      │
      ▼
MongoDB Atlas

      │
Response

      │
NGINX

      │
Browser


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



# Show Services
kubectl get svc

# Detailed Service information
kubectl describe svc backend-service

# Show Endpoints
kubectl get endpoints

# Show EndpointSlices
kubectl get endpointslice

# Show Pods
kubectl get pods -o wide

# Watch Pods
kubectl get pods -w

# Delete one backend Pod
kubectl delete pod <backend-pod>

# Scale backend
kubectl scale deployment backend-deployment --replicas=5

# Show new Endpoints
kubectl get endpointslice

# Open frontend
minikube service frontend-service
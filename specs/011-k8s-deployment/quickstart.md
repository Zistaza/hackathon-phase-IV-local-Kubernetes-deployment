# Quickstart Guide: Phase IV Kubernetes Deployment

## Overview

This guide provides step-by-step instructions for deploying the Todo AI Chatbot to a local Minikube cluster using AI-assisted DevOps practices.

## Prerequisites

### System Requirements
- **Operating System**: Linux, macOS, or Windows 10/11
- **RAM**: Minimum 8GB (Recommended 16GB)
- **CPU**: Minimum 4 cores (Recommended 6 cores)
- **Disk Space**: Minimum 30GB free space
- **Docker**: Docker Desktop installed and running

### Software Installation

#### 1. Install Docker Desktop
Download and install Docker Desktop from https://www.docker.com/products/docker-desktop

#### 2. Install Minikube
```bash
# macOS
brew install minikube

# Linux
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Windows (PowerShell)
irm https://storage.googleapis.com/minikube/releases/latest/minikube-windows-amd64.exe -OutFile minikube.exe
```

#### 3. Install kubectl
```bash
# macOS
brew install kubectl

# Linux
curl -LO https://dl.k8s.io/release/v1.28.0/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# Windows (PowerShell)
irm https://dl.k8s.io/release/v1.28.0/bin/windows/amd64/kubectl.exe -OutFile kubectl.exe
```

#### 4. Install Helm
```bash
# macOS
brew install helm

# Linux
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# Windows (PowerShell)
irm https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3.ps1 -OutFile get_helm.ps1
./get_helm.ps1
```

## Step 1: Start Minikube Cluster

```bash
# Start Minikube with optimal resources
minikube start \
  --cpus=4 \
  --memory=8192 \
  --disk-size=30g \
  --driver=docker

# Enable necessary addons
minikube addons enable ingress
minikube addons enable metrics-server

# Verify cluster is running
kubectl cluster-info
kubectl get nodes
```

**Expected Output:**
```
Kubernetes control plane is running at https://127.0.0.1:8443
CoreDNS is running at https://127.0.0.1:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   2m    v1.28.0
```

## Step 2: Containerize Applications with Docker AI

### 2.1 Navigate to Project Directory
```bash
cd /path/to/todo-chatbot
```

### 2.2 Use Docker AI for Containerization
```bash
# Containerize backend
docker ai build backend --name todo-backend --tag latest

# Containerize frontend
docker ai build frontend --name todo-frontend --tag latest

# View generated Dockerfiles
docker ai show dockerfiles
```

**Expected Output:**
```
Building backend...
Successfully built todo-backend:latest
Building frontend...
Successfully built todo-frontend:latest
```

### 2.3 Verify Container Images
```bash
docker images | grep todo
```

**Expected Output:**
```
todo-backend   latest   1234567890ab   2 minutes ago   12MB
todo-frontend  latest   0987654321ab   2 minutes ago   45MB
```

## Step 3: Create and Deploy Helm Chart

### 3.1 Generate Helm Chart
```bash
# Create Helm chart
docker ai helm create todo-app --version 1.0.0

# View generated chart structure
docker ai show helm-chart
```

### 3.2 Customize Helm Chart
```bash
# Edit values.yaml with appropriate configurations
nano charts/todo-app/values.yaml
```

**Key Configurations to Set:**
```yaml
images:
  backend:
    repository: todo-backend
    tag: latest
  frontend:
    repository: todo-frontend
    tag: latest

replicas:
  backend: 2
  frontend: 1

resources:
  backend:
    requests:
      cpu: "100m"
      memory: "256Mi"
    limits:
      cpu: "500m"
      memory: "512Mi"
  frontend:
    requests:
      cpu: "50m"
      memory: "128Mi"
    limits:
      cpu: "200m"
      memory: "256Mi"

healthChecks:
  backend:
    path: /health
    port: 8000
  frontend:
    path: /
    port: 80
```

### 3.3 Deploy Helm Chart
```bash
# Deploy to Minikube
helm install todo-app charts/todo-app

# Check deployment status
helm status todo-app
```

**Expected Output:**
```
NAME: todo-app
LAST DEPLOYED: 2026-02-07 10:00:00.000000000 +0000 UTC
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

## Step 4: Verify Deployment

### 4.1 Check Pod Status
```bash
kubectl get pods
```

**Expected Output:**
```
NAME                               READY   STATUS    RESTARTS   AGE
todo-backend-7c6d8f9d8f-2g5h1       1/1     Running   0          2m
todo-backend-7c6d8f9d8f-4k2j3       1/1     Running   0          2m
todo-frontend-4d3c2b1a2c-1p9q8      1/1     Running   0          2m
```

### 4.2 Check Services
```bash
kubectl get services
```

**Expected Output:**
```
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
todo-backend     ClusterIP   10.109.87.123   <none>        8000/TCP   2m
todo-frontend    ClusterIP   10.111.45.67    <none>        80/TCP     2m
```

### 4.3 Access Frontend
```bash
# Get Minikube IP
minikube ip

# Access frontend in browser
http://$(minikube ip):$(kubectl get svc todo-frontend -o jsonpath='{.spec.ports[0].nodePort}')
```

## Step 5: Demonstrate AI DevOps Tools

### 5.1 Install kubectl-ai
```bash
# Install kubectl-ai
curl -sL https://kubectl-ai.sh/install | sh

# Verify installation
kubectl ai --version
```

### 5.2 Demonstrate kubectl-ai Commands

#### Scale Deployment
```bash
# Scale backend to 3 replicas
kubectl ai "scale deployment todo-backend to 3 replicas"

# Check scaling result
kubectl get pods
```

#### Debug Pod
```bash
# Debug a specific pod
kubectl ai "debug pod todo-backend-7c6d8f9d8f-2g5h1"

# Check pod logs
kubectl ai "show logs for pod todo-backend-7c6d8f9d8f-2g5h1"
```

#### Update Deployment
```bash
# Update backend image
kubectl ai "update deployment todo-backend to use latest image"

# Check rollout status
kubectl ai "check rollout status for deployment todo-backend"
```

### 5.3 Install and Use Kagent
```bash
# Install Kagent
curl -L https://kagent.io/install | sh

# Analyze cluster health
kagent analyze cluster --health

# Get resource optimization recommendations
kagent optimize resources --cluster

# Analyze specific deployment
kagent analyze deployment todo-backend --insights
```

**Expected Kagent Output:**
```
Cluster Health Analysis:
- CPU Utilization: 65%
- Memory Utilization: 45%
- Network I/O: Normal

Resource Optimization Recommendations:
- Increase CPU limits for backend deployment
- Optimize memory usage for frontend containers
- Consider horizontal pod autoscaling
```

## Step 6: Test Application Functionality

### 6.1 Test Backend API
```bash
# Test backend health endpoint
curl http://$(minikube ip):$(kubectl get svc todo-backend -o jsonpath='{.spec.ports[0].nodePort}')/health

# Test backend API endpoint
curl http://$(minikube ip):$(kubectl get svc todo-backend -o jsonpath='{.spec.ports[0].nodePort}')/api/health
```

### 6.2 Test Frontend
```bash
# Open frontend in browser
open http://$(minikube ip):$(kubectl get svc todo-frontend -o jsonpath='{.spec.ports[0].nodePort}')

# Test authentication flow
# Click "Sign In" and verify JWT authentication
```

## Step 7: Clean Up

### 7.1 Uninstall Helm Release
```bash
helm uninstall todo-app
```

### 7.2 Stop Minikube
```bash
minikube stop
```

### 7.3 Remove Minikube
```bash
minikube delete
```

## Troubleshooting

### Common Issues and Solutions

#### Minikube Won't Start
```bash
# Check Docker status
docker ps

# Try different driver
minikube start --driver=docker

# Check system resources
free -h
```

#### Container Image Build Fails
```bash
# Check Docker daemon status
docker info

# Clear Docker cache
docker system prune -a

# Try building manually
docker build -t todo-backend .
```

#### Helm Chart Deployment Fails
```bash
# Check Helm chart syntax
helm lint charts/todo-app

# Check Kubernetes events
kubectl get events --sort-by='.lastTimestamp'

# Check pod logs
kubectl logs deployment/todo-backend
```

#### kubectl-ai Commands Fail
```bash
# Check kubectl-ai installation
kubectl ai --version

# Verify AI provider configuration
kubectl ai config list

# Check network connectivity
ping api.openai.com
```

### Useful Commands

#### Monitoring
```bash
# Watch pod status
watch kubectl get pods

# View pod logs
kubectl logs -f deployment/todo-backend

# Check resource usage
kubectl top pods
```

#### Debugging
```bash
# Describe pod
kubectl describe pod todo-backend-7c6d8f9d8f-2g5h1

# Check events
kubectl get events --sort-by='.lastTimestamp'

# Debug pod
kubectl exec -it todo-backend-7c6d8f9d8f-2g5h1 -- /bin/bash
```

#### Networking
```bash
# Port forward
kubectl port-forward svc/todo-backend 8000:8000

# Check service endpoints
kubectl get endpoints todo-backend

# Test service connectivity
kubectl exec -it todo-frontend-4d3c2b1a2c-1p9q8 -- curl todo-backend:8000/health
```

## Verification Checklist

### ✅ Containerization
- [ ] Backend containerized as todo-backend:latest
- [ ] Frontend containerized as todo-frontend:latest
- [ ] Container images are optimized and secure
- [ ] Images can run successfully in containers

### ✅ Kubernetes Deployment
- [ ] Helm chart created and validated
- [ ] All pods are running and ready
- [ ] Services are active and accessible
- [ ] Application is accessible via browser

### ✅ AI DevOps Tools
- [ ] kubectl-ai installed and functional
- [ ] Kagent installed and functional
- [ ] AI-assisted scaling demonstrated
- [ ] AI-assisted debugging demonstrated
- [ ] AI-assisted cluster analysis demonstrated

### ✅ Application Functionality
- [ ] Backend API endpoints are accessible
- [ ] Frontend loads correctly
- [ ] Authentication works with JWT
- [ ] User data isolation is maintained
- [ ] All Phase III functionality is preserved

### ✅ Documentation
- [ ] Complete deployment documentation created
- [ ] Troubleshooting guide available
- [ ] Verification checklist completed
- [ ] Demo-ready presentation prepared

## Next Steps

1. **Demo Preparation**: Prepare demonstration script and slides
2. **Performance Testing**: Test application under load
3. **Security Review**: Verify security configurations
4. **Documentation**: Complete all documentation
5. **Feedback Collection**: Gather feedback from test users

## Support

For additional help:
- Check the troubleshooting section above
- Review the official documentation:
  - [Docker Documentation](https://docs.docker.com/)
  - [Kubernetes Documentation](https://kubernetes.io/docs/)
  - [Helm Documentation](https://helm.sh/docs/)
  - [kubectl-ai Documentation](https://kubectl-ai.sh/docs)
  - [Kagent Documentation](https://kagent.io/)
- Search for solutions on Stack Overflow or Kubernetes forums
- Consult with team members or mentors

## Congratulations!

You have successfully deployed the Todo AI Chatbot to a local Kubernetes cluster using AI-assisted DevOps practices! Your deployment is now ready for demonstration and evaluation.

---

**Note**: This guide assumes you have already completed Phase III implementation. The deployment focuses solely on infrastructure and does not modify any application logic.
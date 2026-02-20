# Echo Load Balancing with Kubernetes

A Kubernetes-based load balancing project that demonstrates horizontal scaling and load distribution using the HashiCorp http-echo server.

## 📋 Project Overview

This project sets up a simple echo server application in Kubernetes with load balancing capabilities. The application runs multiple replicas (3 pods) behind a Kubernetes Service, which automatically distributes incoming traffic across all available pods, providing high availability and load balancing.

## 🏗️ Architecture

The project consists of three main Kubernetes resources:

1. **ConfigMap** (`echo-config.yaml`) - Stores configuration data (the echo message)
2. **Deployment** (`echo-deployment.yaml`) - Manages 3 replicas of the echo server pods
3. **Service** (`echo-service.yaml`) - Exposes the pods via NodePort and handles load balancing

## 📁 Project Structure

```
.
├── echo-config.yaml      # ConfigMap for application configuration
├── echo-deployment.yaml  # Deployment with 3 replicas
├── echo-service.yaml     # Service for load balancing
└── README.md            # This file
```

## 🔧 Components

### 1. ConfigMap (`echo-config.yaml`)
- **Purpose**: Stores the message that the echo server will display
- **Key**: `MESSAGE` with value `"Hello, Kubernetes!"`
- **Usage**: Injected as an environment variable into the pods

### 2. Deployment (`echo-deployment.yaml`)
- **Replicas**: 3 pods for high availability
- **Image**: `hashicorp/http-echo:latest`
- **Port**: Container listens on port 5678
- **Features**:
  - Resource limits and requests (CPU: 100m-200m, Memory: 128Mi-256Mi)
  - Liveness probe (checks if container is running)
  - Readiness probe (checks if container is ready to serve traffic)
  - Environment variable from ConfigMap

### 3. Service (`echo-service.yaml`)
- **Type**: NodePort
- **Port Mapping**: 
  - Service port: 80
  - Target port: 5678 (container port)
  - NodePort: 30080 (external access)
- **Load Balancing**: Automatically distributes traffic across all 3 pods

## 🚀 Getting Started

### Prerequisites

- Kubernetes cluster running (minikube, kind, or cloud-based cluster)
- `kubectl` command-line tool installed and configured
- Access to the Kubernetes cluster

### Deployment Steps

1. **Apply the ConfigMap**:
   ```bash
   kubectl apply -f echo-config.yaml
   ```

2. **Deploy the application**:
   ```bash
   kubectl apply -f echo-deployment.yaml
   ```

3. **Create the Service**:
   ```bash
   kubectl apply -f echo-service.yaml
   ```

4. **Verify deployment**:
   ```bash
   # Check pods
   kubectl get pods -l app=echo-app
   
   # Check service
   kubectl get svc echo-service
   
   # Check deployment
   kubectl get deployment echo-deployment
   ```

### Alternative: Deploy All at Once

You can deploy all resources in one command:
```bash
kubectl apply -f echo-config.yaml -f echo-deployment.yaml -f echo-service.yaml
```

Or if you're in the project directory:
```bash
kubectl apply -f .
```

## 🧪 Testing the Load Balancer

### 1. Test via NodePort (External Access)

If using **minikube**:
```bash
minikube service echo-service
```

Or access directly:
- **Minikube**: `http://$(minikube ip):30080`
- **Other clusters**: `http://<node-ip>:30080`

### 2. Test via Port Forwarding

```bash
kubectl port-forward svc/echo-service 8080:80
```

Then access: `http://localhost:8080`

### 3. Test Load Balancing

Make multiple requests to see load balancing in action:
```bash
# On Windows PowerShell
for ($i=1; $i -le 10; $i++) { 
    Invoke-WebRequest -Uri http://localhost:8080 
}

# On Linux/Mac
for i in {1..10}; do curl http://localhost:8080; done
```

Each request may hit a different pod, demonstrating load balancing.

### 4. Verify Pod Distribution

Check which pod handles each request:
```bash
# Get pod names
kubectl get pods -l app=echo-app -o wide

# Check logs from a specific pod
kubectl logs <pod-name>
```

## 📊 Monitoring

### Check Pod Status
```bash
kubectl get pods -l app=echo-app -o wide
```

### Check Service Endpoints
```bash
kubectl get endpoints echo-service
```

### View Pod Logs
```bash
# All pods
kubectl logs -l app=echo-app

# Specific pod
kubectl logs <pod-name>
```

### Describe Resources
```bash
kubectl describe deployment echo-deployment
kubectl describe service echo-service
kubectl describe configmap echo-config
```

## 🔄 Scaling

### Scale Up
```bash
kubectl scale deployment echo-deployment --replicas=5
```

### Scale Down
```bash
kubectl scale deployment echo-deployment --replicas=2
```

### Auto-scaling (if HPA is configured)
```bash
kubectl autoscale deployment echo-deployment --min=2 --max=10 --cpu-percent=80
```

## 🧹 Cleanup

To remove all resources:
```bash
kubectl delete -f echo-service.yaml
kubectl delete -f echo-deployment.yaml
kubectl delete -f echo-config.yaml
```

Or delete all at once:
```bash
kubectl delete -f .
```

## 📝 Customization

### Change the Echo Message

Edit `echo-config.yaml`:
```yaml
data:
  MESSAGE: "Your custom message here"
```

Then apply:
```bash
kubectl apply -f echo-config.yaml
kubectl rollout restart deployment echo-deployment
```

### Change Number of Replicas

Edit `echo-deployment.yaml`:
```yaml
spec:
  replicas: 5  # Change this number
```

Or use kubectl:
```bash
kubectl scale deployment echo-deployment --replicas=5
```

### Change NodePort

Edit `echo-service.yaml`:
```yaml
ports:
  - port: 80
    targetPort: 5678
    nodePort: 30081  # Change this (must be 30000-32767)
```

## 🎯 Key Features Implemented

✅ **Load Balancing**: Kubernetes Service automatically distributes traffic across pods  
✅ **High Availability**: 3 replicas ensure service continues if a pod fails  
✅ **Health Checks**: Liveness and readiness probes ensure only healthy pods receive traffic  
✅ **Resource Management**: CPU and memory limits prevent resource exhaustion  
✅ **Configuration Management**: ConfigMap separates configuration from application code  
✅ **External Access**: NodePort service allows access from outside the cluster  

## 🔍 Troubleshooting

### Pods not starting
```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

### Service not accessible
```bash
kubectl get endpoints echo-service
kubectl describe service echo-service
```

### Check if pods are ready
```bash
kubectl get pods -l app=echo-app
# Look for "READY" column - should show 1/1
```

## 📚 Additional Resources

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [HashiCorp http-echo](https://github.com/hashicorp/http-echo)
- [Kubernetes Service Types](https://kubernetes.io/docs/concepts/services-networking/service/)

---

**Note**: This project demonstrates basic Kubernetes load balancing concepts. For production use, consider using Ingress controllers, TLS certificates, and more sophisticated monitoring solutions.


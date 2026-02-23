# Echo Load Balancing with Kubernetes

A Kubernetes-based load balancing project that demonstrates horizontal scaling and load distribution using the HashiCorp http-echo server.

## 📋 Project Overview

This project sets up a simple echo server application in Kubernetes with load balancing capabilities. The application runs multiple replicas (3 pods) behind a Kubernetes Service, which automatically distributes incoming traffic across all available pods, providing high availability and load balancing.

## 📖 Project Development History - What Was Done

This section documents all the changes and implementations made during the project development. Read this to understand everything that has been done.

### Phase 1: Initial Setup (Basic Load Balancing)
1. ✅ **Created ConfigMap** (`echo-config.yaml`)
   - Set up configuration management for the echo message
   - Configured `MESSAGE` environment variable with value "Hello, Kubernetes!"
   - Separated configuration from application code

2. ✅ **Created Deployment** (`echo-deployment.yaml`)
   - Deployed HashiCorp http-echo server
   - Configured 3 replicas for high availability
   - Set container port to 5678
   - Added resource limits and requests:
     - CPU: 100m-200m
     - Memory: 128Mi-256Mi
   - Implemented health checks:
     - Liveness probe (checks if container is running)
     - Readiness probe (checks if container is ready to serve traffic)
   - Configured environment variable injection from ConfigMap

3. ✅ **Created Service** (`echo-service.yaml`) - Initial Version
   - Initially set up as **NodePort** type
   - Configured port mapping:
     - Service port: 80
     - Target port: 5678
     - NodePort: 30080 (for external access)
   - Enabled automatic load balancing across 3 pods
   - Provided external access via NodePort

### Phase 2: Enhanced Security & Routing (Recent Updates)
4. ✅ **Updated Service Type** (`echo-service.yaml`)
   - **Changed from NodePort to ClusterIP**
   - Improved security by removing direct external access
   - Service now accessible only internally or via Ingress
   - Maintained load balancing functionality

5. ✅ **Added Ingress Resource** (`echo-ingress.yaml`) - NEW
   - Implemented NGINX Ingress Controller integration
   - Configured host-based routing with `echo.local` domain
   - Set up path-based routing (root path `/`)
   - Connected Ingress to echo-service on port 80
   - Provides better security and routing capabilities
   - Enables future HTTPS/TLS support

6. ✅ **Updated Documentation** (`README.md`)
   - Added comprehensive Ingress setup instructions
   - Updated deployment steps to include Ingress
   - Added Ingress prerequisites (NGINX Ingress Controller installation)
   - Updated testing methods (Ingress, Port Forwarding, ClusterIP)
   - Added Ingress host configuration guide
   - Updated cleanup procedures
   - Added this development history section

### Summary of All Files Created/Modified

| File | Status | What Was Done |
|------|--------|---------------|
| `echo-config.yaml` | ✅ Created | ConfigMap for application configuration |
| `echo-deployment.yaml` | ✅ Created | Deployment with 3 replicas, health checks, resource limits |
| `echo-service.yaml` | ✅ Created → Modified | Initially NodePort, changed to ClusterIP for security |
| `echo-ingress.yaml` | ✅ Created (NEW) | NGINX Ingress for external routing |
| `README.md` | ✅ Created → Updated | Comprehensive documentation with all changes |

### Key Improvements Made

1. **Security Enhancement**: Changed Service from NodePort to ClusterIP
   - Removed direct external port exposure
   - Access now controlled via Ingress only

2. **Better Routing**: Added Ingress resource
   - Professional HTTP/HTTPS routing
   - Host-based and path-based routing support
   - Foundation for TLS/SSL certificates

3. **Documentation**: Complete project documentation
   - Step-by-step deployment guide
   - Testing methods for all access types
   - Troubleshooting section
   - Git workflow instructions

### What You Can Learn From This Project

- ✅ Kubernetes ConfigMap for configuration management
- ✅ Deployment with multiple replicas and health checks
- ✅ Service types (NodePort vs ClusterIP)
- ✅ Ingress for external routing
- ✅ Load balancing across multiple pods
- ✅ Resource management (CPU/Memory limits)
- ✅ Health probes (Liveness and Readiness)
- ✅ Environment variable injection from ConfigMap

---

## 🏗️ Architecture

The project consists of four main Kubernetes resources:

1. **ConfigMap** (`echo-config.yaml`) - Stores configuration data (the echo message)
2. **Deployment** (`echo-deployment.yaml`) - Manages 3 replicas of the echo server pods
3. **Service** (`echo-service.yaml`) - Exposes the pods via ClusterIP and handles load balancing
4. **Ingress** (`echo-ingress.yaml`) - Provides HTTP/HTTPS routing and external access via NGINX Ingress Controller

## 📁 Project Structure

```
.
├── echo-config.yaml      # ConfigMap for application configuration
├── echo-deployment.yaml  # Deployment with 3 replicas
├── echo-service.yaml     # Service for load balancing (ClusterIP)
├── echo-ingress.yaml     # Ingress for external routing
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
- **Type**: ClusterIP (internal cluster access)
- **Port Mapping**: 
  - Service port: 80
  - Target port: 5678 (container port)
- **Load Balancing**: Automatically distributes traffic across all 3 pods
- **Note**: Changed from NodePort to ClusterIP for better security (access via Ingress only)

### 4. Ingress (`echo-ingress.yaml`)
- **Ingress Class**: NGINX
- **Host**: `echo.local`
- **Path**: `/` (root path)
- **Backend**: Routes to `echo-service` on port 80
- **Purpose**: Provides external HTTP/HTTPS access and path-based routing
- **Prerequisites**: Requires NGINX Ingress Controller installed in the cluster

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

4. **Create the Ingress** (optional, requires NGINX Ingress Controller):
   ```bash
   kubectl apply -f echo-ingress.yaml
   ```

5. **Verify deployment**:
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
kubectl apply -f echo-config.yaml -f echo-deployment.yaml -f echo-service.yaml -f echo-ingress.yaml
```

Or if you're in the project directory:
```bash
kubectl apply -f .
```

### Prerequisites for Ingress

If you want to use the Ingress resource, ensure NGINX Ingress Controller is installed:

**For Minikube:**
```bash
minikube addons enable ingress
```

**For other clusters:**
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/cloud/deploy.yaml
```

**Verify Ingress Controller:**
```bash
kubectl get pods -n ingress-nginx
```

## 🧪 Testing the Load Balancer

### 1. Test via Ingress (Recommended)

**Configure hosts file** (required for `echo.local`):
- **Windows**: Edit `C:\Windows\System32\drivers\etc\hosts` and add:
  ```
  <ingress-ip> echo.local
  ```
- **Linux/Mac**: Edit `/etc/hosts` and add:
  ```
  <ingress-ip> echo.local
  ```

**Get Ingress IP:**
```bash
kubectl get ingress echo-ingress
```

**Access the application:**
```bash
curl http://echo.local
# Or open in browser: http://echo.local
```

### 2. Test via Port Forwarding

```bash
kubectl port-forward svc/echo-service 8080:80
```

Then access: `http://localhost:8080`

### 3. Test via ClusterIP (Internal Access)

If you need to access the service directly from within the cluster:
```bash
kubectl run curl-test --image=curlimages/curl:latest --rm -it --restart=Never -- curl http://echo-service.default.svc.cluster.local
```

### 4. Test Load Balancing

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

### 5. Verify Pod Distribution

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
kubectl delete -f echo-ingress.yaml
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

### Change Ingress Host

Edit `echo-ingress.yaml`:
```yaml
rules:
  - host: your-custom-host.local  # Change this
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: echo-service
              port:
                number: 80
```

Then update your hosts file accordingly.

## 🎯 Key Features Implemented

✅ **Load Balancing**: Kubernetes Service automatically distributes traffic across pods  
✅ **High Availability**: 3 replicas ensure service continues if a pod fails  
✅ **Health Checks**: Liveness and readiness probes ensure only healthy pods receive traffic  
✅ **Resource Management**: CPU and memory limits prevent resource exhaustion  
✅ **Configuration Management**: ConfigMap separates configuration from application code  
✅ **Ingress Routing**: NGINX Ingress provides external HTTP/HTTPS access with path-based routing  
✅ **Security**: ClusterIP service type restricts direct external access (access via Ingress only)  

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

**Note**: This project demonstrates basic Kubernetes load balancing concepts. For production use, consider adding TLS certificates, more sophisticated monitoring solutions, and proper security policies.

---

## 📝 Change History & Git Workflow

### Recent Changes Made

1. **Added Ingress Resource** (`echo-ingress.yaml`)
   - Implemented NGINX Ingress for external HTTP routing
   - Configured host-based routing with `echo.local` domain
   - Provides better security and routing capabilities compared to NodePort

2. **Updated Service Type** (`echo-service.yaml`)
   - Changed from `NodePort` to `ClusterIP`
   - Improved security by removing direct external access
   - Service now accessible only via Ingress or internal cluster access

3. **Updated Documentation** (`README.md`)
   - Added Ingress configuration and usage instructions
   - Updated deployment steps to include Ingress
   - Added Ingress prerequisites and testing methods
   - Updated cleanup procedures

### Git Workflow: Tracking Changes in a Separate Branch

To track and understand all the changes made, follow these steps to create a separate branch:

#### 1. Check Current Status
```bash
git status
```

#### 2. Create and Switch to a New Branch
```bash
# Create a new branch for your changes
git checkout -b feature/ingress-implementation

# Or if you prefer a different branch name:
git checkout -b feature/load-balancing-updates
```

#### 3. Stage All Changes
```bash
# Add all modified and new files
git add .

# Or add files individually:
git add echo-ingress.yaml
git add echo-service.yaml
git add README.md
```

#### 4. Commit Changes with Descriptive Message
```bash
git commit -m "Add Ingress resource and update Service to ClusterIP

- Added echo-ingress.yaml with NGINX Ingress configuration
- Changed Service type from NodePort to ClusterIP for better security
- Updated README with Ingress setup and usage instructions
- Added prerequisites and testing methods for Ingress"
```

#### 5. Push to Remote Repository
```bash
# Push the new branch to remote
git push -u origin feature/ingress-implementation

# Or if using a different branch name:
git push -u origin feature/load-balancing-updates
```

#### 6. View Changes Made
```bash
# Compare with main/master branch
git diff main..feature/ingress-implementation

# Or view commit history
git log --oneline

# View detailed changes in a specific file
git diff main echo-ingress.yaml
```

#### 7. Create Pull Request (Optional)
After pushing, you can create a Pull Request on GitHub/GitLab to:
- Review the changes
- Discuss modifications
- Merge to main branch when ready

#### 8. Switch Back to Main Branch
```bash
git checkout main
```

#### 9. View All Branches
```bash
# List all local branches
git branch

# List all branches (local and remote)
git branch -a
```

### Summary of Files Changed

| File | Change Type | Description |
|------|-------------|-------------|
| `echo-ingress.yaml` | **New** | Added Ingress resource for external routing |
| `echo-service.yaml` | **Modified** | Changed service type from NodePort to ClusterIP |
| `README.md` | **Modified** | Updated documentation with Ingress instructions |

### Benefits of Using a Separate Branch

- ✅ **Track Changes**: Easily see what was modified and when
- ✅ **Review History**: Understand the evolution of your project
- ✅ **Safe Experimentation**: Test changes without affecting main branch
- ✅ **Easy Rollback**: Revert to previous state if needed
- ✅ **Collaboration**: Share changes with team members for review




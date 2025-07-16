+++
date = '2025-07-16T15:30:18Z'
draft = false
title = 'DevOps Study app: Setting up Kubernetes'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

First of all, let's switch to main and pull.

### K3D

[K3D](https://k3d.io/stable/) is a lightweight wrapper to run [k3s](https://github.com/rancher/k3s) (Rancher Lab’s minimal Kubernetes distribution) in docker.

```
mise use k3d kubectl k9s
```

Let's check everything is running ok with 
```
k3d cluster create mycluster
```

```bash
INFO[0000] Prep: Network
INFO[0000] Created network 'k3d-mycluster'
INFO[0000] Created image volume k3d-mycluster-images
INFO[0000] Starting new tools node...
INFO[0000] Pulling image 'ghcr.io/k3d-io/k3d-tools:5.8.3'
INFO[0001] Creating node 'k3d-mycluster-server-0'
INFO[0002] Starting node 'k3d-mycluster-tools'
INFO[0003] Pulling image 'docker.io/rancher/k3s:v1.31.5-k3s1'
INFO[0009] Creating LoadBalancer 'k3d-mycluster-serverlb'
INFO[0010] Pulling image 'ghcr.io/k3d-io/k3d-proxy:5.8.3'
INFO[0013] Using the k3d-tools node to gather environment information
INFO[0013] HostIP: using network gateway 172.20.0.1 address
INFO[0013] Starting cluster 'mycluster'
INFO[0013] Starting servers...
INFO[0013] Starting node 'k3d-mycluster-server-0'
INFO[0017] All agents already running.
INFO[0017] Starting helpers...
INFO[0017] Starting node 'k3d-mycluster-serverlb'
INFO[0023] Injecting records for hostAliases (incl. host.k3d.internal) and for 2 network members into CoreDNS configmap...
INFO[0025] Cluster 'mycluster' created successfully!
INFO[0025] You can now use it like this:
kubectl cluster-info
```

**Remember**: we are using Docker (the images we made) inside of Docker (dev-container).


## Manifest

### dev directory
make a new directory in the root named ```kubernetes```.

/kubernetes/dev/kustomization.yaml
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - namespace.yaml
  - backend
  - frontend

namespace: study-app
namePrefix: dev-

images:
  - name: backend
    newTag: dev
  - name: frontend
    newTag: dev
```

/kubernetes/dev/namespace.yaml
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: study-app
```

- Resources:
	- The namespace.yaml. 
	- Reads the backend and frontend directories.
- Apply the namespace ```study-app```.
- Have a name prefix: everything going to have ```dev-``` in front of it.
- Images:
	- both backend and frontend getting a new name "dev". 

### backend directory

dev/backend/kustomization.yaml
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - ../../base/backend
```

- Points to the ```/manifest/base/backend```.

#### frontend directory

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - ../../base/frontend

# Patch for the frontend deployment
patches:
  - target:
      kind: Deployment
      name: frontend
      namespace: study-app
    patch: |-
      - op: replace
        path: /spec/template/spec/containers/0/env
        value:
          - name: DEBUG
            value: "true"
          - name: API_URL
            value: "http://dev-backend:22112"
```

- Points to the ```/manifest/base/frontend```.
- Patches:
	- Target the deployment.
	- Replace environment variables:
		- DEBUG to true.
		- Change the API_URL to ```http://dev-backend:22112```.

### Base directory

#### kustomization

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - backend
  - frontend
```

- Reading the backend and frontend directories.

#### backend

deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
  namespace: study-app
spec:
  replicas: 1
  selector:
    matchLabels:
      component: backend
  template:
    metadata:
      labels:
        component: backend
    spec:
      containers:
        - name: backend
          image: backend:latest
          ports:
            - containerPort: 22112
          resources:
            limits:
              memory: "512Mi"
            requests:
              cpu: "100m"
              memory: "128Mi"
```

- The tag new is going to change from ```latest``` to ```dev``` like we saw above.
- 1 replica.
- Expose port 22112.
- Giving resources:
	- Memory.
	- CPU.

service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend
  namespace: study-app
spec:
  selector:
    component: backend
  ports:
    - port: 22112
  type: LoadBalancer
```

- Creating a Load-Balancer service on port 22112.

#### frontend

deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  namespace: study-app
spec:
  replicas: 1
  selector:
    matchLabels:
      component: frontend
  template:
    metadata:
      labels:
        component: frontend
    spec:
      containers:
        - name: frontend
          image: frontend:latest
          ports:
            - containerPort: 22111
          env:
            - name: API_URL
              value: "http://backend:22112"
          resources:
            limits:
              memory: "512Mi"
            requests:
              cpu: "100m"
              memory: "128Mi"
```

- 1 replica.
- The tag new is going to change from ```latest``` to ```dev``` like we saw above.
- Expose port 22111.
- Giving resources:
	- Memory.
	- CPU.

kustomization.yaml
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - deployment.yaml
  - service.yaml
```

service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend
  namespace: study-app
spec:
  selector:
    component: frontend
  ports:
    - port: 22111
  type: LoadBalancer
```

- Creating a Load-Balancer service on port 22111.


### Make the images for Kubernetes

In the manifest, we wrote on images named ```backend:dev``` and ```frontend:dev``` and we don't have them.
As DevOps engineer, we want the making of the images well be automated- we going to use a script for that. 

First, let's make a k3d-config.yaml
```yaml
apiVersion: k3d.io/v1alpha5
kind: Simple
metadata:
  name: study-app-cluster
servers: 1
agents: 1
options:
  k3s:
    extraArgs:
      - arg: --disable=traefik
        nodeFilters:
          - server:*
```

- In K3D we can define clusters in a yaml file:
	- 1 server.
	- 1 agent.
	- K3S:
		- disable traefik: (reverse proxy) to all servers.

#### Bash script

kubernetes/setup_cluster_local
```bash
#!/bin/bash
set -e

# Colors for better output
GREEN='\033[0;32m'
RED='\033[0;31m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# Script directory
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
BASE_DIR="$(dirname "$SCRIPT_DIR")"
CLUSTER_NAME="study-app-cluster"

echo -e "${GREEN}DevOps Study App - Kubernetes Deployment Helper${NC}"
echo -e "${YELLOW}This script will set up a k3d cluster and deploy the study app.${NC}"
echo ""

# Check for required tools
check_dependency() {
  if ! command -v "$1" &>/dev/null; then
    echo -e "${RED}Error: $1 is not installed. Please install it before proceeding.${NC}"
    exit 1
  fi
}

echo "Checking dependencies..."
check_dependency k3d
check_dependency kubectl
check_dependency docker
echo -e "${GREEN}All dependencies are installed.${NC}"
echo ""

# Check if cluster exists
if k3d cluster list | grep -q "$CLUSTER_NAME"; then
  echo -e "${YELLOW}Cluster $CLUSTER_NAME already exists.${NC}"
  read -p "Do you want to delete and recreate it? (y/n): " -r
  if [[ $REPLY =~ ^[Yy]$ ]]; then
    echo "Deleting existing cluster..."
    k3d cluster delete "$CLUSTER_NAME"
  else
    echo "Using existing cluster."
  fi
fi

# Create cluster if it doesn't exist
if ! k3d cluster list | grep -q "$CLUSTER_NAME"; then
  echo "Creating k3d cluster using config file..."
  k3d cluster create --config "$SCRIPT_DIR/k3d-config.yaml"
  echo -e "${GREEN}Cluster created successfully!${NC}"
fi

# Configure kubectl to use the cluster
echo "Configuring kubectl to use the cluster..."
kubectl config use-context k3d-"$CLUSTER_NAME"

# Build Docker images
echo "Building Docker images..."
echo "Building backend image..."
docker build -t backend:dev -f "$BASE_DIR/src/backend/Dockerfile" "$BASE_DIR/src/backend"

echo "Building frontend image..."
docker build -t frontend:dev -f "$BASE_DIR/src/frontend/Dockerfile" "$BASE_DIR/src/frontend"

# Import images into k3d
echo "Importing images into k3d..."
k3d image import backend:dev -c "$CLUSTER_NAME"
k3d image import frontend:dev -c "$CLUSTER_NAME"

# Deploy the application using kustomize
echo "Deploying application using kustomize..."
kubectl apply -k "$SCRIPT_DIR/manifests/dev"

# Wait for pods to be ready
echo "Waiting for pods to be ready..."
kubectl wait --for=condition=Ready pods --all -n study-app --timeout=120s

# Get service info
echo -e "${GREEN}Application deployed successfully!${NC}"
echo "Getting service information..."
kubectl get services -n study-app

# Function to wait for LoadBalancer IP assignment and get URL
get_service_url() {
  local service_name=$1
  local max_attempts=30
  local attempt=1

  echo -e "Waiting for $service_name external IP..."

  while [ $attempt -le $max_attempts ]; do
    local ip
    local port

    ip=$(kubectl get svc "$service_name" -n study-app -o=jsonpath='{.status.loadBalancer.ingress[0].ip}' 2>/dev/null)
    port=$(kubectl get svc "$service_name" -n study-app -o=jsonpath='{.spec.ports[0].port}' 2>/dev/null)

    if [ -n "$ip" ] && [ -n "$port" ]; then
      echo "http://$ip:$port"
      return 0
    fi

    echo -n "."
    sleep 2
    ((attempt++))
  done

  echo ""
  echo -e "${RED}Could not get external IP for $service_name after $max_attempts attempts${NC}"
  return 1
}

# Get frontend and backend service URLs
FRONTEND_URL=$(get_service_url "dev-frontend")
BACKEND_URL=$(get_service_url "dev-backend")

echo -e "\n${GREEN}Access your application via LoadBalancer external IPs:${NC}"
if [ -n "$FRONTEND_URL" ] && [ -n "$BACKEND_URL" ]; then
  echo -e "Frontend: ${YELLOW}$FRONTEND_URL${NC}"
  echo -e "Backend API: ${YELLOW}$BACKEND_URL${NC}"
  echo -e "Backend health check: ${YELLOW}$BACKEND_URL/health${NC}"
else
  echo -e "\n${RED}Could not determine service URLs. Please check the service status:${NC}"
  echo -e "${YELLOW}kubectl get svc -n study-app${NC}"
fi

# Extract service ports for port-forwarding instructions
FRONTEND_PORT=$(kubectl get svc dev-frontend -n study-app -o=jsonpath='{.spec.ports[0].port}' 2>/dev/null)
BACKEND_PORT=$(kubectl get svc dev-backend -n study-app -o=jsonpath='{.spec.ports[0].port}' 2>/dev/null)

echo -e "\n${GREEN}Alternative access via port-forwarding (for local development):${NC}"
echo -e "To access via port-forwarding, run these commands in separate terminals:"
echo -e "${YELLOW}kubectl port-forward svc/dev-frontend -n study-app $FRONTEND_PORT:$FRONTEND_PORT${NC}"
echo -e "${YELLOW}kubectl port-forward svc/dev-backend -n study-app $BACKEND_PORT:$BACKEND_PORT${NC}"
echo -e "Then access the application at:"
echo -e "Frontend: ${YELLOW}http://localhost:$FRONTEND_PORT${NC}"
echo -e "Backend API: ${YELLOW}http://localhost:$BACKEND_PORT${NC}"
echo -e "Backend health check: ${YELLOW}http://localhost:$BACKEND_PORT/health${NC}"

echo -e "\nTo delete the cluster when finished:"
echo -e "${YELLOW}k3d cluster delete $CLUSTER_NAME${NC}"
```

Don't forget to ```chmod +x setup_cluster_local```.

What the script do:
1. Check if all dependencies are installed.
2. Create a cluster using the ```k3d-config.yaml``` file.
3. Build images locally- with tag.
4. Import the images to K3D.
5. Applying the manifest.
6. Creating the namespaces, and Load-Balancers.

The script:
- Setting script directory- to use it with mise.
- Check for dependencies:
	- K3D.
	- kubectl.
	- docker.
- Checking if cluster exists:
	- If it does, asking for delete and recreate.
	- If don't exists, create it.
- Configure kubectl to use in the cluster.
- Build Docker images:
	- Build back-end image.
	- Build the front-end image.
- Imports the images to K3D.
- Apply the app with Kustomize. 
- Function for Load-Balancer:
	- Max attempts is 30.
	- Getting all the information from kubectl:
		- IP.
		- Port.

### Task with mise

We can add out script to mise to work as an alias:
```toml
[task.build]
description = "Script"
run = "build"
```

Or with file task
```toml
#!.usr.bin/env bash
#MISE description = "Script"
build
```

[mise task documentation](https://mise.jdx.dev/tasks/).

We going to use the File task option:

```
[tasks.k8s-setup-local]
description = "Set up a basic Kubernetes cluster that builds the local images and applies them"
run = "bash ./kubernetes/setup_cluster_local"
```

Now, if we do ```mise run k8s-setup-local``` 

### Minimal cluster script

Let's add a script for a minimal script that's only creates a cluster:
```
#!/bin/bash
set -e

# Colors for better output
GREEN='\033[0;32m'
RED='\033[0;31m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

# Script directory
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
CLUSTER_NAME="study-app-cluster"

echo -e "${GREEN}DevOps Study App - Minimal Kubernetes Cluster Setup${NC}"
echo -e "${YELLOW}This script will set up a k3d cluster without deploying any applications.${NC}"
echo ""

# Check for required tools
check_dependency() {
  if ! command -v "$1" &>/dev/null; then
    echo -e "${RED}Error: $1 is not installed. Please install it before proceeding.${NC}"
    exit 1
  fi
}

echo "Checking dependencies..."
check_dependency k3d
check_dependency kubectl
echo -e "${GREEN}All dependencies are installed.${NC}"
echo ""

# Check if cluster exists
if k3d cluster list | grep -q "$CLUSTER_NAME"; then
  echo -e "${YELLOW}Cluster $CLUSTER_NAME already exists.${NC}"
  read -p "Do you want to delete and recreate it? (y/n): " -r
  if [[ $REPLY =~ ^[Yy]$ ]]; then
    echo "Deleting existing cluster..."
    k3d cluster delete "$CLUSTER_NAME"
  else
    echo "Using existing cluster."
  fi
fi

# Create cluster if it doesn't exist
if ! k3d cluster list | grep -q "$CLUSTER_NAME"; then
  echo "Creating k3d cluster using config file..."
  k3d cluster create --config "$SCRIPT_DIR/k3d-config.yaml"
  echo -e "${GREEN}Cluster created successfully!${NC}"
fi

# Configure kubectl to use the cluster
echo "Configuring kubectl to use the cluster..."
kubectl config use-context k3d-"$CLUSTER_NAME"

echo -e "\n${GREEN}Cluster is now ready!${NC}"
echo -e "You can deploy applications manually or use one of the other setup scripts to deploy the study app."

echo -e "\nTo check your cluster nodes:"
echo -e "${YELLOW}kubectl get nodes${NC}"

echo -e "\nTo create the application namespace:"
echo -e "${YELLOW}kubectl create namespace study-app${NC}"

echo -e "\nTo delete the cluster when finished:"
echo -e "${YELLOW}k3d cluster delete $CLUSTER_NAME${NC}"
```

Add it to mise.toml:
```
[tasks.k8s-setup-minimal]
description = "Set up a basic Kubernetes cluster that builds the local images and applies them"
run = "bash ./kubernetes/setup_cluster_minimal"
```


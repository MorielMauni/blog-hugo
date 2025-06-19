+++
date = '2025-06-19T12:29:41Z'
draft = true
title = 'Mini-Homelab'
author = 'Moriel Mauni'
tags = ["Homelab", "kubernetes", "docker"]
categories = ["Homelab", "docker", "kubernetes"]
description = "The start of building the homelab using kubernetes"
+++

### Intro 

The full Kubernetes home-lab is very interesting. You have an open hand to do what ever you want, how ever you want it, and the best thing, it's going to help you learn about Kubernetes the best way you can - Hands-on.

### Pre-requisites

1. Linux machine: any will work.
2. A server: can be old laptop, PC or Raspberry Pi.
3. Wired internet connection.

#### Installations
1. Update the machine.
2. Install Rancher Desktop: will help manage containers with GUI when needed.
```bash 
curl -s https://download.rancherdesktop.io/install.sh | sh
sudo apt update
sudo apt install -y rancher-desktop
```

3. Install K9S
```bash 
curl -sLO https://github.com/derailed/k9s/releases/download/<version>/k9s_Linux_amd64.tar.gz

# Extract the tarball
tar -xzf k9s_Linux_amd64.tar.gz

# Move k9s to /usr/local/bin
sudo mv k9s /usr/local/bin/

# Make it executable
sudo chmod +x /usr/local/bin/k9s

# Clean up
rm k9s_Linux_amd64.tar.gz
```

4. Install kubectl
```bash 
# Download the latest kubectl binary
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Make the binary executable
chmod +x kubectl

# Move kubectl to /usr/local/bin
sudo mv kubectl /usr/local/bin/

# Verify installation
kubectl version --client
```

### kubectl 

This is Kubernetes CLI tool. [kubectl docs](https://kubernetes.io/docs/reference/kubectl/).

#### Commands

You can pick the workspace that the pods will be in:
```bash 
kubectl config use-context <context-name>
```

For example: I can work with rancher-desktop or k3s (I use both)
```bash 
k config use-context rancher-desktop 
```

```bash 
k config use-context k3s 
```

For now, we need that rancher-desktop will be our set.

### Pod Management

There are many commands to manage your pods. 

```bash
kubectl run <pod-name> --image=<image-name>
kubectl get pods
kubectl describe pod <pod-name> | less
kubectl get pods -o wide
kubectl get <pod-name> -o yaml | less
kubectl edit pod/<pod-name>
```



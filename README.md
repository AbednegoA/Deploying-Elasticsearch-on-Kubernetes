# Elasticsearch on Kubernetes
This project is used to deploy Elasticsearch on kubernetes

# How to Connect to K8s Cluster
```bash 
export KUBECONFIG=./kubeconfig_244894765
```
# Install kubectl binary with curl on Linux 
- Download the latest release with the command
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

# Elasticsearch on Kubernetes
This project is used to deploy Elasticsearch on kubernetes

# Install kubectl binary with curl on Linux 
- Download the latest release with the command
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
- Install kubectl 
```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
- Test to ensure the version you installed is up-to-date 
```bash
kubectl version --client
```

# Connect to K8s Cluster
```bash 
export KUBECONFIG=./kubeconfig_244894765
```
![Screenshot (7)](https://user-images.githubusercontent.com/104996017/167937536-6b092983-6ec8-4fe7-a864-f6de7e3ebc8d.png)

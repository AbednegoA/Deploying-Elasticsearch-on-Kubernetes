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

# Install Helm 
```bash 
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```
- Check helm version
```bash 
helm --version 
```

# Install Elasticsearch using helm 
- Add repo
```bash 
helm repo add elastic https://helm.elastic.co
```
- Install chart 
```bash 
helm install my-elasticsearch elastic/elasticsearch --version 7.17.3
```
- Check Elasticsearch version
```bash 
elasticsearch --version 
```
# New ES cluster
```bash 
kubectl get pods -o wide 
```
![Screenshot 2022-05-14 162629](https://user-images.githubusercontent.com/104996017/168448653-a467273a-b0da-4e25-ae3c-e0286e32faee.png)

# Install kibana using helm 




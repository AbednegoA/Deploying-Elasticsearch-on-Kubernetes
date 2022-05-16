# Elasticsearch on Kubernetes
This project is used to deploy Elasticsearch, logstash and kibana (ELK stack) on kubernetes with helm. Argo CD serves as a maintainer and it ensures the application is evergreen. With Argo we can carry out deployment strategies like canary or blue green.

# Usage 
- Logstash is used for data-processing
- Elasticsearch used for data storage 
- Kibana is used to visualize data 
```bash 
logstash(data processing) -> Elasticsearch(data storage) -> Kibana(data visualization)
```
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

# Deploying Elasticsearch with helm 
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

# Deploying kibana with helm 
- Add repo 
```bash 
helm repo add elastic https://helm.elastic.co
```
- Install Chart 
```bash 
helm install my-kibana elastic/kibana --version 7.17.3
```
# Deploying Logstash with helm
- Add repo 
```bash 
helm repo add elastic https://helm.elastic.co
```
- Install chart 
```bash 
helm install my-logstash elastic/logstash --version 7.17.3
```
# Deploying Argo CD 
```bash 
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml
```
- Access the Argo CD api server 
```bash 
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```
- External link 
a540ef787b23c4783bc735c8426a68fb-8818170.eu-west-1.elb.amazonaws.com

![Screenshot (14)](https://user-images.githubusercontent.com/104996017/168541098-ec7a67e5-6ac9-444b-a5de-8a1c6c88cfae.png)

# Download Helm Charts Locally 
- Helm pull 
```bash 
helm pull elastic/elasticsearch -d ./Picnic-Home-Assignment/ --untar
helm pull elastic/kibana -d ./Picnic-Home-Assignment/ --untar
helm pull elastic/logstash -d ./Picnic-Home-Assignment/ --untar
```
# Used Git as Version Control System
- ./Picnic-Home-Assignment
```bash 
git init 
git config --global user.name "username"
git config --global user.email "email"
git status 
git add .
git status 
git commit -m " new message" 
git push origin master 
```
# External ip using port-forwarding 
- Elasticsearch
```bash 
kubectl port-forward svc/elasticsearch-master 9200
```
![Screenshot (11)](https://user-images.githubusercontent.com/104996017/168546563-27e71fbf-5414-4eb1-b75d-b34f035e0654.png)


- Kibana 
```bash 
kubectl port-forward deployment/kibana-kibana 5601 
```
![Screenshot (10)](https://user-images.githubusercontent.com/104996017/168546606-ecbb7fa4-8aeb-4374-9546-3070d6c8b363.png)

- Argo CD 
```bash 
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
![Screenshot (13)](https://user-images.githubusercontent.com/104996017/168547427-8c70ef57-d627-4d67-a875-a31d08278408.png)

# Exposing ip using ingress rules
- Argo CD 
```bash 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-server-ingress
  namespace: argocd
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
spec:
  rules:
  - host: argocd.example.com
    http:
      paths:
      - backend:
          serviceName: argocd-server
          servicePort: https
```

- Elasticsearch 
```bash 
apiVersion: elasticsearch.k8s.elastic.co/v1beta1
kind: Elasticsearch
metadata:
  name: elasticsearch-master
  namespace: default 
spec:
  version: 7.4.0
  http:
    service:
      spec:
        type: ClusterIP
    tls:
      selfSignedCertificate:
        disabled: true
  nodeSets:
  - name: master
    count: 1
    nodeSelector:
      component: elasticsearch
    volumeClaimTemplates:
    - metadata:
        name: elasticsearch-master
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 5Gi
        storageClassName: multik8s-nfs-storage
    - metadata:
        name: elasticsearch-data
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 5Gi
        storageClassName: multik8s-nfs-storage
    config:
      node.master: true
      node.data: true
      node.ingest: true
      node.store.allow_mmap: false
'''
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
  name: elasticsearch
  namespace: default
spec:
  tls:
    - hosts:
      - elasticsearch.foo.bar
      secretName: tls-secret
  rules:
    - host: elasticsearch.foo.bar
      http:
        paths:
          - path: /
            backend:
              serviceName: elasticsearch-master
              servicePort: 9200
```
- Kibana 
```bash 
apiVersion: kibana.k8s.elastic.co/v1beta1
kind: Kibana
metadata:
  name: my-kibana-kibana
  namespace: default 
spec:
  version: 7.4.0
  #http:
  #  tls:
  #    certificate:
  #      secretName: tls-secret-test
  http:
    service:
      spec:
        type: ClusterIP
    tls:
      selfSignedCertificate:
        disabled: true
  count: 1
  elasticsearchRef:
    name: elasticsearch-test
'''
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: nginx
  name: my-kibana-kibana
  namespace: default
spec:
  tls:
    - hosts:
      - kibana.foo.bar
      secretName: tls-secret
  rules:
    - host: kibana.foo.bar
      http:
        paths:
          - path: /
            backend:
              serviceName: my-kibana-kibana 
              servicePort: 5601
```

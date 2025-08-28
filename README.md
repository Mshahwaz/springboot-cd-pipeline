# PRODUCTION GRADE DEVSECOPS CICD Pipeline

## Create 1 EC2 server
- [ ] Minikube server with 4 GB storage - t2.medium

## Setup Minikube
```
 Installing Docker using apt-repo
# Add Docker's official GPG key:
$ sudo apt-get update
$ sudo apt-get install ca-certificates curl 
$ sudo install -m 0755 -d /etc/apt/keyrings
$ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
$ sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
$ sudo docker version
$ sudo usermod -aG docker $USER && newgrp docker

 Install kubectl
$  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
$ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
$ kubectl version --client

 Install Minikube
$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/v1.23.2/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/

 Start Minikube
$  sudo apt install conntrack ----> Only if you run minikube on bare metal
$  minikube start --vm-driver=docker --ports=32000:32000,31000:31000 ## 1-for ArgoCD Server and 1-for          springboot-Application
```

## Setup ArgoCD
```
$ kubectl create namespace argocd
$ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.5.8/manifests/install.yaml
$ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort","ports": [{"name": "http","port": 80,"targetPort": 8080,"nodePort": 32000}]}}' 

For version 1.9 or later:( to get initial admin password)
$ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

# Documentation: Kubernetes and Docker Setup on a Linux VM (Ubuntu)

## 1. Install kubectl

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client

## 2. Install minikube

curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64

minikube version

## 3. Install Docker

sudo rm -rf /etc/apt/keyrings/docker.gpg
sudo mkdir -p /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo docker --version

### Test Docker Installation

docker run -d nginx
docker ps
docker exec -it <container_id> /bin/bash
docker stop <container_id>
docker rm <container_id>

## 4. Set Up a Kubernetes Cluster Using Minikube

minikube start --driver=docker
docker ps

### Example output:
 CONTAINER ID   IMAGE                                 COMMAND                  CREATED         STATUS         PORTS
 4732972f87ac   gcr.io/k8s-minikube/kicbase:v0.0.46   "/usr/local/bin/entr..."   3 minutes ago   Up 3 minutes

minikube status
minikube ip

### Proxy Configuration (to be repeated each session):

export NO_PROXY=192.168.49.2,127.0.0.1,localhost
export no_proxy=192.168.49.2,127.0.0.1,localhost
source ~/.bashrc

### Stop and Restart Minikube

minikube stop
minikube start
kubectl version
kubectl cluster-info

### Create an Alias for kubectl (to be repeated each session):

alias k=kubectl
k cluster-info

## 5. Explore the Kubernetes Cluster

### Basic Commands

k get nodes
k get namespaces
k get pods --namespace=kube-system

### Access the Minikube Node

minikube ip
ping <minikube_ip>
minikube ssh

### Check running containers inside the node:

docker ps
exit

## 6. Create Pods and Containers

### Single Pod with a Container

k run my-nginx-pod --image=nginx
k get pods
k describe pod my-nginx-pod

###  Retrieve the Pod's IP 

minikube ssh
ping <pod_ip>
curl <pod_ip>
docker ps | grep nginx

### Delete the Pod

k delete pod my-nginx-pod
k get pods

## 7. Deployments

### Create a Deployment

k create deployment my-nginx-deploy --image=nginx
k describe deploy my-nginx-deploy
k describe pod <pod_name>

### Scale the Deployment

k scale deploy my-nginx-deploy --replicas=10
k get pods -o wide
k scale deploy my-nginx-deploy --replicas=4

get replicaset

k edit deployment my-nginx-deploy
k logs my-nginx-deploy-75488fc988-vp8t6

k exec -it my-nginx-deploy-75488fc988-vp8t6 -- bin/bash

k delete deployment  my-nginx-deploy-75488fc988-vp8t6

## 8. Access Pods Externally

k expose deployment my-nginx-deploy --type=NodePort --port=80
k get services

### Minikube service URL
minikube service my-nginx-deploy --url

### Access the service via the URL
curl $(minikube service my-nginx-deploy --url)

### View cluster info
k cluster-info dump

### Stop Minikube
minikube stop

# Delete all Minikube data
minikube delete

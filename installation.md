# Documentation: Kubernetes and Docker Setup on a Linux VM (Ubuntu)

---

## 1. Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

---

## 2. Install minikube

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
minikube version
```

---

## 3. Install Docker

```bash
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
```

### Test Docker Installation

```bash
docker run -d nginx
docker ps
docker exec -it <container_id> /bin/bash
docker stop <container_id>
docker rm <container_id>
```

---

## 4. Set Up a Kubernetes Cluster Using Minikube

```bash
minikube start --driver=docker
docker ps
```

### Example output:
```
CONTAINER ID   IMAGE                                 COMMAND                  CREATED         STATUS         PORTS
4732972f87ac   gcr.io/k8s-minikube/kicbase:v0.0.46   "/usr/local/bin/entr..."   3 minutes ago   Up 3 minutes
```

```bash
minikube status
minikube ip
```

### Proxy Configuration and an Alias for kubectl (to be repeated each session):

```bash
export NO_PROXY=192.168.49.2,127.0.0.1,localhost
export no_proxy=192.168.49.2,127.0.0.1,localhost
source ~/.bashrc
alias k=kubectl
```

### Stop and Restart Minikube

```bash
minikube stop
minikube start
kubectl version
kubectl cluster-info
```
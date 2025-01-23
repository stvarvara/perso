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

### Proxy Configuration (to be repeated each session):

```bash
export NO_PROXY=192.168.49.2,127.0.0.1,localhost
export no_proxy=192.168.49.2,127.0.0.1,localhost
source ~/.bashrc
```

### Stop and Restart Minikube

```bash
minikube stop
minikube start
kubectl version
kubectl cluster-info
```

### Create an Alias for kubectl (to be repeated each session):

```bash
alias k=kubectl
k cluster-info
```

---

## 5. Explore the Kubernetes Cluster

### Basic Commands

```bash
k get nodes
k get namespaces
k get pods --namespace=kube-system
```

### Access the Minikube Node

```bash
minikube ip
ping <minikube_ip>
minikube ssh
```

### Check running containers inside the node:

```bash
docker ps
exit
```

---

## 6. Create Pods and Containers

### Single Pod with a Container

```bash
k run my-nginx-pod --image=nginx
k get pods
k describe pod my-nginx-pod
```

### Retrieve the Pod's IP 

```bash
minikube ssh
ping <pod_ip>
curl <pod_ip>
docker ps | grep nginx
```

### Delete the Pod

```bash
k delete pod my-nginx-pod
k get pods
```

---

## 7. Deployments

### Create a Deployment

```bash
k create deployment my-nginx-deploy --image=nginx
k describe deploy my-nginx-deploy
k describe pod <pod_name>
```

### Commands for the Deployment

```bash
k scale deploy my-nginx-deploy --replicas=10
k get pods -o wide
k scale deploy my-nginx-deploy --replicas=4
```

```bash
get replicaset
k edit deployment my-nginx-deploy
k logs my-nginx-deploy-75488fc988-vp8t6
k exec -it my-nginx-deploy-75488fc988-vp8t6 -- bin/bash
k delete deployment my-nginx-deploy-75488fc988-vp8t6
```

### Kubernetes config files

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy2
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.16
        ports:
        - containerPort: 80
```

```bash
k apply -f config-file.yaml
```

---

## 8. Config File Syntax

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: 
  labels:
spec: 
  replicas:
  selector:
    matchLabels:
      app: 
  template: 
    metadata:
      labels:
        app:
    spec:
      containers:
      - name:
        image:
        ports:
        - containerPort: 8080
```

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: 
spec:
  selector:
    app: 
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

---
### Example
```bash
nano httpd-deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deploy
  labels:
    app: httpd
spec:
  replicas: 3
  selector:
    matchLabels:
      app: httpd
  template:
    metadata:
      labels:
        app: httpd
    spec:
      containers:
      - name: httpd
        image: httpd:2.4
        ports:
        - containerPort: 80
```
```bash
nano httpd-service.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: httpd-service
spec:
  selector:
    app: httpd
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort

```

```bash
kubectl apply -f httpd-deployment.yaml
kubectl apply -f httpd-service.yaml
kubectl get deployments
kubectl get services
minikube service httpd-service --url
curl <url>


```
### Delete

```bash
kubectl delete -f httpd-deployment.yaml
kubectl delete -f httpd-service.yaml



```
---

## 9. Access Pods Externally

```bash
k expose deployment my-nginx-deploy --type=NodePort --port=80
k get services
```

### Minikube service URL

```bash
minikube service my-nginx-deploy --url
```

### Access the service via the URL

```bash
curl $(minikube service my-nginx-deploy --url)
```

### View cluster info

```bash
k cluster-info dump
```

### Stop Minikube

```bash
minikube stop
```

### Delete all Minikube data

```bash
minikube delete
```

--- 

## 9. MongoDB application



## MongoDB pod

```bash
echo -n 'labo' | base64
echo -n '1234labo' | base64
nano mongodb-secret.yaml
```
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongodb-secret
type: Opaque
data:
  mongo-root-username: bGFibw==
  mongo-root-password: MTIzNGxhYm8=
```
```bash
kubectl apply -f mongodb-secret.yaml

nano mongodb-deploy.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb  
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password

        
```
```bash
k apply -f mongodb-deploy.yaml
```
## Internal Service

## MongoExpress deployement (we need MongoDB url (ConfigMap) and credentials (Secret ) to communicate -> deployement file) 


## External Service

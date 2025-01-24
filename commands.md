# Bais Commands and tests with Kubernetes

---

## 1. Explore the Kubernetes Cluster

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

## 2. Create Pods and Containers

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

## 3. Deployments

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

## 4. Config File Syntax

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

## 5. Access Pods Externally

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

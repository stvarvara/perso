# Kubernetes Ingress

---

## Config FIle

### BackEnd Internal Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-internal-service 
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

### Ingress

```yaml
apiVersion: v1
kind: Ingress
metadata:
  name: myapp-ingress 
spec:
  rules:
  - host: myapp.com
    http:
        paths:
        - backend:
            serviceName: myapp-internal-service
            servicePort: 8080
```

```bash
k get namespaces
```

## Use

* Should be a real domain name
* You have to map domain name to Node's IP adress
* 1 - Backend, 2 - Ingress, 3- Ingress Controller (another pod to controll the rules of the ingress - K8s Nginx Ingress Controller)

```bash
k create namespace test
k delete namespace test
```

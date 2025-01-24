# Kubernetes MongoDB simple App

---

## Secret for credentials + MongoDB Pod Deployment + Internal Service

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
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
  labels:
    app: mongodb
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017

```

```bash
k apply -f mongodb-deploy.yaml
```

## MongoExpress deployement (we need MongoDB url (ConfigMap) and credentials (Secret ) to communicate -> deployement file)

## External Service

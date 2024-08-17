# 🌟 Kubernetes Django Notes Application 🌟

Welcome to the Kubernetes Django Notes Application repository! This guide provides a comprehensive, end-to-end deployment of a Django application on Kubernetes.

## 📑 Table of Contents

1. [🛠️ Project Setup](#-project-setup)
2. [📦 Dockerizing the Django Application](#-dockerizing-the-django-application)
3. [🔧 Kubernetes Configuration](#-kubernetes-configuration)
4. [🔒 Securing the Application](#-securing-the-application)
5. [🌐 Accessing the Application](#-accessing-the-application)
6. [🚀 Deployment](#-deployment)
7. [📝 Conclusion](#-conclusion)

## 🛠️ Project Setup

1. **Create a Project Directory**

   ```bash
   mkdir k8s-django-notes-app
   cd k8s-django-notes-app
   ```

2. **Clone the Repository**

```bash
git clone https://github.com/ojasjawale/k8s-notes-app
cd k8s-notes-app
```

## 📦 Dockerizing the Django Application

1. **Create a Dockerfile**

```bash
# Use the official Django image from the Docker Hub
FROM python:3.11-slim

# Set the working directory
WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application code
COPY . .

# Expose the port the app runs on
EXPOSE 8000

# Command to run the application
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "myproject.wsgi:application"]
```

2. **Build the Docker Image**

```bash
docker build -t ojasjawale/notes-app:latest .
```

3. **Push to Docker Hub**

```bash
docker push ojasjawale/notes-app:latest
```

## 🔧 Kubernetes Configuration

1. **Setup minikube**
https://ojasj45.hashnode.dev/k8s-minikube

2. **Create Namespace**

```bash
apiVersion: v1
kind: Namespace
metadata:
  name: notes-app
```

3. **Deployment Configuration**

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: notes-app-deployment
  namespace: notes-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: notes-app
  template:
    metadata:
      labels:
        app: notes-app
    spec:
      containers:
      - name: notes-app
        image: ojasjawale/notes-app:latest
        ports:
        - containerPort: 8000
```

4. **Service Configuration**

```bash
apiVersion: v1
kind: Service
metadata:
  name: notes-app-service
  namespace: notes-app
spec:
  selector:
    app: notes-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8000
```

5. **Ingress Configuration**

```bash
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: notes-app-ingress
  namespace: notes-app
spec:
  rules:
  - host: notes-app
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: notes-app-service
            port:
              number: 80
      - path: /app
        pathType: Prefix
        backend:
          service:
            name: notes-app-service
            port:
              number: 80
```

6. **Persistent Volume and Claim**

```bash
#Persistent Volume
apiVersion: v1
kind: PersistentVolume
metadata:
  name: django-pv
  namespace: notes-app
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
```
```bash
Persistent Volume Claim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: django-pvc
  namespace: notes-app
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## 🔒 Securing the Application

1. **Create a Secret**

```bash
apiVersion: v1
kind: Secret
metadata:
  name: django-secrets
  namespace: notes-app
type: Opaque
data:
  secret_key: c2VjcmV0X3N0cmluZw==  # Base64 encoded secret key
```

## 🌐 Accessing the Application

1. **Apply Kubernetes Configurations**

```bash
kubectl apply -f namespace.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
kubectl apply -f secret.yaml
kubectl apply -f psp.yaml
```

2. **Update /etc/host**

```bash
<minikube-ip> notes-app
```

## 🚀 Deployment

1. **Deploy the Application**

Ensure all YAML files are correctly applied and your application should be live.

## 📝 Conclusion

Congratulations! 🎉 You've successfully deployed a Django application on Kubernetes.

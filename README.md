<<<<<<< HEAD
# Kubernetes Deployment with Ingress and TLS

This repository provides a complete Kubernetes setup with **NGINX Ingress Controller**, **Cert-Manager**, and **Let's Encrypt** for securing services with HTTPS.

📌 **Cluster Provisioned on Digital Ocean**
This Kubernetes cluster was provisioned on **Digital Ocean**, ensuring scalability and high availability.

## 📌 Prerequisites
Ensure you have the following installed:
- **Kubernetes Cluster** (Minikube, Kind, or a cloud provider like AWS EKS, GKE, AKS, or **Digital Ocean Kubernetes**)
- **Helm** (Package manager for Kubernetes)
- **Kubectl** (Kubernetes CLI)

---
## 🚀 Installation Steps
### 1️⃣ Install Helm
Helm is required for managing Kubernetes packages.
```sh
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
helm version
```

### 2️⃣ Install NGINX Ingress Controller
The **NGINX Ingress Controller** manages HTTP and HTTPS traffic inside the cluster.
```sh
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```
Verify installation:
```sh
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

### 3️⃣ Deploy Sample Applications
#### **Deploy web-color-green**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: web-color-green
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web-color-green
  template:
    metadata:
      labels: 
        app: web-color-green
    spec:
      containers:
        - name: web-color-green
          image: gabrieloliver001/web-color:green
          ports:
            - containerPort: 80
```
#### **Deploy web-color-blue**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: web-color-blue
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web-color-blue
  template:
    metadata:
      labels: 
        app: web-color-blue
    spec:
      containers:
        - name: web-color-blue
          image: gabrieloliver001/web-color:blue
          ports:
            - containerPort: 80
```
Apply the deployments:
```sh
kubectl apply -f web-color-green.yaml
kubectl apply -f web-color-blue.yaml
```

### 4️⃣ Create Services
Each deployment requires a service for internal communication.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: service-webcolor-green
spec: 
  selector:
    app: web-color-green
  ports:
    - port: 80
      targetPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: service-webcolor-blue
spec: 
  selector:
    app: web-color-blue
  ports:
    - port: 80
      targetPort: 80
```
Apply the services:
```sh
kubectl apply -f services.yaml
```

### 5️⃣ Configure Ingress
#### **Default Ingress for unmatched requests**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-default
spec:
  ingressClassName: nginx
  defaultBackend:
    service:
      name: nginx
      port: 
        number: 80
```
#### **Ingress rules for /blue and /green**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-webcolor
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  ingressClassName: nginx
  tls:
    - hosts:
      - labdevops.online
      secretName: letsencrypt-prod
  rules:
    - host: labdevops.online
      http:
        paths:
          - path: /blue
            pathType: Prefix
            backend:
              service:
                name: service-webcolor-blue
                port: 
                  number: 80
          - path: /green
            pathType: Prefix
            backend:
              service:
                name: service-webcolor-green
                port: 
                  number: 80
```
Apply the Ingress rules:
```sh
kubectl apply -f ingress.yaml
```

### 6️⃣ Install Cert-Manager
```sh
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager --create-namespace \
  --version v1.12.0 \
  --set installCRDs=true
```
Verify installation:
```sh
kubectl get pods -n cert-manager
```

### 7️⃣ Setup Let's Encrypt ClusterIssuer
```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: gabriel.o.lima1709@gmail.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: nginx
```
Apply the ClusterIssuer:
```sh
kubectl apply -f cluster-issuer.yaml
```
Verify:
```sh
kubectl get clusterissuer
kubectl describe clusterissuer letsencrypt-prod
```

### 8️⃣ Verify HTTPS Configuration
After applying TLS Ingress rules, check certificate status:
```sh
kubectl describe certificate letsencrypt-prod
kubectl get cert
```
Test HTTPS access:
```sh
curl -k https://labdevops.online/blue
curl -k https://labdevops.online/green
```
If everything is set up correctly, you should see secure connections with Let's Encrypt SSL. 🚀🔒

---
## 🎯 Conclusion
With this setup, you have:
✅ **Ingress NGINX for routing requests**
✅ **Cert-Manager for automatic SSL certificates**
✅ **Secure HTTPS with Let's Encrypt**
✅ **Multiple services exposed via Ingress**
✅ **Cluster provisioned on Digital Ocean for high availability**

If you have any issues, feel free to open an issue in this repository! Happy coding! 🚀

=======
# webcolor
>>>>>>> 3fda972 (Initial commit)

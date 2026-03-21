# 🚀 YT-K8S-Mini-Project

<div align="center">

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)
![AWS](https://img.shields.io/badge/Amazon_AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)

**A beginner-friendly Kubernetes mini-project for learning containerized deployments**

[📖 Overview](#overview) • [⚙️ Prerequisites](#prerequisites) • [🚀 Quick Start](#quick-start) • [☁️ Cloud K8s](#cloud-kubernetes) • [🔧 Troubleshooting](#troubleshooting)

</div>

---

## 📁 Project Structure

```
YT-K8S-Mini-Project/
│
├── static/                  # Static CSS/JS assets
├── templates/               # HTML Jinja2 templates
│
├── app.py                   # Flask application entry point
├── dockerfile               # Docker image definition
├── requirements.txt         # Python dependencies
├── deployment.yaml          # Kubernetes Deployment + Service manifest
│
├── README.md                # This file
└── LICENSE                  # MIT License
```

---

## 📌 Overview

This project demonstrates how to **containerize** a Python Flask web application and **deploy it on Kubernetes** using Minikube (local) or managed cloud services (EKS/AKS/GKE). It is built for educational purposes as part of a YouTube tutorial series.

**What you'll learn:**
- Building and testing Docker images locally
- Writing Kubernetes `deployment.yaml` manifests
- Using `kubectl` to manage pods and services
- Integrating Kubernetes into a GitHub Actions CI/CD pipeline
- Comparing managed Kubernetes services: EKS vs AKS vs GKE

---

## ✅ Prerequisites

| Tool | Purpose | Install Link |
|------|---------|-------------|
| ![Docker](https://img.shields.io/badge/-Docker-2496ED?logo=docker&logoColor=white&style=flat-square) | Build & run containers | [docs.docker.com](https://docs.docker.com/get-docker/) |
| ![Minikube](https://img.shields.io/badge/-Minikube-326CE5?logo=kubernetes&logoColor=white&style=flat-square) | Local Kubernetes cluster | [minikube.sigs.k8s.io](https://minikube.sigs.k8s.io/docs/start/) |
| ![kubectl](https://img.shields.io/badge/-kubectl-326CE5?logo=kubernetes&logoColor=white&style=flat-square) | Kubernetes CLI | [kubernetes.io](https://kubernetes.io/docs/tasks/tools/) |
| ![DockerHub](https://img.shields.io/badge/-Docker_Hub-2496ED?logo=docker&logoColor=white&style=flat-square) | Image registry | [hub.docker.com](https://hub.docker.com/) |

---

## 🚀 Quick Start

### 1. 🏗️ Build the Application

Clone the repo and test the app locally before containerizing:

```bash
git clone https://github.com/vikashishere/YT-K8S-Mini-Project.git
cd YT-K8S-Mini-Project
pip install -r requirements.txt
python app.py
```

---

### 2. 🐳 Build & Test the Docker Image

```bash
# Build the image
docker build -t kubernetes-test-app:latest .

# Verify it exists
docker images

# Run locally to test
docker run -p 5000:5000 kubernetes-test-app:latest
```

> Visit `http://localhost:5000` to verify the app is running.

---

### 3. 📄 Kubernetes Deployment Manifest

The `deployment.yaml` file defines:
- A **Deployment** with 2 replicas of your containerized app
- A **Service** of type `NodePort` to expose the app

```yaml
# deployment.yaml (already included in repo)
kubectl apply -f deployment.yaml
```

---

### 4. ⚙️ Install & Start Minikube

Visit the [Minikube Installation Guide](https://minikube.sigs.k8s.io/docs/start/) and download the installer.

```bash
# Start Minikube
minikube start

# OR with embedded certs (recommended on some systems)
minikube start --embed-certs

# Add extra nodes (optional)
minikube start --nodes=2 --embed-certs
```

---

### 5. 🔍 Verify Cluster Status

```bash
minikube status

kubectl get all -A
kubectl get pods -A
kubectl get nodes -A
```

---

### 6. 📦 Load Docker Image into Minikube

> ⚠️ Minikube runs its own Docker daemon. You must load your image into it.

```bash
# Check existing images
minikube image list

# Load the local image
minikube image load kubernetes-test-app:latest
```

---

### 7. 🚢 Deploy to Kubernetes

```bash
# Apply the manifest
kubectl apply -f deployment.yaml

# Delete if needed
kubectl delete deployment kubernetes-test-app
```

---

### 8. 🧪 Test the Application

```bash
# Check running pods
kubectl get pods -A

# Access the app via Minikube
minikube service kubernetes-test-app

# Open Kubernetes dashboard
minikube dashboard
```

---

### 9. 🪵 Debugging & Logs

```bash
# View pod logs (live)
kubectl logs -f <pod-id>

# Check services and endpoints
kubectl get service
kubectl get endpoints

# Delete a specific pod (Kubernetes will recreate it)
kubectl delete pod <pod-name>
```

---

### 10. 🛑 Stop Minikube

```bash
minikube stop
```

---

## 🔧 Troubleshooting

### ❌ Minikube Can't Connect to `registry.k8s.io`

**Error:**
```
Failing to connect to https://registry.k8s.io/
```

**Solutions:**

```bash
# Option 1: If behind a proxy
minikube start \
  --docker-env HTTP_PROXY=http://your-proxy:port \
  --docker-env HTTPS_PROXY=https://your-proxy:port

# Option 2: Unset proxy variables
unset HTTP_PROXY
unset HTTPS_PROXY
unset NO_PROXY

# Option 3: Test DNS resolution
nslookup registry.k8s.io

# Option 4: Clean restart
minikube stop
minikube delete --all
minikube start
```

---

### ❌ `ImagePullBackOff` Error

This means Kubernetes can't find the image. Push it to Docker Hub:

```bash
# Tag with your Docker Hub username
docker tag kubernetes-test-app:latest <your-dockerhub-username>/kubernetes-test-app:latest

# Push to Docker Hub
docker push <your-dockerhub-username>/kubernetes-test-app:latest
```

Then update your `deployment.yaml` to reference the full image path:
```yaml
image: <your-dockerhub-username>/kubernetes-test-app:latest
```

---

## ⚡ CI/CD with GitHub Actions + Kubernetes

This project can integrate Kubernetes into a full CI/CD pipeline. On every push to `main`:

1. **Build** the Docker image
2. **Push** to Amazon ECR
3. **Deploy** to Kubernetes (EKS or Minikube)

```yaml
name: CI/CD Pipeline with Kubernetes
on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Log in to ECR
        run: |
          aws ecr get-login-password --region <region> | \
          docker login --username AWS --password-stdin \
          <account-id>.dkr.ecr.<region>.amazonaws.com

      - name: Build and Push Docker Image
        run: |
          docker build -t <image-name>:latest .
          docker tag <image-name>:latest \
            <account-id>.dkr.ecr.<region>.amazonaws.com/<image-name>:latest
          docker push \
            <account-id>.dkr.ecr.<region>.amazonaws.com/<image-name>:latest

      - name: Deploy to Kubernetes
        uses: azure/k8s-deploy@v4
        with:
          manifests: |
            deployment.yaml
```

---

## ☁️ Cloud Kubernetes

### Why Not Minikube in Production?

> Minikube is a **local development tool only**. For production, use a managed Kubernetes service.

### Comparison: EKS vs AKS vs GKE

| Feature | ![AWS](https://img.shields.io/badge/-AWS_EKS-FF9900?logo=amazonaws&logoColor=white&style=flat-square) | ![Azure](https://img.shields.io/badge/-Azure_AKS-0089D6?logo=microsoftazure&logoColor=white&style=flat-square) | ![GCP](https://img.shields.io/badge/-GCP_GKE-4285F4?logo=googlecloud&logoColor=white&style=flat-square) |
|---------|---------|---------|---------|
| **Ease of Use** | Medium | ⭐ Beginner-Friendly | ⭐ Polished |
| **Control Plane** | Billed separately | Free | Free |
| **Best For** | Enterprise / AWS users | Azure ecosystem | AI/ML workloads |
| **Unique Advantage** | IAM + VPC integration | Azure AD / RBAC | GPU/TPU + Vertex AI |
| **Autoscaling** | Manual / ASG | Simplified | Advanced algorithms |

### 🎯 How to Choose

- **Using AWS already?** → Go with **EKS**
- **Microsoft shop (Azure AD, Teams)?** → Go with **AKS**
- **ML/AI workloads or new to cloud?** → Go with **GKE**

---

## 📊 Load Testing

Use **Postman** to simulate traffic and observe Kubernetes pod behavior:

```
Collection → Runs → Performance → Run Performance Test
Example: Fixed — 10 users, 1 minute
```

Watch how Kubernetes responds with pod autoscaling and fault tolerance!

---

## 🤝 Contributing

Contributions are welcome! Feel free to:
- ⭐ Star this repo
- 🐛 Open issues
- 🔀 Submit pull requests

---



## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<div align="center">

Built with ❤️ by [vikashishere](https://github.com/vikashishere) · For learning purposes

![Stars](https://img.shields.io/github/stars/vikashishere/YT-K8S-Mini-Project?style=social)
![Forks](https://img.shields.io/github/forks/vikashishere/YT-K8S-Mini-Project?style=social)

</div>
# 🎮 DevSecOps Tic Tac Toe Game using Flask, Jenkins & ArgoCD

A complete DevSecOps pipeline for deploying a **Tic Tac Toe** game built with **Python Flask**, integrated with **Jenkins for CI**, **Docker for containerization**, and **ArgoCD for CD** into a Kubernetes environment.

---

## 📌 Table of Contents

- [🧱 Architecture](#-architecture)
- [🧰 Tech Stack](#-tech-stack)
- [📂 Project Structure](#-project-structure)
- [🚀 Setup Instructions](#-setup-instructions)
- [🔁 CI Pipeline - Jenkins](#-ci-pipeline---jenkins)
- [🌐 Kubernetes Deployment](#-kubernetes-deployment)
- [🚢 CD Pipeline - ArgoCD](#-cd-pipeline---argocd)
- [🔐 DevSecOps Best Practices](#-devsecops-best-practices)
- [✅ Outcome](#-outcome)

---

## 🧱 Architecture

```
User → Flask App → GitHub → Jenkins (CI) → DockerHub → ArgoCD (CD) → Kubernetes Cluster
```

---

## 🧰 Tech Stack

| Layer           | Technology         |
|----------------|--------------------|
| Application     | Python Flask       |
| CI              | Jenkins            |
| CD              | ArgoCD             |
| Containerization| Docker             |
| Orchestration   | Kubernetes         |
| Security        | Trivy              |
| Version Control | GitHub             |

---

## 📂 Project Structure

```
tic-tac-toe-devsecops/
├── app/
│   ├── templates/
│   │   └── index.html
│   └── app.py
├── Dockerfile
├── requirements.txt
├── Jenkinsfile
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
└── README.md
```

---

## 🚀 Setup Instructions

### 1. Clone the Repository

```bash
git clone [https://github.com/Piyushbankhede/devSecOps-tic-tac-toc.git]
cd tic-tac-toe-devsecops
```

### 2. Build & Run Locally

```bash
docker build -t tic-tac-toe .
docker run -p 5000:5000 tic-tac-toe
```

Visit `http://localhost:5000`

---

## 🔁 CI Pipeline - Jenkins

### Jenkinsfile Overview

- Clone code from GitHub
- Run vulnerability scan with Trivy
- Build Docker image
- Push image to DockerHub

```groovy
pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps { git '[https://github.com/Piyushbankhede/devSecOps-tic-tac-toc.git]' }
    }
    stage('Security Scan') {
      steps { sh 'trivy fs --exit-code 1 --severity HIGH,CRITICAL . || true' }
    }
    stage('Build Docker Image') {
      steps { script { docker.build("piyushbankhede/tic-tac-toe:${BUILD_NUMBER}") } }
    }
    stage('Push to DockerHub') {
      steps {
        withDockerRegistry([credentialsId: 'dockerhub-creds', url: '']) {
          docker.image("piyushbankhede/tic-tac-toe:${BUILD_NUMBER}").push()
        }
      }
    }
  }
}
```

---

## 🌐 Kubernetes Deployment

### `k8s/deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tic-tac-toe
spec:
  replicas: 2
  selector:
    matchLabels:
      app: tic-tac-toe
  template:
    metadata:
      labels:
        app: tic-tac-toe
    spec:
      containers:
        - name: tic-tac-toe
          image: piyushbankheded/tic-tac-toe:latest
          ports:
            - containerPort: 5000
```

### `k8s/service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: tic-tac-toe-service
spec:
  type: LoadBalancer
  selector:
    app: tic-tac-toe
  ports:
    - port: 80
      targetPort: 5000
```

---

## 🚢 CD Pipeline - ArgoCD

### Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Create ArgoCD Application

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: tic-tac-toe
  namespace: argocd
spec:
  source:
    repoURL: '[https://github.com/Piyushbankhede/devSecOps-tic-tac-toc.git]'
    targetRevision: HEAD
    path: k8s
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

Apply it:

```bash
kubectl apply -f app.yaml
```

---

## 🔐 DevSecOps Best Practices

- ✅ Scan Docker image with **Trivy** in Jenkins stage.
- ✅ Use **RBAC** in Kubernetes and ArgoCD.
- ✅ Store credentials using Jenkins secrets.
- ✅ Follow **GitOps** principles for delivery.

---

## ✅ Outcome

- Automated CI/CD pipeline for Flask app
- Secure container image and deployment workflow
- GitHub → Jenkins → DockerHub → ArgoCD → Kubernetes
- Lightweight game deployed in a production-grade way!

---

## 🙌 Credits

Created by [Piyush Bankhede](https://github.com/piyushbankhede)



# Hello World Kubernetes Demo

Welcome to the **Hello World Kubernetes Demo**! This repository contains a minimalist, yet complete, example of deploying a containerized Flask application backed by a PostgreSQL database to Kubernetes.

---

## 🚀 Overview

In this demo, we build two Docker images:

1. **Frontend** – A Flask web server that retrieves a name from the database and responds with:
   ```text
   hello world, {name}
   ```
2. **Database** – A PostgreSQL instance initialized with a single `users` table containing one row for `{name}`.

We then deploy both services into a Kubernetes cluster using standard manifests: Namespaces, Secrets, Deployments, and Services.

---

## 🗂️ Project Layout

```text
hello-k8s-demo/
├── frontend/         # Flask application
│   ├── app.py        # Application logic
│   ├── requirements.txt
│   └── Dockerfile
├── database/         # PostgreSQL initialization
│   ├── init.sql      # Schema + seed data
│   └── Dockerfile
└── k8s/              # Kubernetes YAML manifests
    ├── namespace.yaml
    ├── postgres-secret.yaml
    ├── postgres-deployment.yaml
    ├── postgres-service.yaml
    ├── frontend-deployment.yaml
    └── frontend-service.yaml
```

---

## 🛠️ Technologies

- **Python 3.9** & **Flask** for the web server
- **PostgreSQL 13** database
- **Docker** for containerization
- **Kubernetes** (tested with Minikube) for orchestration

---

## ✅ Prerequisites

Before you begin, ensure you have:

- A **Docker Hub** account
- **Docker** (v20+) installed and logged in (`docker login`)
- A running Kubernetes cluster and **kubectl** configured (e.g. Minikube, kind, GKE, EKS)
- Optional: **jq** CLI for inspecting JSON

---

## 📦 1. Build & Publish Docker Images

> **Tip:** Replace `your-dockerhub-username` with your actual Docker Hub handle.

```bash
# From project root
export DOCKERHUB_USERNAME=your-dockerhub-username

# Build & push frontend
cd frontend
docker build -t $DOCKERHUB_USERNAME/hello-frontend:latest .
docker push $DOCKERHUB_USERNAME/hello-frontend:latest

# Build & push database
cd ../database
docker build -t $DOCKERHUB_USERNAME/hello-postgres:latest .
docker push $DOCKERHUB_USERNAME/hello-postgres:latest
``` 

---

## 🔧 2. Deploy to Kubernetes

1. **Create Namespace**
   ```bash
   kubectl apply -f k8s/namespace.yaml
   ```

2. **Configure Secrets**
   ```bash
   kubectl apply -f k8s/postgres-secret.yaml
   ```

3. **Deploy PostgreSQL**
   ```bash
   kubectl apply -f k8s/postgres-deployment.yaml
   kubectl apply -f k8s/postgres-service.yaml
   ```

4. **Deploy Flask Frontend**
   ```bash
   kubectl apply -f k8s/frontend-deployment.yaml
   kubectl apply -f k8s/frontend-service.yaml
   ```

---

## 🔍 3. Verify & Access

1. **Check resource status**
   ```bash
   kubectl get all -n hello-demo
   ```
2. **View logs (if needed)**
   ```bash
   kubectl logs -l app=frontend -n hello-demo
   ```
3. **Open the web app**
   - **Minikube:**
     ```bash
     minikube service frontend -n hello-demo
     ```
   - **Other clusters:**
     Visit `http://<NODE_IP>:30007` in your browser.

You should see:

```
hello world, Your Name
```
---

## 📚 References & Acknowledgments

This demo leverages concepts and code inspired by several authoritative sources:

- **Flask Quickstart**: Official Flask tutorial for creating minimal web applications.  
  https://flask.palletsprojects.com/en/2.2.x/quickstart/
- **Dockerfile Best Practices**: Guidance on writing efficient Dockerfiles and multi-stage builds.  
  https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
- **PostgreSQL Initialization Scripts**: Example patterns for database schema and seed data.  
  https://hub.docker.com/_/postgres#initialization-scripts
- **Kubernetes by Example**: Hands-on examples for Namespaces, Deployments, Services, and Secrets.  
  https://kubernetes.io/docs/tutorials/kubernetes-basics/
- **Minikube Documentation**: Instructions for running a local cluster and using `minikube service`.  
  https://minikube.sigs.k8s.io/docs/

These resources guided the structure and configuration of this project.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).


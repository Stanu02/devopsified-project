# DevOpsified Project 🚀

A complete end-to-end DevOps implementation on a Python web application.

## What This Project Does

Every time code is pushed to GitHub, it automatically:
1. Builds a Docker image
2. Pushes it to Docker Hub
3. Updates the Helm chart with the new image tag
4. Deploys to Kubernetes — all without any manual steps

---

## Tech Stack

| Layer | Tool |
|-------|------|
| Code Repo | GitHub |
| CI Pipeline | GitHub Actions |
| Containerization | Docker (Multi-stage build) |
| Container Registry | Docker Hub |
| Package Manager | Helm |
| Kubernetes Cluster | Minikube (local) |
| GitOps Engine | ArgoCD |
| Image Automation | ArgoCD Image Updater |
| Ingress | NGINX Ingress Controller |

---

## Architecture

```
Developer pushes code to GitHub
         ↓
GitHub Actions CI Pipeline runs
         ↓
Docker image built & pushed to Docker Hub
         ↓
ArgoCD Image Updater detects new image
         ↓
Image Updater commits new tag to Git (Helm chart)
         ↓
ArgoCD detects Git change
         ↓
Kubernetes automatically redeploys the app
```

---

## Project Structure

```
devopsified-project/
├── .github/
│   └── workflows/
│       └── docker-ci.yml       # GitHub Actions CI pipeline
├── devopsified-chart/          # Helm chart
│   ├── Chart.yaml
│   ├── values.yaml             # Image tag managed by Image Updater
│   └── templates/
├── Dockerfile                  # Multi-stage Docker build
├── app.py                      # Python web application
├── app.yaml                    # ArgoCD Application manifest
├── image-updater.yaml          # ArgoCD Image Updater config
└── requirements.txt
```

---

## CI Pipeline (GitHub Actions)

On every push to `main`:
- Checks out code
- Logs into Docker Hub
- Builds Docker image tagged with `github.sha`
- Pushes image to Docker Hub

---

## CD Pipeline (ArgoCD + Image Updater)

- ArgoCD watches the `devopsified-chart/` Helm chart in Git
- Image Updater polls Docker Hub every 2 minutes for new images
- When a new image is found, Image Updater automatically commits the new tag to Git
- ArgoCD detects the Git change and redeploys to Kubernetes

---

## Key Concepts Implemented

- **Multi-stage Docker build** — smaller, more secure images using distroless base
- **GitOps** — Git is the single source of truth for the cluster state
- **Continuous Integration** — automated build and push on every commit
- **Continuous Deployment** — automated deploy without manual intervention
- **Helm** — Kubernetes package management for multiple environments
- **Ingress** — NGINX ingress controller to expose the app via a domain
- **DNS Mapping** — local domain `devopsified.local` mapped to the cluster

---

## How to Run Locally

### Prerequisites
- Docker
- Minikube
- kubectl
- Helm
- ArgoCD CLI

### Steps

**1. Start Minikube**
```bash
minikube start
```

**2. Install ArgoCD**
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**3. Install ArgoCD Image Updater**
```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm install argocd-image-updater argo/argocd-image-updater -n argocd
```

**4. Apply ArgoCD Application**
```bash
kubectl apply -f app.yaml
```

**5. Enable Ingress**
```bash
minikube addons enable ingress
```

**6. Add DNS mapping**
```bash
echo "$(minikube ip) devopsified.local" | sudo tee -a /etc/hosts
```

**7. Access the app**
```bash
minikube service devopsified-app-devopsified-chart --url
```

---

## GitHub Secrets Required

| Secret | Description |
|--------|-------------|
| `DOCKERHUB_USERNAME` | Your Docker Hub username |
| `DOCKERHUB_TOKEN` | Your Docker Hub access token |

---

## Acknowledgements

Project inspired by [Abhishek Veeramalla's DevOpsified tutorial](https://youtu.be/HGu9sgoHaJ0).

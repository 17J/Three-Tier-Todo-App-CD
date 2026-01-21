# 🔄 GitOps Three-Tier Todo Application - CD Pipeline

<div align="center">

![GitOps](https://img.shields.io/badge/GitOps-ArgoCD-purple?style=for-the-badge)
![Kubernetes](https://img.shields.io/badge/K8s-AWS%20EKS-orange?style=for-the-badge)
![Auto Deploy](https://img.shields.io/badge/Auto%20Deploy-Enabled-green?style=for-the-badge)
![Image Updater](https://img.shields.io/badge/Image%20Updater-Active-blue?style=for-the-badge)

**Continuous Deployment with ArgoCD & Automatic Image Updates**

[Overview](#-overview) • [Architecture](#%EF%B8%8F-architecture) • [Setup](#-setup-guide) • [Usage](#-usage) • [Troubleshooting](#-troubleshooting)

</div>

---

## 🤝 Contributing

Contributions are welcome! To contribute:

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -m 'Add improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👨‍💻 Author

<div align="center">

**Rahul Joshi**

[![Email](https://img.shields.io/badge/Email-17rahuljoshi%40gmail.com-red?style=for-the-badge&logo=gmail)](mailto:17rahuljoshi@gmail.com)
[![GitHub](https://img.shields.io/badge/GitHub-17J-black?style=for-the-badge&logo=github)](https://github.com/17J)

</div>

---

## ⭐ Show Your Support

Give a ⭐ if this project helped you implement GitOps!

<div align="center">

**⭐ Star this repo | 🍴 Fork it | 📢 Share it**

</div>

---

## 🔄 Version History

### [1.0.0] - 2026-01-21

#### Added

- Initial CD pipeline with ArgoCD
- Automated image updates
- Kubernetes manifests for three-tier app
- ALB Ingress configuration
- Comprehensive documentation

#### Features

- GitOps workflow with ArgoCD
- Automatic deployment on image push
- Self-healing and auto-sync
- Rollback support

---

<div align="center">

### 🎯 Project Status: Production Ready ✅

**Built with ❤️ for GitOps and Cloud-Native Community**

---

[![GitOps](https://img.shields.io/badge/GitOps-Enabled-purple?style=for-the-badge)]()
[![ArgoCD](https://img.shields.io/badge/ArgoCD-Active-green?style=for-the-badge)]()
[![Kubernetes](https://img.shields.io/badge/Kubernetes-EKS-orange?style=for-the-badge)]()

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Architecture](#%EF%B8%8F-architecture)
- [Repository Structure](#-repository-structure)
- [Prerequisites](#-prerequisites)
- [Setup Guide](#-setup-guide)
- [ArgoCD Configuration](#-argocd-configuration)
- [How It Works](#-how-it-works)
- [Deployment](#-deployment)
- [Monitoring](#-monitoring)
- [Maintenance](#-maintenance)

---

## 🎯 Overview

This repository contains **Kubernetes manifests** for the Three-Tier Todo Application deployed using **GitOps principles** with ArgoCD. The CD pipeline automatically detects new Docker images pushed by the CI pipeline and updates the deployment without manual intervention.

### Key Features

✅ **GitOps Workflow** - Infrastructure and application configuration as code  
✅ **Automated Deployments** - ArgoCD Image Updater pulls latest images automatically  
✅ **Declarative Configuration** - Kubernetes manifests version-controlled in Git  
✅ **Self-Healing** - ArgoCD automatically corrects drift from desired state  
✅ **Rollback Support** - Easy rollback to any previous Git commit  
✅ **Multi-Environment Ready** - Kustomize overlays for dev/staging/prod

---

## 🏗️ Architecture

### GitOps Flow

```
┌─────────────┐       ┌─────────────┐       ┌─────────────┐       ┌─────────────┐
│   Jenkins   │──────▶│ Docker Hub  │       │   ArgoCD    │       │  K8s Cluster│
│  CI Pipeline│ Push  │  Registry   │       │Image Updater│       │    (EKS)    │
└─────────────┘       └──────┬──────┘       └──────┬──────┘       └──────┬──────┘
                             │                     │                     │
                             │   ┌─────────────────▼─────────────────┐   │
                             │   │  Detects new image every 2 min    │   │
                             │   │  Updates Git repo with new tag    │   │
                             │   └─────────────────┬─────────────────┘   │
                             │                     │                     │
                      ┌──────▼─────────┐    ┌─────▼──────┐       ┌──────▼──────┐
                      │  CD Git Repo   │◀───│  Git Write │       │   Syncs     │
                      │ (This Repo)    │    │   Back     │       │Automatically │
                      └────────────────┘    └────────────┘       └─────────────┘
```

### Deployment Architecture

```
┌──────────────────────────────────────────────────────────┐
│                    AWS Cloud (ap-south-1)                │
│  ┌────────────────────────────────────────────────────┐  │
│  │              EKS Cluster (expdevops-cluster)       │  │
│  │                                                     │  │
│  │  ┌──────────────────────────────────────────────┐  │  │
│  │  │          Namespace: threetierapp             │  │  │
│  │  │                                               │  │  │
│  │  │  ┌────────────┐  ┌────────────┐             │  │  │
│  │  │  │  Frontend  │  │  Backend   │             │  │  │
│  │  │  │  Pods (3)  │  │  Pods (3)  │             │  │  │
│  │  │  └─────┬──────┘  └─────┬──────┘             │  │  │
│  │  │        │               │                     │  │  │
│  │  │  ┌─────▼──────┐  ┌─────▼──────┐             │  │  │
│  │  │  │  Frontend  │  │  Backend   │             │  │  │
│  │  │  │  Service   │  │  Service   │             │  │  │
│  │  │  │(ClusterIP) │  │(ClusterIP) │             │  │  │
│  │  │  └─────┬──────┘  └────────────┘             │  │  │
│  │  │        │                                     │  │  │
│  │  │  ┌─────▼──────────────────────┐             │  │  │
│  │  │  │    MySQL StatefulSet       │             │  │  │
│  │  │  │      mysql-service         │             │  │  │
│  │  │  └────────────────────────────┘             │  │  │
│  │  └──────────────────┬────────────────────────┬─┘  │  │
│  │                     │                        │    │  │
│  │  ┌──────────────────▼────────┐   ┌───────────▼──┐ │  │
│  │  │   ALB Ingress Controller  │   │    ArgoCD    │ │  │
│  │  │  (todo-app-ingress)       │   │  Namespace   │ │  │
│  │  └───────────┬───────────────┘   └──────────────┘ │  │
│  └──────────────┼─────────────────────────────────────┘  │
│                 │                                         │
│      ┌──────────▼──────────┐                             │
│      │  Application Load   │                             │
│      │     Balancer        │                             │
│      └──────────┬──────────┘                             │
└─────────────────┼────────────────────────────────────────┘
                  │
           ┌──────▼──────┐
           │   Internet  │
           │    Users    │
           └─────────────┘
```

---

## 📁 Repository Structure

```
Three-Tier-Todo-App-CD/K8s/
│
├── README.md                          # This file
├── kustomization.yaml                 # Kustomize base configuration
│
├── frontend-ds.yml           # Frontend Deployment manifest and Service
│
├── backend-ds.yml            # Backend Deployment manifest & # Backend Service (ClusterIP)
│
├── mysql-ds.yaml              # MySQL
│
├── secrets-configmap.yml             # Secrets and ConfigMaps
├── abb-ingress.yaml                       # ALB Ingress configuration
│
│
└── argocd-source.yml              # Auto-generated by Image Updater
                                       # (Don't manually edit)
```

---

## 📋 Prerequisites

### 1. Kubernetes Cluster

```yaml
Required:
  - EKS Cluster running (e.g., expdevops-cluster)
  - kubectl configured with cluster access
  - Cluster version: 1.28+
  - Worker nodes: 2-3 minimum (t3.medium or higher)
```

**Verify:**

```bash
kubectl get nodes
kubectl cluster-info
```

### 2. ArgoCD Installed

```yaml
Required:
  - ArgoCD installed in argocd namespace
  - ArgoCD server accessible
  - Admin credentials configured
```

**Verify:**

```bash
kubectl get pods -n argocd
kubectl get svc -n argocd
```

### 3. ALB Ingress Controller

```yaml
Required:
  - AWS Load Balancer Controller installed
  - IAM OIDC provider configured
  - Service account with proper IAM role
```

**Verify:**

```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```

### 4. ArgoCD Image Updater

```yaml
Required:
  - ArgoCD Image Updater installed
  - Git credentials secret created
  - Access to Docker Hub registry
```

**Verify:**

```bash
kubectl get deployment -n argocd argocd-image-updater
kubectl get secret git-token-secret -n argocd
```

### 5. Docker Hub Repository

```yaml
Required:
  - Docker Hub account
  - Repositories created:
    * 17rj/three-tier-todo-frontend
    * 17rj/three-tier-todo-backend
  - Images tagged with build numbers
```

---

## 🚀 Setup Guide

### Step 1: Clone Repository

```bash
# Clone CD repository
git clone https://github.com/17J/Three-Tier-Todo-App-CD.git
cd Three-Tier-Todo-App-CD
```

### Step 2: Create Namespace

```bash
# Create application namespace
kubectl create namespace threetierapp

# Verify
kubectl get namespace threetierapp
```

### Step 3: Create Secrets

```bash
# Create MySQL secret
kubectl create secret generic mysql-secret \
  --from-literal=mysql-root-password=rootpassword \
  --from-literal=mysql-database=tododb \
  --from-literal=mysql-user=todouser \
  --from-literal=mysql-password=todopassword \
  -n threetierapp

# Verify
kubectl get secret mysql-secret -n threetierapp
```

### Step 4: Install ArgoCD (If Not Already Installed)

```bash
# Create ArgoCD namespace
kubectl create namespace argocd

# Install ArgoCD
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Wait for pods to be ready
kubectl wait --for=condition=Ready pods --all -n argocd --timeout=300s

# Get admin password
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d && echo

# Access ArgoCD UI (Optional)
kubectl port-forward svc/argocd-server -n argocd 8080:443
# Open: https://localhost:8080
# Username: admin
# Password: (from above command)
```

### Step 5: Install ArgoCD Image Updater

```bash
# Install Image Updater
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj-labs/argocd-image-updater/stable/manifests/install.yaml

# Verify installation
kubectl get deployment argocd-image-updater -n argocd

# Check logs
kubectl logs -n argocd -l app.kubernetes.io/name=argocd-image-updater
```

### Step 6: Create Git Credentials Secret

```bash
# Generate GitHub Personal Access Token (PAT)
# Go to: GitHub → Settings → Developer settings → Personal access tokens
# Create token with 'repo' scope

# Create secret
kubectl create secret generic git-token-secret \
  --from-literal=username=17J \
  --from-literal=password=<YOUR_GITHUB_PAT_TOKEN> \
  -n argocd

# Verify
kubectl describe secret git-token-secret -n argocd
```

### Step 7: Create Kustomization File

```bash
# If not already exists, create kustomization.yaml
cat <<EOF > kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
  - frontend-ds-service.yml
  - backend-ds-service.yml
  - db-ds-service.yaml
  - alb-ingress.yml
  - secrets-configmap.yml

EOF

# Commit to Git
git add kustomization.yaml
git commit -m "Add Kustomize configuration"
git push origin main
```

### Step 8: Create ArgoCD Application

```bash
# Create ArgoCD application manifest
cat <<EOF > argocd/application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: gitops-three-tier-cd
  namespace: argocd
  annotations:
    # Image list for auto-update
    argocd-image-updater.argoproj.io/image-list: myfrontend=17rj/three-tier-todo-frontend,mybackend=17rj/three-tier-todo-backend

    # Update strategy
    argocd-image-updater.argoproj.io/myfrontend.update-strategy: latest
    argocd-image-updater.argoproj.io/mybackend.update-strategy: latest

    # Git write-back configuration
    argocd-image-updater.argoproj.io/write-back-method: git:secret:argocd/git-token-secret
    argocd-image-updater.argoproj.io/git-branch: main
spec:
  project: default
  source:
    repoURL: https://github.com/17J/Three-Tier-Todo-App-CD.git
    targetRevision: main
    path: .
    kustomize: {}
  destination:
    server: https://kubernetes.default.svc
    namespace: threetierapp
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
EOF

# Apply ArgoCD application
kubectl apply -f argocd/application.yaml

# Verify
kubectl get application -n argocd gitops-three-tier-cd
```

---

## ⚙️ ArgoCD Configuration

### Application Annotations Explained

```yaml
annotations:
  # Defines which images to monitor
  # Format: <alias>=<registry>/<image-name>
  argocd-image-updater.argoproj.io/image-list: |
    myfrontend=17rj/three-tier-todo-frontend,
    mybackend=17rj/three-tier-todo-backend

  # Update strategy: latest (always use newest tag)
  # Alternatives: semver, digest
  argocd-image-updater.argoproj.io/myfrontend.update-strategy: latest
  argocd-image-updater.argoproj.io/mybackend.update-strategy: latest

  # Write-back method: Update Git repository
  # Format: git:secret:<namespace>/<secret-name>
  argocd-image-updater.argoproj.io/write-back-method: git:secret:argocd/git-token-secret

  # Git branch to update
  argocd-image-updater.argoproj.io/git-branch: main
```

### Sync Policy Options

```yaml
syncPolicy:
  automated:
    prune: true # Delete resources not in Git
    selfHeal: true # Auto-correct manual changes
  syncOptions:
    - CreateNamespace=true # Auto-create namespace
    - PruneLast=true # Delete resources last
```

---

## 🔄 How It Works

### Complete Workflow

```
1. Developer commits code to CI repository
   └─→ GitHub (Three-Tier-Todo-App-CI)

2. Jenkins CI pipeline triggers
   ├─→ Runs security scans
   ├─→ Builds Docker images
   ├─→ Tags with build number (e.g., :42)
   └─→ Pushes to Docker Hub

3. ArgoCD Image Updater polls Docker Hub (every 2 minutes)
   ├─→ Detects new image: 17rj/three-tier-todo-frontend:42
   └─→ Compares with current deployment

4. Image Updater updates CD repository
   ├─→ Modifies .argocd-source-gitops-three-tier-cd.yaml
   ├─→ Updates image tags in deployments
   └─→ Commits to Git with message: "build: automatic update..."

5. ArgoCD detects Git changes
   ├─→ Syncs new manifests to cluster
   ├─→ Performs rolling update
   └─→ Monitors health status

6. Application running with new version
   └─→ Zero-downtime deployment complete
```

### Image Update Process

```bash
# Before Update (Current State)
frontend-deployment.yaml:
  image: 17rj/three-tier-todo-frontend:41

# After Update (New State)
.argocd-source-gitops-three-tier-cd.yaml:
  images:
  - 17rj/three-tier-todo-frontend:42
  - 17rj/three-tier-todo-backend:42

frontend-deployment.yaml:
  image: 17rj/three-tier-todo-frontend:42
```

---

## 🚢 Deployment

### Manual Deployment (Without ArgoCD)

```bash
# Apply all manifests
kubectl apply -k .

# Or apply individually
kubectl apply -f secrets-configmap.yaml -n threetierapp
kubectl apply -f mysql-deployment.yaml -n threetierapp
kubectl apply -f mysql-service.yaml -n threetierapp
kubectl apply -f backend-deployment.yaml -n threetierapp
kubectl apply -f backend-service.yaml -n threetierapp
kubectl apply -f frontend-deployment.yaml -n threetierapp
kubectl apply -f frontend-service.yaml -n threetierapp
kubectl apply -f ingress.yaml -n threetierapp

# Verify deployment
kubectl get all -n threetierapp
```

### GitOps Deployment (With ArgoCD)

```bash
# ArgoCD will automatically deploy
# Just monitor the sync status

# Via kubectl
kubectl get application gitops-three-tier-cd -n argocd -w

# Via ArgoCD CLI
argocd app get gitops-three-tier-cd --watch

# Via ArgoCD UI
# Open ArgoCD dashboard and view application
```

### Verify Deployment

```bash
# Check all resources
kubectl get all -n threetierapp

# Check pods status
kubectl get pods -n threetierapp -o wide

# Check services
kubectl get svc -n threetierapp

# Check ingress
kubectl get ingress -n threetierapp

# Get ALB URL
kubectl get ingress todo-app-ingress -n threetierapp \
  -o jsonpath='{.status.loadBalancer.ingress[0].hostname}'

# Test application
ALB_URL=$(kubectl get ingress todo-app-ingress -n threetierapp -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
curl http://$ALB_URL
```

---

## 📊 Monitoring

### ArgoCD Application Status

```bash
# Check sync status
kubectl get application gitops-three-tier-cd -n argocd

# Detailed status
kubectl describe application gitops-three-tier-cd -n argocd

# Via ArgoCD CLI
argocd app get gitops-three-tier-cd

# View sync history
argocd app history gitops-three-tier-cd
```

### Image Updater Logs

```bash
# Follow Image Updater logs
kubectl logs -n argocd -l app.kubernetes.io/name=argocd-image-updater -f

# Expected output:
# time="..." level=info msg="Starting image update cycle, considering 1 annotated application(s)"
# time="..." level=info msg="Processing update for myfrontend: current=41, latest=42"
# time="..." level=info msg="Successfully updated image for myfrontend"
```

### Application Logs

```bash
# Frontend logs
kubectl logs -n threetierapp -l app=frontend --tail=100 -f

# Backend logs
kubectl logs -n threetierapp -l app=backend --tail=100 -f

# MySQL logs
kubectl logs -n threetierapp -l app=mysql --tail=100 -f

# All pods logs
kubectl logs -n threetierapp --all-containers=true --tail=50
```

### Health Checks

```bash
# Check pod health
kubectl get pods -n threetierapp

# Describe pod for events
kubectl describe pod <pod-name> -n threetierapp

# Check service endpoints
kubectl get endpoints -n threetierapp

# Test internal connectivity
kubectl run test-pod --image=busybox -it --rm -n threetierapp -- sh
# Inside pod:
wget -O- http://backend-service:8080/health
wget -O- http://frontend-service:80
```

---

## 🔧 Maintenance

### Rollback to Previous Version

```bash
# Via ArgoCD CLI
argocd app rollback gitops-three-tier-cd <revision-number>

# Or manually revert Git commit
git revert <commit-hash>
git push origin main
# ArgoCD will auto-sync to previous state

# View available revisions
argocd app history gitops-three-tier-cd
```

### Update Configurations

```bash
# 1. Edit manifests in Git
vim frontend-deployment.yaml

# 2. Commit changes
git add frontend-deployment.yaml
git commit -m "Update frontend replicas to 5"
git push origin main

# 3. ArgoCD auto-syncs (if automated sync enabled)
# Or manually sync:
argocd app sync gitops-three-tier-cd
```

### Scale Applications

```bash
# Manual scaling (will be overridden by ArgoCD)
kubectl scale deployment frontend -n threetierapp --replicas=5

# Proper way: Update deployment manifest
vim frontend-deployment.yaml
# Change: replicas: 5
git commit -am "Scale frontend to 5 replicas"
git push origin main
```

### View Application Resources

```bash
# All resources
kubectl get all -n threetierapp

# Specific resource types
kubectl get deployments -n threetierapp
kubectl get statefulsets -n threetierapp
kubectl get services -n threetierapp
kubectl get ingress -n threetierapp
kubectl get pvc -n threetierapp

# Resource usage
kubectl top pods -n threetierapp
kubectl top nodes
```

### Clean Up

```bash
# Delete ArgoCD application (will delete all resources)
kubectl delete application gitops-three-tier-cd -n argocd

# Or delete namespace (will delete all resources)
kubectl delete namespace threetierapp

# Manual cleanup
kubectl delete -f ingress.yaml -n threetierapp
kubectl delete -f frontend-service.yaml -n threetierapp
kubectl delete -f frontend-deployment.yaml -n threetierapp
kubectl delete -f backend-service.yaml -n threetierapp
kubectl delete -f backend-deployment.yaml -n threetierapp
kubectl delete -f mysql-service.yaml -n threetierapp
kubectl delete -f mysql-deployment.yaml -n threetierapp
kubectl delete -f secrets-configmap.yaml -n threetierapp
```

---

## 🔗 Related Repositories

- **CI Pipeline**: [Three-Tier-Todo-App-CI](https://github.com/17J/Three-Tier-Todo-App-CI)
- **Infrastructure**: [Terraform-AWS-EKS](https://github.com/17J/Terraform-AWS-EKS)

---

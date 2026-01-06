# ArgoCD Multi-Application Deployment on Kubernetes

## 🚀 Project Overview

This DevOps project demonstrates a comprehensive GitOps workflow using ArgoCD to manage multiple Kubernetes applications with different deployment strategies and tools. The project showcases automated synchronization, secure certificate management, and canary deployment patterns.

## 📁 Project Structure
```
Argo_Repo/
├── ArgoApps/                    # Bootstrap directory - ArgoCD Applications
│   ├── canary.yaml              # Canary deployment app definition
│   ├── candy-web.yaml           # Caddy webserver app definition
│   └── webserver.yaml           # Nginx webserver app definition
│
├── canary/                      # Kustomize-based canary deployment
│   ├── base/                    # Base Kubernetes manifests
│   │   ├── deployment.yaml
│   │   ├── kustomization.yaml
│   │   └── service.yaml
│   └── overlays/                # Environment-specific overlays
│       ├── canary/              # Canary environment (tag-based switching)
│       │   ├── configmap.yaml
│       │   ├── deployment-patch.yaml
│       │   ├── ingress-canary.yaml
│       │   ├── kustomization.yaml
│       │   └── service-patch.yaml
│       └── prod/                # Production environment
│           ├── configmap.yaml
│           ├── deployment-patch.yaml
│           ├── ingress.yaml
│           ├── kustomization.yaml
│           └── service-patch.yaml
│
├── candy-web/                   # Caddy webserver with Kustomize
│   ├── base/                    # Base manifests
│   │   ├── deployment.yaml
│   │   ├── index.html
│   │   ├── ingress.yaml
│   │   ├── kustomization.yaml
│   │   ├── sealedsecret.yaml   # Encrypted TLS certificates
│   │   ├── service.yaml
│   │   ├── tls.crt
│   │   └── tls.key
│   └── overlays/                # Custom overlays
│       ├── index.html
│       └── kustomization.yaml
│
└── webserver/                   # Nginx webserver with plain manifests
    ├── deployment.yaml
    ├── ingress.yaml
    ├── sealedsecret.yaml        # Encrypted TLS certificates
    ├── service.yaml
    ├── tls.crt
    └── tls.key
```

## 🎯 Key Features

### 1. **Bootstrap Pattern**
- **App of Apps**: The `ArgoApps/` directory contains ArgoCD Application manifests that automatically sync all applications
- Single entry point for managing multiple applications
- Declarative GitOps approach for application deployment

### 2. **Nginx Webserver (webserver/)**
- Traditional Kubernetes manifests
- Nginx-based web application
- TLS termination at Ingress level
- **Sealed Secrets** for secure certificate management

### 3. **Caddy Webserver (candy-web/)**
- Modern alternative to Nginx
- Kustomize-based configuration management
- Base + Overlay structure for environment management
- **Sealed Secrets** for encrypted TLS certificates
- Customizable content through overlays

### 4. **Canary Deployment (canary/)**
- Advanced deployment strategy using Kustomize
- **Tag-based switching** (not branch-based)
- Separate canary and production overlays
- Traffic splitting capabilities
- Canary-specific ingress rules

## 🔐 Security with Sealed Secrets

This project uses **Sealed Secrets** to encrypt sensitive data (TLS certificates) before storing them in Git:

- Certificates are encrypted using `kubeseal`
- Only the Kubernetes cluster can decrypt them
- Safe to store in public repositories
- Automatic decryption in-cluster

## 🛠️ Technologies Used

- **Kubernetes**: Container orchestration
- **ArgoCD**: GitOps continuous delivery
- **Kustomize**: Configuration management
- **Sealed Secrets**: Secret encryption
- **Nginx**: Traditional web server
- **Caddy**: Modern web server with automatic HTTPS
- **Ingress**: Traffic routing and TLS termination

## 📦 Deployment Strategy

### Traditional Deployment (Nginx)
```
Git Repository → ArgoCD → Kubernetes → Nginx Pod
```

### Kustomized Deployment (Caddy)
```
Git Repository → ArgoCD → Kustomize → Kubernetes → Caddy Pod
```

### Canary Deployment
```
Git Repository → ArgoCD → Kustomize (Overlay) → 
    ├── Production Pod (stable)
    └── Canary Pod (new version)
```

## 🚦 Canary Deployment Flow

1. **Base Configuration**: Common resources shared between environments
2. **Production Overlay**: Stable production version
3. **Canary Overlay**: New version for testing
4. **Tag-Based Switching**: Deploy specific versions using image tags
5. **Traffic Splitting**: Gradual rollout with weighted traffic distribution

## 🔄 How It Works

1. **Bootstrap**: Deploy the ArgoCD Applications in `ArgoApps/`
2. **Auto-Sync**: ArgoCD automatically syncs all defined applications
3. **Nginx App**: Deploys traditional Nginx webserver with sealed TLS
4. **Caddy App**: Deploys Caddy using Kustomize with overlays
5. **Canary App**: Deploys both production and canary versions
6. **Tag Management**: Switch between versions by updating image tags in overlays

## 🎓 Learning Outcomes

- GitOps principles with ArgoCD
- Kustomize base and overlay patterns
- Secure secret management with Sealed Secrets
- Canary deployment strategies
- Tag-based versioning
- Multi-application orchestration
- Infrastructure as Code (IaC)

## 🔧 Prerequisites

- Kubernetes cluster
- ArgoCD installed
- Sealed Secrets controller installed
- kubectl configured
- kubeseal CLI tool

## 📝 Getting Started

1. Install ArgoCD in your cluster
2. Install Sealed Secrets controller
3. Create sealed secrets from your TLS certificates
4. Apply the bootstrap application: `kubectl apply -f ArgoApps/`
5. Watch ArgoCD sync all applications automatically

## 🌟 Best Practices Demonstrated

- ✅ GitOps workflow
- ✅ Secret encryption at rest
- ✅ Separation of concerns (base vs overlays)
- ✅ Tag-based versioning (not branch-based)
- ✅ Progressive delivery with canary deployments
- ✅ Declarative infrastructure management

---

**Note**: This project showcases production-ready patterns for deploying and managing applications on Kubernetes using modern DevOps tools and methodologies.

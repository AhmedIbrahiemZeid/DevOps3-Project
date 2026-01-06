# ArgoCD Multi-Application Deployment on Kubernetes

## 🚀 Project Overview

This DevOps project demonstrates a comprehensive GitOps workflow using ArgoCD to manage multiple Kubernetes applications with different deployment strategies and tools. The project showcases automated synchronization, secure certificate management, and canary deployment patterns using ArgoCD ApplicationSets.

## 📁 Project Structure
```
Argo_Repo/
├── ArgoApps/                    # Bootstrap directory - ArgoCD Applications
│   ├── canary.yaml              # Canary deployment ApplicationSet definition
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

### 4. **Canary Deployment with ApplicationSet (canary/)**
- Advanced deployment strategy using **ArgoCD ApplicationSet**
- Automated multi-environment deployment (prod + canary)
- **Tag-based switching** (not branch-based)
- Separate canary and production overlays managed by ApplicationSet
- Traffic splitting capabilities
- Canary-specific ingress rules
- Single ApplicationSet generates multiple Applications automatically

## 🔐 Security with Sealed Secrets

This project uses **Sealed Secrets** to encrypt sensitive data (TLS certificates) before storing them in Git:

- Certificates are encrypted using `kubeseal`
- Only the Kubernetes cluster can decrypt them
- Safe to store in public repositories
- Automatic decryption in-cluster

## 🛠️ Technologies Used

- **Kubernetes**: Container orchestration
- **ArgoCD**: GitOps continuous delivery
- **ArgoCD ApplicationSet**: Multi-environment application management
- **Kustomize**: Configuration management
- **Sealed Secrets**: Secret encryption
- **Nginx**: Traditional web server
- **Caddy**: Modern web server with automatic HTTPS
- **Ingress**: Traffic routing and TLS termination

## 📦 Deployment Strategy

### Traditional Deployment (Nginx)
```
Git Repository → ArgoCD Application → Kubernetes → Nginx Pod
```

### Kustomized Deployment (Caddy)
```
Git Repository → ArgoCD Application → Kustomize → Kubernetes → Caddy Pod
```

### Canary Deployment with ApplicationSet
```
Git Repository → ArgoCD ApplicationSet → 
    ├── Production Application → Kustomize (prod overlay) → Production Pod
    └── Canary Application → Kustomize (canary overlay) → Canary Pod
```

## 🚦 Canary Deployment Flow with ApplicationSet

1. **ApplicationSet Definition**: Single YAML defines deployment pattern for multiple environments
2. **Generator**: ApplicationSet automatically creates separate Applications for prod and canary
3. **Base Configuration**: Common resources shared between environments
4. **Production Overlay**: Stable production version deployed automatically
5. **Canary Overlay**: New version for testing deployed simultaneously
6. **Tag-Based Switching**: Deploy specific versions using image tags in overlays
7. **Traffic Splitting**: Gradual rollout with weighted traffic distribution via Ingress annotations

## 🔄 How It Works

1. **Bootstrap**: Deploy the ArgoCD Applications and ApplicationSet in `ArgoApps/`
2. **ApplicationSet Magic**: The canary ApplicationSet automatically generates both prod and canary Applications
3. **Auto-Sync**: ArgoCD automatically syncs all defined applications
4. **Nginx App**: Deploys traditional Nginx webserver with sealed TLS
5. **Caddy App**: Deploys Caddy using Kustomize with overlays
6. **Canary Apps**: ApplicationSet deploys both production and canary versions simultaneously
7. **Tag Management**: Switch between versions by updating image tags in overlays

## 🎓 Learning Outcomes

- GitOps principles with ArgoCD
- **ArgoCD ApplicationSet for multi-environment deployments**
- Kustomize base and overlay patterns
- Secure secret management with Sealed Secrets
- Canary deployment strategies
- Tag-based versioning
- Multi-application orchestration
- Infrastructure as Code (IaC)
- **Automated application generation patterns**

## 🔧 Prerequisites

- Kubernetes cluster
- ArgoCD installed (with ApplicationSet controller)
- Sealed Secrets controller installed
- kubectl configured
- kubeseal CLI tool

## 📝 Getting Started

1. Install ArgoCD in your cluster (ensure ApplicationSet controller is enabled)
2. Install Sealed Secrets controller
3. Create sealed secrets from your TLS certificates
4. Apply the bootstrap application: `kubectl apply -f ArgoApps/`
5. Watch ArgoCD ApplicationSet automatically generate and sync canary applications
6. Monitor both production and canary deployments in ArgoCD UI

## 🌟 Best Practices Demonstrated

- ✅ GitOps workflow
- ✅ Secret encryption at rest
- ✅ Separation of concerns (base vs overlays)
- ✅ Tag-based versioning (not branch-based)
- ✅ Progressive delivery with canary deployments
- ✅ **ApplicationSet for environment automation**
- ✅ Declarative infrastructure management
- ✅ **DRY principle with ApplicationSet generators**

## 💡 Why ApplicationSet for Canary?

Using ApplicationSet for canary deployments provides several advantages:

- **Single Source of Truth**: One ApplicationSet manages multiple environments
- **Automatic Generation**: No need to manually create separate Application manifests
- **Consistency**: Ensures prod and canary follow the same deployment pattern
- **Scalability**: Easy to add more environments (staging, dev, etc.)
- **Reduced Maintenance**: Update deployment logic in one place

---

**Note**: This project showcases production-ready patterns for deploying and managing applications on Kubernetes using modern DevOps tools and methodologies, with a special focus on ArgoCD ApplicationSet for advanced deployment scenarios.

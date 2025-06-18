# GitOps with ArgoCD

## Overview

This project showcases a production-ready GitOps workflow using ArgoCD to manage Kubernetes applications declaratively. It supports Helm charts, integrates Sealed Secrets for secure secret management, and enables continuous delivery with auto-sync and self-healing capabilities.

---

## Key Features

- Git-based declarative application management
- Continuous delivery with ArgoCD
- Sealed Secrets for secure secret handling
- Helm chart support
- Auto-sync and drift detection

---

## Tech Stack

- Kubernetes
- ArgoCD
- Helm
- Bitnami Sealed Secrets

---

## Project Structure

```
.
├── manifests/
│   ├── application.yaml        # ArgoCD application manifest
│   └── sealed-secret.yaml      # Example of a sealed secret
├── README.md
```

---

## Getting Started

### Step 1: Clone the Repository

```bash
git clone https://github.com/Here2ServeU/gitops-with-argocd.git
cd gitops-with-argocd
```

### Step 2: Create EKS Cluster (Optional)

```bash
eksctl create cluster --name gitops-cluster --region us-east-1 --nodes 2
```

### Step 3: Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Access ArgoCD UI at: [https://localhost:8080](https://localhost:8080)

To retrieve the initial admin password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd   -o jsonpath="{.data.password}" | base64 -d && echo
```

### Step 4: Deploy the Application

Edit `application.yaml` to point to your Git repository, then apply:

```bash
kubectl apply -f manifests/application.yaml
```

### Step 5: Manage Secrets Securely

Create and seal your secret:

```bash
kubectl create secret generic my-secret --from-literal=password='s3cr3t' --dry-run=client -o json > secret.json
kubeseal --format yaml < secret.json > manifests/sealed-secret.yaml
kubectl apply -f manifests/sealed-secret.yaml
```

---

## Usage

Use this project to enable GitOps in your Kubernetes environments. ArgoCD continuously watches your Git repository and ensures your cluster remains in sync with the declared state, enabling safe and auditable deployments.

---

## Teardown (Optional)

To remove all EKS resources:

```bash
eksctl delete cluster --name gitops-cluster --region us-east-1
```

---

## Author

**Emmanuel Naweji, 2025**  
Cloud | DevOps | SRE | FinOps | AI Engineer  
Helping businesses modernize infrastructure and guiding engineers into the top 1% through real-world projects and automation-first thinking.

![AWS Certified](https://img.shields.io/badge/AWS-Certified-blue?logo=amazonaws)
![Azure Solutions Architect](https://img.shields.io/badge/Azure-Solutions%20Architect-0078D4?logo=microsoftazure)
![CKA](https://img.shields.io/badge/Kubernetes-CKA-blue?logo=kubernetes)
![Terraform](https://img.shields.io/badge/IaC-Terraform-623CE4?logo=terraform)
![GitHub Actions](https://img.shields.io/badge/CI/CD-GitHub%20Actions-blue?logo=githubactions)
![GitLab CI](https://img.shields.io/badge/CI/CD-GitLab%20CI-FC6D26?logo=gitlab)
![Jenkins](https://img.shields.io/badge/CI/CD-Jenkins-D24939?logo=jenkins)
![Ansible](https://img.shields.io/badge/Automation-Ansible-red?logo=ansible)
![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-orange?logo=argo)
![VMware](https://img.shields.io/badge/Virtualization-VMware-607078?logo=vmware)
![Linux](https://img.shields.io/badge/OS-Linux-black?logo=linux)
![FinOps](https://img.shields.io/badge/FinOps-Cost%20Optimization-green?logo=money)
![OpenAI](https://img.shields.io/badge/AI-OpenAI-ff9900?logo=openai)

---

## Connect with Me

- [LinkedIn](https://www.linkedin.com/in/ready2assist/)
- [GitHub](https://github.com/Here2ServeU)
- [Medium](https://medium.com/@here2serveyou)

---

## Book a Free Consultation

Ready to implement GitOps or scale your Kubernetes platform?  
[Schedule a free 1:1 consultation](https://bit.ly/letus-meet)


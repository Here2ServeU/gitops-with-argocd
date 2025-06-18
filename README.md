# GitOps with ArgoCD for Secure Kubernetes App Delivery

## Real-World Use Case

This project demonstrates how a DevOps team at a fintech company can use GitOps principles with ArgoCD to securely and continuously deploy a customer-facing microservice to an EKS cluster. The team integrates Helm for configuration management and Sealed Secrets for compliance-friendly secret handling. This approach eliminates drift, enables auditability, and allows developers to promote changes via Git merges rather than manual kubectl actions.

---

## Scenario: Secure Deployment of a Customer API

**Business Case:**  
A fintech company needs to deploy a customer management microservice (`customer-api`) to Kubernetes with strict compliance rules around secret handling (e.g., API keys, DB credentials). It must be version-controlled, auditable, and automatically deployed to EKS with minimal manual steps.

**Tech Goals:**  
- Git as the single source of truth
- Use ArgoCD for deployment
- Store secrets securely (e.g., API_KEY, DB_PASSWORD)
- Ensure environments self-heal if drift occurs

---

## Tech Stack

- **Kubernetes** (EKS)
- **ArgoCD** for GitOps
- **Helm** for templating
- **Sealed Secrets** for secure secret management
- **eksctl** for infrastructure provisioning

---

## Project Structure

```
.
├── manifests/
│   ├── application.yaml          # ArgoCD application manifest
│   ├── sealed-secret.yaml        # Encrypted secret for Kubernetes
│   └── kustomization.yaml        # Optional Kustomize file
├── charts/
│   └── customer-api/             # Helm chart for the microservice
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
│           ├── deployment.yaml
│           └── service.yaml
├── secrets/
│   └── secret.json               # Temporary unsealed secret (not committed)
├── scripts/
│   └── bootstrap.sh              # Optional setup script
├── README.md
```

---

## Setup Steps

### 1. Clone the Repository

```bash
git clone https://github.com/Here2ServeU/gitops-with-argocd.git
cd gitops-with-argocd
```

### 2. (Optional) Create the EKS Cluster

```bash
eksctl create cluster --name gitops-cluster --region us-east-1 --nodes 2
```

---

### 3. Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Login password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd   -o jsonpath="{.data.password}" | base64 -d && echo
```

---

### 4. Deploy the ArgoCD Application

Update `application.yaml` to point to this repo. Then:

```bash
kubectl apply -f manifests/application.yaml
```

---

### 5. Manage Secrets with Sealed Secrets

#### Step A: Create Your Secret

```bash
kubectl create secret generic customer-api-secret --from-literal=API_KEY='prod_key_123' --dry-run=client -o json > secrets/secret.json
```

#### Step B: Install the Controller

```bash
kubectl apply -f https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.23.0/controller.yaml
```

#### Step C: Install `kubeseal`

**macOS:** `brew install kubeseal`  
**Linux:** Use the release tarball  
**Windows:** Download `kubeseal.exe` and add to your PATH

#### Step D: Seal the Secret

```bash
kubeseal --controller-namespace kube-system --format yaml < secrets/secret.json > manifests/sealed-secret.yaml
kubectl apply -f manifests/sealed-secret.yaml
```

---

## Notes on Helm Chart

Edit values in `charts/customer-api/values.yaml` to adjust environment-specific settings like:

```yaml
image:
  repository: your-registry/customer-api
  tag: v1.0.0

resources:
  limits:
    cpu: 200m
    memory: 256Mi
```

---

## Troubleshooting Common Errors

**Missing Chart.yaml?**

- Ensure `charts/customer-api/Chart.yaml` exists
- If not using Helm, remove the `helm:` block from `application.yaml`

**Secret not decrypted?**

- Make sure the controller is running:
  ```bash
  kubectl get pods -n kube-system | grep sealed-secrets
  ```

---

## Cleanup

```bash
eksctl delete cluster --name gitops-cluster --region us-east-1
```

---

## 👤 Author

**Emmanuel Naweji**  
Cloud | DevOps | SRE | FinOps | AI Engineer  
Helping businesses build secure and scalable infrastructure using GitOps and automation-first strategies.

---

## Let's Connect

- [LinkedIn](https://www.linkedin.com/in/ready2assist/)
- [GitHub](https://github.com/Here2ServeU)
- [Medium](https://medium.com/@here2serveyou)

---

## Free Consultation

Want help implementing GitOps at your company?  
[Book a session](https://bit.ly/letus-meet)


# Flask Authen Manifest

This repository contains Kubernetes manifests for deploying the **Flask Authentication App** using ArgoCD.  
It is designed to work with the companion repository:  
👉 [flask-app-authen](https://github.com/khanhquoc4114/flask-app-authen)  

The setup includes:

- Kubernetes deployment & service manifests
- ArgoCD Application manifest (`argocd-application.yaml`)
- GitHub Actions integration to automatically update image tags in this repo when a new Docker image is built and pushed from `flask-app-authen`

---

## Repository Structure

``` bash
.
├── k8s/
│   ├── backend-deployment.yaml
│   ├── frontend-deployment.yaml
│   └── manifest.yaml
├── argocd-application.yaml
└── README.md
```

---

## Prerequisites

- A running Kubernetes cluster (e.g., k3s, GKE, EKS, AKS, or Minikube)
- [ArgoCD](https://argo-cd.readthedocs.io/) installed in the cluster
- [kubectl](https://kubernetes.io/docs/tasks/tools/) configured for your cluster

---

## Deploying with ArgoCD

1. Apply the **ArgoCD Application** manifest:

   ```bash
   kubectl apply -f argocd-application.yaml -n argocd
   ```

   > Replace `argocd` with the namespace where ArgoCD is installed if different.

2. The application will appear in the ArgoCD UI.  
   ArgoCD will continuously sync the manifests from this repository and keep your cluster up to date.

---

## GitHub Actions CI/CD Flow

- The **flask-app-authen** repository builds and pushes a new Docker image whenever changes are pushed to `main`.  
- After a successful image push, GitHub Actions in `flask-app-authen` updates the `deployment.yaml` in this repository (`flask-authen-manifest`) with the new image tag.  
- This triggers ArgoCD to detect changes and redeploy the updated image to the cluster automatically.

### Workflow Summary

1. **Push code →** `flask-app-authen`
2. **GitHub Actions →** Build & push Docker image
3. **GitHub Actions →** Update `flask-authen-manifest` with new image tag
4. **ArgoCD →** Sync manifests to cluster
5. ✅ New app version is deployed

---

## Verify Deployment

Once synced in ArgoCD, check the service:

```bash
kubectl get svc -n flask-authen
```

Forward the port or use LoadBalancer/Ingress depending on your cluster setup:

```bash
kubectl port-forward svc/flask-authen 5000:5000 -n flask-authen
```

Now visit: [http://localhost:5000](http://localhost:5000)

---

## Related Repositories

- [flask-app-authen](https://github.com/khanhquoc4114/flask-app-authen) – Source code & Docker image build  
- [flask-authen-manifest](https://github.com/khanhquoc4114/flask-authen-manifest) – Kubernetes manifests (this repo)

---

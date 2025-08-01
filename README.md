
# Flask Authen App - Kubernetes Manifests

## 📌 Overview

Repo này chứa các **Kubernetes manifests** để triển khai ứng dụng `flask-authen` lên cluster Kubernetes theo mô hình **GitOps** dùng Argo CD.

Ứng dụng gồm:

- **Deployment**: Flask application
- **Service**: Expose app trong cluster
- **Namespace**: `flask-authen`

---

## 🗂 Repo Structure

``` bash
manifest/
├── k8s/
│   └── manifest.yaml   # Deployment + Service cho Flask app
├── .env.example
└── README.md
```

---

## 🚀 Deployment Options

### **1. Apply trực tiếp bằng kubectl**

``` bash
kubectl apply -f k8s/manifest.yaml
```

### **2. Dùng Argo CD (GitOps)**

- Tạo Application trong Argo CD:

```bash
argocd app create flask-authen \
  --repo https://github.com/<your-org>/manifest.git \
  --path k8s \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace flask-authen
```

- Sync ứng dụng:

```bash
argocd app sync flask-authen
```

---

## 🔗 Workflow với CI/CD

- Khi pipeline build image mới, tag mới được cập nhật trong file `manifest.yaml` ở trường:

```yaml
containers:
  - name: flask-container
    image: wuoc4114/flask-authen:<new-tag>
```

- GitOps tool (Argo CD) tự động sync và deploy version mới vào cluster.

---

## ⚙ Configuration

| Key         | Description             |
|------------|-------------------------|
| Namespace  | `flask-authen`         |
| Image      | `wuoc4114/flask-authen`|
| Port       | 5000                   |

---

## ✅ Best Practices

- Nên chuyển từ **raw manifest** sang **Helm** hoặc **Kustomize** để:
  - Dễ quản lý environment (dev/staging/prod)
  - Dễ update image tag từ CI/CD pipeline
- Dùng **sealed-secrets** cho secret management

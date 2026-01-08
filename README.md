🧩 Project: GitOps Deployment using Argo CD + GitHub + Kubernetes
🎯 Project Goal

Automatically deploy and update a Kubernetes application by pushing code to GitHub, using Argo CD as the GitOps controller.

🛠️ Tech Stack
Kubernetes (Minikube / Kind / AKS / EKS / GKE)
Argo CD
GitHub
NGINX sample app
YAML manifests

📌 Architecture (Simple Flow)
Developer → GitHub Repo → Argo CD → Kubernetes Cluster
GitHub = Source of Truth
Argo CD = Watches GitHub and syncs
Kubernetes = Runs the app

📁 Step 1: Create GitHub Repository
Create a repo:
argocd-gitops-demo
Folder structure
argocd-gitops-demo/
├── manifests/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── namespace.yaml
└── README.md

📄 Step 2: Kubernetes Manifests
namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: demo
  
deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: demo
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
        
service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: demo
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80

👉 Commit and push to GitHub.

Step 3: Setup Kubernetes Cluster

Using Minikube (simplest):

minikube start
kubectl get nodes

🔄 Step 4: Install Argo CD
kubectl create namespace argocd

kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


Verify:

kubectl get pods -n argocd

🌐 Step 5: Access Argo CD UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
Open:

https://localhost:8080

Get admin password
kubectl get secret argocd-initial-admin-secret \
-n argocd -o jsonpath="{.data.password}" | base64 -d

📦 Step 6: Create Argo CD Application
Option A: UI (Beginner Friendly)

Application Name: nginx-gitops

Project: default

Sync Policy: Automatic

Repo URL: https://github.com/<your-username>/argocd-gitops-demo

Path: manifests

Destination:

Cluster URL: https://kubernetes.default.svc

Namespace: demo

Click Create

Option B: YAML (Recommended for Learning)
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-gitops
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/<your-username>/argocd-gitops-demo
    targetRevision: HEAD
    path: manifests
  destination:
    server: https://kubernetes.default.svc
    namespace: demo
  syncPolicy:
    automated:
      prune: true
      selfHeal: true

Apply:

kubectl apply -f application.yaml

✅ Step 7: Verify Deployment
kubectl get pods -n demo
kubectl get svc -n demo


Access app:

minikube service nginx-service -n demo

🔁 Step 8: GitOps in Action (Most Important Step)

Edit deployment.yaml

replicas: 3


Commit & push to GitHub

👉 Argo CD will auto-sync and update pods without kubectl apply

Check:

kubectl get pods -n demo

🧠 What You Learn from This Project

✅ GitOps principles
✅ Argo CD architecture
✅ Continuous Delivery with Git
✅ Auto-sync & self-healing
✅ Kubernetes deployment lifecycle

⭐ Beginner Enhancements (Next Level)

Add Helm chart

Add Kustomize

Enable RBAC in Argo CD

Add private GitHub repo

Add Argo CD Notifications

Multi-environment setup (dev/prod)

🧪 Interview Angle (Very Important)

You can confidently say:

“I implemented a GitOps workflow using Argo CD where GitHub was the single source of truth. Any change pushed to GitHub was automatically synchronized to Kubernetes with self-healing and auto-pruning enabled.”

If you want, next I can:

Convert this to Helm-based project

Create multi-env (dev/prod) GitOps

Prepare Argo CD interview Q&A

Provide a real-world enterprise GitOps design












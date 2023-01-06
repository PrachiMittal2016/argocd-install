# ArgoCD Installation

git clone -b prachi https://github.com/PrachiMittal2016/argocd-install.git

kubectl apply -k ./install/.

--------
Get Admin Password and Login to ArgoCD:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

Port Forward: (Only for test purpose, else follow Ingress setup steps)
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Install ArgoCD Client:

# Login to ArgoCD:

argocd login localhost:8080
WARNING: server certificate had error: x509: “Argo CD” certificate is not trusted. Proceed insecurely (y/n)? y
Username: admin
Password: 
'admin:login' logged in successfully
Context 'localhost:8080' updated

# Ingress SetUp:


# SSO Setup:

# Configuration:

Connect to Git repos:


kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

kubectl port-forward svc/argocd-server -n argocd 8080:443

kubectl apply -f .\manifests\projects.yaml

kubectl apply -f .\overlays\prod\app-of-apps.yaml

kubectl apply -f .\install\overlays\repo-secrets.yaml

kubectl apply -f .\overlays\prod\app-of-apps.yaml

# ArgoCD Installation

git clone -b prachi https://github.com/PrachiMittal2016/argocd-install.git

kubectl apply -k ./install/.

--------

# Install ArgoCD Client:

brew install argocd

# Get Admin Password:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

# Port Forward: (Only for test purpose, else follow Ingress setup steps)
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Ingress SetUp:
https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-helm/

If we want to termintate the ssl at the ingress controller.

1) set the insecure mode in argocd-server configmap  argocd-cmd-params-cm ( restart the deployment )
    server.insecure = true
2) k rollout restart deploy argocd-server -n argocd

3) set the following use-proxy-protocol: "false" in the cm argocd-ingress-ingress-nginx-controller

4) deploy the helm deployment    

helm upgrade --install argocd-ingress ingress-nginx/ingress-nginx  --version 4.4.0 \
--namespace argocd \
--set controller.ingressClassResource.name=nginx-argocd-class \
--set controller.ingressClass=nginx-argocd-class \
--set controller.ingressClassResource.controllerValue="cxai.dev.sap/argocd-ingress" \
--set controller.ingressClassResource.enabled=true \
--set controller.ingressClassByName=true \
--set controller.service.externalTrafficPolicy=Local

5) deploy the ingress 

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-server-ingress
  namespace: argocd
  annotations:
    #cert-manager.io/cluster-issuer: letsencrypt-prod
    #kubernetes.io/tls-acme: "true"
    nginx.ingress.kubernetes.io/enable-access-log: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    # My home address and login.microsoftonline.com
    nginx.ingress.kubernetes.io/whitelist-source-range: "192.222.186.105/32,40.126.28.11/32,40.126.28.13/32,40.126.28.14/32,40.126.28.18/32,40.126.28.19/32,40.126.28.22/32,40.126.28.23/32,40.126.7.32/32"
spec:
  ingressClassName: nginx-argocd-class
  rules:
  - host: argocd.cxai.dev.sap
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: argocd-server
            port:
              name: http
  tls:
  - hosts:
    - argocd.cxai.dev.sap
    secretName: argocd-server-tls 
-------
# Login to ArgoCD:

argocd login localhost:8080
WARNING: server certificate had error: x509: “Argo CD” certificate is not trusted. Proceed insecurely (y/n)? y
Username: admin
Password: 
'admin:login' logged in successfully
Context 'localhost:8080' updated

# Update Admin Password

argocd account update-password
*** Enter password of currently logged in user (admin): 
*** Enter new password for user admin: 
*** Confirm new password for user admin: 
Password updated
Context 'localhost:8080' updated

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

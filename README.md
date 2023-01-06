# argocd-install

git clone -b prachi https://github.com/PrachiMittal2016/argocd-install.git

kubectl apply -k ./install/.

--------

Configuration:

Connect to Git repos:


kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

kubectl port-forward svc/argocd-server -n argocd 8080:443

kubectl apply -f .\manifests\projects.yaml

kubectl apply -f .\overlays\prod\app-of-apps.yaml

kubectl apply -f .\install\overlays\repo-secrets.yaml

kubectl apply -f .\overlays\prod\app-of-apps.yaml

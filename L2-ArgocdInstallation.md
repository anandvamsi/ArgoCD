# Installing ArgoCD and argocd CLI


## Argocd Installation

Create a new namespace argocd
```bash
kubectl create ns argocd
namespace/argocd created
```
Deploy argocd in the new namespace
```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/applicationsets.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io created
serviceaccount/argocd-application-controller created
serviceaccount/argocd-applicationset-controller created
serviceaccount/argocd-dex-server created
serviceaccount/argocd-notifications-controller created
serviceaccount/argocd-redis created
serviceaccount/argocd-repo-server created
serviceaccount/argocd-server created
role.rbac.authorization.k8s.io/argocd-application-controller created
role.rbac.authorization.k8s.io/argocd-applicationset-controller created
role.rbac.authorization.k8s.io/argocd-dex-server created
role.rbac.authorization.k8s.io/argocd-notifications-controller created
role.rbac.authorization.k8s.io/argocd-redis created
role.rbac.authorization.k8s.io/argocd-server created
clusterrole.rbac.authorization.k8s.io/argocd-application-controller created
clusterrole.rbac.authorization.k8s.io/argocd-applicationset-controller created
clusterrole.rbac.authorization.k8s.io/argocd-server created
rolebinding.rbac.authorization.k8s.io/argocd-application-controller created
rolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller created
rolebinding.rbac.authorization.k8s.io/argocd-dex-server created
rolebinding.rbac.authorization.k8s.io/argocd-notifications-controller created
rolebinding.rbac.authorization.k8s.io/argocd-redis created
rolebinding.rbac.authorization.k8s.io/argocd-server created
clusterrolebinding.rbac.authorization.k8s.io/argocd-application-controller created
clusterrolebinding.rbac.authorization.k8s.io/argocd-applicationset-controller created
clusterrolebinding.rbac.authorization.k8s.io/argocd-server created
configmap/argocd-cm created
configmap/argocd-cmd-params-cm created
configmap/argocd-gpg-keys-cm created
configmap/argocd-notifications-cm created
configmap/argocd-rbac-cm created
configmap/argocd-ssh-known-hosts-cm created
configmap/argocd-tls-certs-cm created
secret/argocd-notifications-secret created
secret/argocd-secret created
service/argocd-applicationset-controller created
service/argocd-dex-server created
service/argocd-metrics created
service/argocd-notifications-controller-metrics created
service/argocd-redis created
service/argocd-repo-server created
service/argocd-server created
service/argocd-server-metrics created
deployment.apps/argocd-applicationset-controller created
deployment.apps/argocd-dex-server created
deployment.apps/argocd-notifications-controller created
deployment.apps/argocd-redis created
deployment.apps/argocd-repo-server created
deployment.apps/argocd-server created
statefulset.apps/argocd-application-controller created
networkpolicy.networking.k8s.io/argocd-application-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-applicationset-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-dex-server-network-policy created
networkpolicy.networking.k8s.io/argocd-notifications-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-redis-network-policy created
networkpolicy.networking.k8s.io/argocd-repo-server-network-policy created
networkpolicy.networking.k8s.io/argocd-server-network-policy created
```
Validate the pods
```bash
argocd-application-controller-0                    1/1     Running   0          38s
argocd-applicationset-controller-597675595-42bm7   1/1     Running   0          39s
argocd-dex-server-5674dc45f9-lblq6                 1/1     Running   0          39s
argocd-notifications-controller-7696b54558-lkh7q   1/1     Running   0          38s
argocd-redis-cd975cdbd-9m9b8                       1/1     Running   0          38s
argocd-repo-server-674756d699-tbt65                1/1     Running   0          38s
argocd-server-65f5d8cc59-9d42c                     0/1     Running   0          38s
```

Retrive the secrets from argocd secrets
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

## Methods to expose the ArgoCD 
### Load balancer
Change the argo-cd  service type to LoadBalancer

### Ingress
Create an ingress resource that point into argocd-service services

### Port forwarding
Kubectl port-forward svc/argo-server 0b argocd 8080:443

## Argocd CLI installation process to login
```bash
VERSION=$(curl -s https://api.github.com/repos/argoproj/argo-cd/releases/latest | grep tag_name | cut -d '"' -f 4)
curl -sSL -o argocd "https://github.com/argoproj/argo-cd/releases/download/${VERSION}/argocd-linux-amd64"
chmod +x argocd
sudo mv argocd /usr/local/bin/

 argocd version
argocd: v3.0.12+ed1e239
  BuildDate: 2025-07-25T20:01:42Z
  GitCommit: ed1e2397ef9af6d23b284e39d504308cdda1957b
  GitTreeState: clean
  GoVersion: go1.24.4
  Compiler: gc
  Platform: linux/amd64
{"level":"fatal","msg":"Argo CD server address unspecified","time":"2025-08-04T19:20:46+05:30"}
```
### portforward Argo CD
```bash
kubectl port-forward svc/argocd-server 8080:443
```
### Login into Argocd
```bash
argocd login localhost:8080
<Enter the username and password
```

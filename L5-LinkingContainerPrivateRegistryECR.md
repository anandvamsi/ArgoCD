# Linking AWS ECR into ArgoCD

## Step 1: Create secrets in OC ::- Note execute this command in a EC2 with proper permission
```bash
oc create secret docker-registry ecr-secret \
  --docker-server=XXXXXXX.dkr.ecr.us-west-2.amazonaws.com \
  --docker-username=AWS \
    --docker-password=$(aws ecr get-login-password --region ) \
   -n openshift-gitops
```

## Step 2:- Create a service account that links to secrets created in step1
```bash
oc patch serviceaccount default \
  -p '{"imagePullSecrets": [{"name": "ecr-secret"}]}' \
  -n openshift-gitops
```

## Step3 :- Deploy Application Manifest in Argo
```bash
project: default
source:
  repoURL: https://gXXXXXXXXXXXXXXXXXXXXXXXXX-ease.git
  path: content/modules/ROOT/examples/bgd-base
  targetRevision: argo-br1
destination:
  server: https://kubernetes.default.svc
  namespace: openshift-gitops
```

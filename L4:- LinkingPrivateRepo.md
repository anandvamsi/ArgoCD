# Linking private repository
There are two method to link one is using PAT token and other is using SSH Key



## PAT Token Method
- Create a PAT token from the Github
- Create a secret using PAT token,username and password

```bash
apiVersion: v1
kind: Secret
metadata:
  name: private-repo-secret
  namespace: openshift-gitops
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  url: https://github.com/<your-org>/<your-repo>.git
  username: oauth2
  password: <your-pat-token>
```

## SSH Key
There are two methods to deal with adding the keys using manifest and second is using command line
- Step 1 generate private and public ssh keys

### Method 2
```bash 
argocd repo add git@github.com:anandvamsi/ArgoCD.git --ssh-private-key-path ./argo --name argorepo
Repository 'git@github.com:anandvamsi/ArgoCD.git' added
```

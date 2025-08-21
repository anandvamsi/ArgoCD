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

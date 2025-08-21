# Argo Projects

Projects provide a logical grouping of applications
- app1
- app2
- app3

Project Rules that enables you to create roles with set of polices "Permissions" to grant access to a projects application

```bash
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: dev-project
  namespace: argocd
spec:
  description: Dev project
  sourceRepos:
  - '*'

  destinations:
  - namespace: ns1
    server: https://kubernetes.default.svc

  clusterResourceWhitelist:
  - group: '*'
    kind: '*'

  namespaceResourceWhitelist:
  - group: '*'
    kind: '*'
```


### With Roles with privilages
```bash
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: project-with-role
  namespace: argocd
spec:
  description: project with ci-role
  sourceRepos: 
  - '*'

  destinations:
  - namespace: '*'
    server: '*'

  clusterResourceWhitelist:
  - group: '*'
    kind: '*'

  namespaceResourceWhitelist:
  - group: '*'
    kind: '*'

  roles:
  - name: ci-role
    description: Sync privileges for project-with-role
    policies:
    - p, proj:project-with-role:ci-role, applications, sync, project-with-role/*, allow
    - p, proj:project-with-role:ci-role, applications, get, project-with-role/*, allow
controlplane:~$ 
```


## To Create Token to login via cli
```bash
argocd proj role create-token PROJECT-NAME ROLE-NAME
```

```bash
controlplane:~$ argocd proj role create-token project-with-role ci-role
Create token succeeded for proj:project-with-role:ci-role.
  ID: 8e0d6256-2b33-416e-8ffd-bfa7ad75a529
  Issued At: 2025-08-21T06:07:17Z
  Expires At: Never
  Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVffCJ9.eyJpc3MiOiJhcmdvY2QiLCJzdWIiOiJwcm9qOnByb2plY3Qtd2l0aC1yb2xlOmNpLXJvbGUiLCJuYmYiOjE3NTU3NTY0MzcsImlhdCI6MTc1NTc1NjQzNywianRpIjoiOGUwZDYyNTYtMmIzMy00MTZlLThmZmQtYmZhN2FkNzVhNTI5In0.x_NTdY6Zv0Bz1ZBjQNKFN5kxga6f_V0VHqx-W2noXGQ
```

### Using token to authenticate  and delete the project:- Error because no privilages
```bash
argocd app delete demo --grpc-web --auth-token $TOKEN     
Are you sure you want to delete 'demo' and all its resources? [y/n]
y
FATA[0002] rpc error: code = PermissionDenied desc = permission denied: applications, delete, project-with-role/demo, sub: proj:project-with-role:ci-role, iat: 2025-08-21T06:07:17Z
```

### Using token to authenticate and syn the project
```bash
argocd app sync demo --grpc-web --auth-token $TOKEN
TIMESTAMP                  GROUP        KIND   NAMESPACE                  NAME    STATUS    HEALTH        HOOK  MESSAGE
2025-08-21T06:09:06+00:00            Service        demo          guestbook-ui  OutOfSync  Missing              
2025-08-21T06:09:06+00:00   apps  Deployment        demo          guestbook-ui  OutOfSync  Missing              
2025-08-21T06:09:08+00:00          Namespace                              demo   Running   Synced              namespace/demo created
2025-08-21T06:09:09+00:00            Service        demo          guestbook-ui    Synced  Healthy              
2025-08-21T06:09:09+00:00            Service        demo          guestbook-ui    Synced   Healthy              service/guestbook-ui created
2025-08-21T06:09:09+00:00   apps  Deployment        demo          guestbook-ui  OutOfSync  Missing              deployment.apps/guestbook-ui created
2025-08-21T06:09:09+00:00   apps  Deployment        demo          guestbook-ui    Synced  Progressing              deployment.apps/guestbook-ui created

Name:               demo
Project:            project-with-role
Server:             https://kubernetes.default.svc
Namespace:          demo
URL:                http://172.30.1.2:32073/applications/demo
Repo:               https://github.com/mabusaa/argocd-example-apps.git
Target:             master
Path:               guestbook
SyncWindow:         Sync Allowed
Sync Policy:        <none>
Sync Status:        Synced to master (fa875a8)
Health Status:      Progressing

Operation:          Sync
Sync Revision:      fa875a82cc8438f051da7d67dd1e6ca863d638a4
Phase:              Succeeded
Start:              2025-08-21 06:09:06 +0000 UTC
Finished:           2025-08-21 06:09:09 +0000 UTC
Duration:           3s
Message:            successfully synced (all tasks run)

GROUP  KIND        NAMESPACE  NAME          STATUS   HEALTH       HOOK  MESSAGE
       Namespace              demo          Running  Synced             namespace/demo created
       Service     demo       guestbook-ui  Synced   Healthy            service/guestbook-ui created
apps   Deployment  demo       guestbook-ui  Synced   Progressing        deployment.apps/guestbook-ui created
```



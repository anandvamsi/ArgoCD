# Basics of ArgoCD

## what is gitops
## why we need ArgoCD pros and cons
## Core concepts of ArgoCD
## States in ArgoCD
## Argo CD Architecture overview


```bash
ArgoCD
Gitops based CD with pull model design

Developer---commit---->Git-----pullChanages---->ArgoCD-----SyncClusterStatus----->K8s

ArgoCD is not responsible of building application docker Images, You need a CI system to do this


Need to understand what is the difference between push and pull model pros and cons.


Core-Concepts
---------------

Application ::- Defines sources and destination to deploy group of K8s  resources
- Source: K8s Manifest
- Destination:- cluster and namespaces

ArgoCD supports below tools as sources
- Helm charts
- Kustomize application
- Directory of YAML files

Projects::- Logical grouping of applications
application1, applicaton2, application2


States
------
Desired state:- described in git
Actual State:- what is actually running

Sync
---- 
Process of making the desired state= actual set

Refresh
--------
Compare the latest code in Git with the live state
ArgoCD automatically refreshes every 3 minutes


## Argo CD architecture Overview.

Components

ArgoCD consist of 3 main components

- Argo Server(API + Webserver) ::- Its a gPRC/REST server which exposes the API consumed by the Web UI, CLI
   Applicaton Management(Create,Delete,Update)
   Application Operations(Sync and ROllback)
   Repos and cluster Management
   Authentication  

- Argo RepoServer::-
Its a internal Service that is responsible of cloning remote git repos and generate the needed
K8s manifest:-

Clone git repo
Generate K8s manifest

- Argo Application Controller:;-
Its a Kubernetes controller which continuously monitors running applications and compares the 
current agaist the desired state.
- Communicate with Repo Server to get the generated manifest
- communicate with K8s to get actual cluster state.
- Invoking user-defined hooks for lifecycle events(PreSync,Sync and Post SYnc)


Additional Components
Redis: Used for caching
Dex: Identity service to integrate with external identity provider
ApplicationSetController:- deploying application to multiple clusters.

````

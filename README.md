### Introduction

This repo is a small demo of how to deploy an AMQ Streams Kafka instance using OpenShift GitOps.

### Pre-Requisites

This demo requires that the OpenShift GitOps and AMQ Streams operator be deployed in the cluster

### Demo

1. Create two namespaces as follows:
    a. `int-dev`. This is where we will install the Kafka cluster
    b. `int-tools`. This is where we will install the OpenShift GitOps instance

2. In the Openshift console, navigate to the Installed Operators while making sure that `int-tools` is selected

3. In Installed Operators, select OpenShift GitOps and click the Argo CD tab. Click the create button and paste the contents from argocd-cr.yaml here. Select Save

4. Wait for the Argo CD instance to be running, `oc get pods` should show the following:

```
$ oc get pods -n int-tools
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          6m19s
argocd-applicationset-controller-75797955d4-8krzn   1/1     Running   0          6m19s
argocd-dex-server-75698c4ffd-2db28                  1/1     Running   0          6m19s
argocd-redis-6fb8d68fd5-2nn8m                       1/1     Running   0          6m19s
argocd-repo-server-585d76876-wq655                  1/1     Running   0          6m19s
argocd-server-d6ccff7c6-ztksg                       1/1     Running   0          6m19s
```

5. Prior to using the Argo CD we need enable it to manage the `int-dev` namespace, typically this involves creating a bunch of roles and rolebindings for the serviceaccounts in `int-tools` but OpenShift GitOps can do this for you automatically. To do so, add the following label to `int-dev`:

```
oc label namespace int-dev argocd.argoproj.io/managed-by=int-tools
```

6. verify the new roles have been created in int-dev:

```
$ oc get roles -n int-dev
NAME                                   CREATED AT
argocd-argocd-application-controller   2021-12-14T21:09:46Z
argocd-argocd-dex-server               2021-12-14T21:09:46Z
argocd-argocd-redis-ha                 2021-12-14T21:09:46Z
argocd-argocd-server                   2021-12-14T21:09:46Z
```

7.

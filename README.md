# Namespace Bug Demo

We expect the Kustomization controller to respect the kustomize build outputs:

```bash
❯ kustomize version  
{Version:kustomize/v3.8.5 GitCommit:4052cd4fd8c76a17b5f64e32509f3fba9713fe75 BuildDate:2020-10-08T05:35:40+01:00 GoOs:darwin GoArch:amd64}
❯ kustomize build ./deploy
apiVersion: v1
kind: Namespace
metadata:
  labels:
    foo: bar
  name: runyontr
```

## Environment


```bash
❯ k3d cluster create
INFO[0000] Network with name 'k3d-k3s-default' already exists with ID '4c559e1c52a5a351ba2611ba087748a0a4de62008adb0930b1af7c21c50a9e44'
INFO[0000] Created volume 'k3d-k3s-default-images'      
INFO[0001] Creating node 'k3d-k3s-default-server-0'     
INFO[0001] Creating LoadBalancer 'k3d-k3s-default-serverlb' 
INFO[0007] Cluster 'k3s-default' created successfully!  
INFO[0007] You can now use it like this:                
kubectl cluster-info
```

After setting the github token:

```bash
❯ flux bootstrap github --owner=runyontr --repository=flux-namespace-overwrite --path=deploy
► connecting to github.com
✔ repository cloned
✚ generating manifests
✔ components manifests pushed
► installing components in flux-system namespace
namespace/flux-system created
customresourcedefinition.apiextensions.k8s.io/alerts.notification.toolkit.fluxcd.io created
customresourcedefinition.apiextensions.k8s.io/buckets.source.toolkit.fluxcd.io created
customresourcedefinition.apiextensions.k8s.io/gitrepositories.source.toolkit.fluxcd.io created
customresourcedefinition.apiextensions.k8s.io/helmcharts.source.toolkit.fluxcd.io created
customresourcedefinition.apiextensions.k8s.io/helmreleases.helm.toolkit.fluxcd.io created
customresourcedefinition.apiextensions.k8s.io/helmrepositories.source.toolkit.fluxcd.io created
customresourcedefinition.apiextensions.k8s.io/kustomizations.kustomize.toolkit.fluxcd.io created
customresourcedefinition.apiextensions.k8s.io/providers.notification.toolkit.fluxcd.io created
customresourcedefinition.apiextensions.k8s.io/receivers.notification.toolkit.fluxcd.io created
serviceaccount/helm-controller created
serviceaccount/kustomize-controller created
serviceaccount/notification-controller created
serviceaccount/source-controller created
clusterrole.rbac.authorization.k8s.io/crd-controller-flux-system created
clusterrolebinding.rbac.authorization.k8s.io/cluster-reconciler-flux-system created
clusterrolebinding.rbac.authorization.k8s.io/crd-controller-flux-system created
service/notification-controller created
service/source-controller created
service/webhook-receiver created
deployment.apps/helm-controller created
deployment.apps/kustomize-controller created
deployment.apps/notification-controller created
deployment.apps/source-controller created
networkpolicy.networking.k8s.io/allow-scraping created
networkpolicy.networking.k8s.io/allow-webhooks created
networkpolicy.networking.k8s.io/deny-ingress created
Waiting for deployment "source-controller" rollout to finish: 0 out of 1 new replicas have been updated...
Waiting for deployment "source-controller" rollout to finish: 0 of 1 updated replicas are available...
deployment "source-controller" successfully rolled out
Waiting for deployment "kustomize-controller" rollout to finish: 0 of 1 updated replicas are available...
deployment "kustomize-controller" successfully rolled out
deployment "helm-controller" successfully rolled out
Waiting for deployment "notification-controller" rollout to finish: 0 of 1 updated replicas are available...
deployment "notification-controller" successfully rolled out
✔ install completed
► configuring deploy key
✔ deploy key configured
► generating sync manifests
✔ sync manifests pushed
► applying sync manifests
◎ waiting for cluster sync
✔ bootstrap finished
```


Looking at the namespaces, the provided namespace is overwritten:

```bash
❯ kubectl get ns                
NAME              STATUS   AGE
kube-system       Active   10m
default           Active   10m
kube-public       Active   10m
kube-node-lease   Active   10m
flux-system       Active   65s
runyontr          Active   39s
```
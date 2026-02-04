# Kustomize Env Overlay

https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#bases-and-overlays


```
directory layout:

my-kustomize-project/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
├── overlays/
│   ├── dev/
│   │   ├── kustomization.yaml
│   │   └── patch-deployment.yaml
│   └── prod/
│       ├── kustomization.yaml
│       └── patch-deployment.yaml
```
```
base/kustomization.yaml

resources:
  - deployment.yaml
  - service.yaml
```
```
overlays/dev/kustomization.yaml

resources:
  - ../../base

patchesStrategicMerge:
  - patch-deployment.yaml

namePrefix: dev-
namespace: dev
```
```
overlays/prod/kustomization.yaml

resources:
  - ../../base

patchesStrategicMerge:
  - patch-deployment.yaml

namePrefix: prod-
namespace: prod
```
```
overlays/prod/patch-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  template:
    spec:
      containers:
        - name: my-app
          image: my-app:prod
          env:
            - name: ENV
              value: "production"
```
```
Apply with Kustomize:

# Apply dev
kubectl apply -k overlays/dev/

# Apply prod
kubectl apply -k overlays/prod/
```

```
controlplane:~/my-project$ k get pods -A
NAMESPACE            NAME                                      READY   STATUS    RESTARTS      AGE
dev                  dev-my-nginx-68f5946d6d-2gvrt             1/1     Running   0             5m50s
dev                  dev-my-nginx-68f5946d6d-j882v             1/1     Running   0             5m50s
dev                  dev-my-nginx-68f5946d6d-nch2w             1/1     Running   0             5m50s
kube-system          calico-kube-controllers-7bb4b4d4d-wxkp9   1/1     Running   1 (35m ago)   13d
kube-system          canal-vfr7w                               2/2     Running   2 (35m ago)   13d
kube-system          coredns-76bb9b6fb5-dhgw5                  1/1     Running   1 (35m ago)   13d
kube-system          coredns-76bb9b6fb5-ssjc6                  1/1     Running   1 (35m ago)   13d
kube-system          etcd-controlplane                         1/1     Running   1 (35m ago)   13d
kube-system          kube-apiserver-controlplane               1/1     Running   1 (35m ago)   13d
kube-system          kube-controller-manager-controlplane      1/1     Running   1 (35m ago)   13d
kube-system          kube-proxy-458zr                          1/1     Running   1 (35m ago)   13d
kube-system          kube-scheduler-controlplane               1/1     Running   1 (35m ago)   13d
local-path-storage   local-path-provisioner-76f88ddd78-7bchl   1/1     Running   1 (35m ago)   13d
prod                 prod-my-nginx-5d6b5cf7b4-bpmvb            1/1     Running   0             2m4s
prod                 prod-my-nginx-5d6b5cf7b4-glvhj            1/1     Running   0             2m4s
prod                 prod-my-nginx-5d6b5cf7b4-gw25c            1/1     Running   0             2m4s

```
# Scheduling Pod Affinity


That Pod should be preferred to be only scheduled on Nodes where Pods with label level=restricted are running.

For the topologyKey use kubernetes.io/hostname .

There are no taints on any Nodes which means no tolerations are needed.

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    level: hobby
  name: hobby-project
spec:
  containers:
  - image: nginx:alpine
    name: c
  affinity:
    podAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: level
              operator: In
              values:
              - restricted
          topologyKey: kubernetes.io/hostname
```

# Question 12 | Deployment on all Nodes

Solve this question on: ssh cka2556

Implement the following in Namespace project-tiger:

Create a Deployment named deploy-important with 3 replicas

The Deployment and its Pods should have label id=very-important

First container named container1 with image nginx:1-alpine

Second container named container2 with image google/pause

There should only ever be one Pod of that Deployment running on one worker node, use topologyKey: kubernetes.io/hostname for this

```
➜ ssh cka2556

➜ candidate@cka2556:~$ k -n project-tiger create deployment --image=nginx:1-alpine deploy-important --dry-run=client -o yaml > 12.yaml
```

```
# cka2556:/home/candidate/12.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    id: very-important                  # change
  name: deploy-important
  namespace: project-tiger              # important
spec:
  replicas: 3                           # change
  selector:
    matchLabels:
      id: very-important                # change
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        id: very-important              # change
    spec:
      containers:
      - image: nginx:1-alpine
        name: container1                # change
        resources: {}
      - image: google/pause             # add
        name: container2                # add
      affinity:                                             # add
        podAntiAffinity:                                    # add
          requiredDuringSchedulingIgnoredDuringExecution:   # add
          - labelSelector:                                  # add
              matchExpressions:                             # add
              - key: id                                     # add
                operator: In                                # add
                values:                                     # add
                - very-important                            # add
            topologyKey: kubernetes.io/hostname             # add
status: {}
```

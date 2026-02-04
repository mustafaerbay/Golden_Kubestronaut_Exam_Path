# Apply node affinity to a pod

## Create a pod with node affinity

In the namespace named 012963bd , create a pod named az1-pod which uses the nginx:1.24.0 image. This pod should use node affinity, and prefer during scheduling to be placed on the node with the label availability-zone=zone1 with a weight of 80.

Also, have that same pod prefer to be scheduled to a node with the label availability-zone=zone2 with a weight of 20.

NOTE: Make sure the container remains in a running state
Ensure that the pod is scheduled to the controlplane node.

```
k -n 012963bd run az1-pod --image nginx:1.24.0 --dry-run=client -o yaml > pod.yaml
```

```
controlplane:~$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: az1-pod
  name: az1-pod
  namespace: 012963bd
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/os
            operator: In
            values:
            - linux
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 80
        preference:
          matchExpressions:
          - key: availability-zone
            operator: In
            values:
            - zone1
      - weight: 20
        preference:
          matchExpressions:
          - key: availability-zone
            operator: In
            values:
            - zone2
  containers:
  - image: nginx:1.24.0
    name: az1-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
controlplane:~$ 
```

```
k apply -f pod.yaml
```


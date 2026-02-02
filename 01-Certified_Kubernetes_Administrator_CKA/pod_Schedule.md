# Scheduling a pod to a specific node

>In the namespace named 012963bd , create a pod named ctrl-pod which uses the nginx image. This pod should be scheduled to the control plane node.

Ensure that the pod is scheduled to the controlplane node.

```
# create the yaml template and save it to a file named 'pod.yaml'
k -n 012963bd run ctrl-pod --image nginx --dry-run=client -o yaml > pod.yaml


# open the pod.yaml file 'vim pod.yaml' and the contents should look similar to the following
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ctrl-pod
  name: ctrl-pod
  namespace: 012963bd
spec:
  containers:
  - image: nginx
    name: ctrl-pod
  nodeName: controlplane
```
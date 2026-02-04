# Remove the Taint from Node

> Run the following command in order to schedule a pod named nginx to the control plane node.

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
  nodeSelector:
    kubernetes.io/hostname: controlplane
EOF
```

## Schedule the Pod to the Control Plane
```
controlplane:~$ k describe no controlplane | grep -i taint
Taints:             node-role.kubernetes.io/control-plane:NoSchedule

```

```
controlplane:~$ # get the pod to run on the control plane by removing the taint
controlplane:~$ kubectl taint no controlplane node-role.kubernetes.io/control-plane:NoSchedule-
node/controlplane untainted
controlplane:~$ k describe no controlplane | grep -i taint
Taints:             <none>

```
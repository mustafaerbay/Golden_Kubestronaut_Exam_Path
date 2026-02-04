# Taints and Tolerations

## List the taints for node01

```
controlplane:~$ k describe no node01 | grep -i taints
Taints:             dedicated=special-user:NoSchedule
```

```
controlplane:~$ k get nodes -o yaml | grep taints -A 5 
    taints:
    - effect: NoSchedule
      key: node-role.kubernetes.io/control-plane
  status:
    addresses:
    - address: 172.30.1.2
--
    taints:
    - effect: NoSchedule
      key: dedicated
      value: special-user
  status:
    addresses:
```

## Apply the tolerations to the pod

https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/

```
controlplane:~$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
  tolerations:
  - key: "dedicated"
    operator: "Exists"
    effect: "NoSchedule"

```

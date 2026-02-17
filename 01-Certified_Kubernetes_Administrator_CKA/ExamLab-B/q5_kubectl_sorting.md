# Question 5 | Kubectl sorting
Solve this question on: ssh cka8448

 

Create two bash script files which use kubectl sorting to:

Write a command into /opt/course/5/find_pods.sh which lists all Pods in all Namespaces sorted by their AGE (metadata.creationTimestamp)

Write a command into /opt/course/5/find_pods_uid.sh which lists all Pods in all Namespaces sorted by field metadata.uid

```
# cka8448:/opt/course/5/find_pods.sh
kubectl get pod -A --sort-by=.metadata.creationTimestamp
```

```
# cka8448:/opt/course/5/find_pods_uid.sh
kubectl get pod -A --sort-by=.metadata.uid
```
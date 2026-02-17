# Question 3 | Scale down StatefulSet

Solve this question on: ssh cka3962

There are two Pods named o3db-* in Namespace project-h800. The Project H800 management asked you to scale these down to one replica to save resources.

```
➜ candidate@cka3962:~ k -n project-h800 scale sts o3db --replicas 1
statefulset.apps/o3db scaled

➜ candidate@cka3962:~ k -n project-h800 get sts o3db
NAME   READY   AGE
o3db   1/1     6d19h
```
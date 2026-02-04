# Rollback a Deployment

## Create Deployment
```
k create deploy apache --image httpd
```

## Change Deployment Image
```
k set image deploy/apache httpd=httpd httpd=httpd:2.4.54
deployment.apps/apache image updated
```

```
k describe rs
```
```
controlplane:~$ k describe rs
Name:           apache-6f9bf96cf5
Namespace:      default
Selector:       app=apache,pod-template-hash=6f9bf96cf5
Labels:         app=apache
                pod-template-hash=6f9bf96cf5
Annotations:    deployment.kubernetes.io/desired-replicas: 1
                deployment.kubernetes.io/max-replicas: 2
                deployment.kubernetes.io/revision: 2
Controlled By:  Deployment/apache
Replicas:       1 current / 1 desired
Pods Status:    1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=apache
           pod-template-hash=6f9bf96cf5
  Containers:
   httpd:
    Image:         httpd:2.4.54
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  25s   replicaset-controller  Created pod: apache-6f9bf96cf5-q62g9


Name:           apache-7b8c8c4b44
Namespace:      default
Selector:       app=apache,pod-template-hash=7b8c8c4b44
Labels:         app=apache
                pod-template-hash=7b8c8c4b44
Annotations:    deployment.kubernetes.io/desired-replicas: 1
                deployment.kubernetes.io/max-replicas: 2
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/apache
Replicas:       0 current / 0 desired
Pods Status:    0 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=apache
           pod-template-hash=7b8c8c4b44
  Containers:
   httpd:
    Image:         httpd
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  2m3s  replicaset-controller  Created pod: apache-7b8c8c4b44-kpn57
  Normal  SuccessfulDelete  17s   replicaset-controller  Deleted pod: apache-7b8c8c4b44-kpn57

```

## Rollback Deployment
```
# view the rollout history
kubectl rollout history deploy apache

# roll back to the previous rollout
kubectl rollout undo deploy apache

# view the status of the rollout
kubectl rollout status deploy apache
```
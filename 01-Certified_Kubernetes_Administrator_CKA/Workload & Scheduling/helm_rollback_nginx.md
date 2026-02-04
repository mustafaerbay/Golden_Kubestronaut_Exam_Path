# Rollback and Troubleshoot

>First, let's create a problematic configuration by upgrading with invalid settings:
```
helm upgrade my-nginx-ingress nginx-stable/nginx-ingress \
  --namespace nginx-ingress \
  --set controller.image.tag="invalid-tag" \
  --set controller.replicaCount=5
```

> Check the pod status - you should see some issues:
```
kubectl get pods -n nginx-ingress -w
```

>Press Ctrl+C to stop watching after observing the failing pods.

>View the release history to see all revisions:
```
helm history my-nginx-ingress -n nginx-ingress
```
>Rollback to the previous working revision (revision 3):
```
helm rollback my-nginx-ingress 3 -n nginx-ingress
```
>Check that the rollback was successful:
```
helm status my-nginx-ingress -n nginx-ingress
```
```
helm history my-nginx-ingress -n nginx-ingress

controlplane:~$ helm history my-nginx-ingress -n nginx-ingress
REVISION        UPDATED                         STATUS          CHART                   APP VERSION     DESCRIPTION     
1               Sun Feb  1 17:18:15 2026        superseded      nginx-ingress-2.4.2     5.3.2           Install complete
2               Sun Feb  1 17:31:26 2026        superseded      nginx-ingress-2.4.2     5.3.2           Upgrade complete
3               Sun Feb  1 17:32:09 2026        superseded      nginx-ingress-2.4.2     5.3.2           Upgrade complete
4               Sun Feb  1 17:34:23 2026        superseded      nginx-ingress-2.4.2     5.3.2           Upgrade complete
5               Sun Feb  1 17:37:59 2026        superseded      nginx-ingress-2.4.2     5.3.2           Upgrade complete
6               Sun Feb  1 17:38:54 2026        deployed        nginx-ingress-2.4.2     5.3.2           Rollback to 3   
controlplane:~$ 
```

> Verify pods are healthy again:
```
kubectl get pods -n nginx-ingress
Get values for the current release:

helm get values my-nginx-ingress -n nginx-ingress
Get all information about the release:

helm get all my-nginx-ingress -n nginx-ingress

```
# Upgrade the release
```
helm upgrade my-nginx-ingress nginx-stable/nginx-ingress \
  --namespace nginx-ingress \
  --values /root/nginx-values.yaml
```


> below command upgrades the service annotations as well

```
helm upgrade my-nginx-ingress nginx-stable/nginx-ingress \
  --namespace nginx-ingress \
  --values /root/nginx-values.yaml \
  --set controller.service.annotations."service\.kubernetes\.io/load-balancer-source-ranges"="10.0.0.0/8"
```

```
controlplane:~$ helm history my-nginx-ingress -n nginx-ingress
REVISION        UPDATED                         STATUS          CHART                   APP VERSION     DESCRIPTION     
1               Sun Feb  1 17:18:15 2026        superseded      nginx-ingress-2.4.2     5.3.2           Install complete
2               Sun Feb  1 17:31:26 2026        superseded      nginx-ingress-2.4.2     5.3.2           Upgrade complete
3               Sun Feb  1 17:32:09 2026        deployed        nginx-ingress-2.4.2     5.3.2           Upgrade complete

```
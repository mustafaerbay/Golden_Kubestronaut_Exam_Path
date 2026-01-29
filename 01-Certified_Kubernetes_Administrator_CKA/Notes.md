# Apiserver Crash

### CASE01: Apiserver has invalid arguments
- Check the logs under 

```
/var/log/container/
```

```
cat /var/log/syslog | grep apiserver
```

```
journalctl | grep apiserver
```

```
tail -f /var/log/pods/kube-system_kube-apiserver-controlplane_*/kube-apiserver/*.log
```
- update the manifest file under `/etc/kubernetes/manifests`
``` 
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```
----------

### CASE02: Kube Controller Manager Misconfigured

Check the pod log and yaml.
```
vi /etc/kubernetes/manifests/kube-controller-manager.yaml
```

-----------------
### CASE 03: Kubelet Misconfigured

```
service kubelet status
```


> Fix the ARG under /var/lib/kubelet
> ```
> vi /var/lib/kubelet/kubeadm-flags.env
>```


### CASE 04: Application Misconfigured

- crashloopbackoff -> Config map misconfigured
- pending -> nodeName definition wrong

### CASE 05: Application Multi Container Issue

- Get all the container logs from deployment

```
kubectl -n management logs --all-containers deploy/collect-data > /root/logs.log 
```

### CASE 06: Create configmaps

```
kubectl create configmap trauerweide --from-literal=tree=trauerweide
```

### CASE 07: ConfigMap Access in Pods

```
kubectl run pod1 --image=nginx:alpine --dry-run=client -o yaml >pod.yaml
```
and verify 

```
k exec pod1 -- ls /etc/birke
k exec pod1 -- env | grep -i tree
```

### CASE 08: Create ClusterIP for existing Deployments

```
k expose deployment asia --port=80
```

### CASE 09: Create Ingress for existing Services

```
k create ingress world --rule="world.universe.mine/europe=europe:80" --rule="world.universe.mine/asia=asia:80" --dry-run=client -o yaml > ingress.yaml
```

ingress.yml

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: world
  namespace: world
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: world.universe.mine
    http:
      paths:
      - backend:
          service:
            name: europe
            port:
              number: 80
        path: /europe
        pathType: Exact
      - backend:
          service:
            name: asia
            port:
              number: 80
        path: /asia
        pathType: Exact
status:
  loadBalancer: {}
```

### CASE 10: NetworkPolicy Namespace Selector

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np
  namespace: space1
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - to:
     - namespaceSelector:
        matchLabels:
         kubernetes.io/metadata.name: space2
  - ports:
    - port: 53
      protocol: TCP
    - port: 53
      protocol: UDP
```


```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np
  namespace: space2
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
   - from:
     - namespaceSelector:
        matchLabels:
         kubernetes.io/metadata.name: space1

```


verify:

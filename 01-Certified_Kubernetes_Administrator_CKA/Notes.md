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

Update the pathType: Prefix
```
sed -i "s|Exact|Prefix|g" ingress.yml
```

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
        pathType: Prefix
      - backend:
          service:
            name: asia
            port:
              number: 80
        path: /asia
        pathType: Prefix
status:
  loadBalancer: {}
```

### CASE 10: NetworkPolicy Namespace Selector

- Check the namespace labels first then use that label as namespaceSelector


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
```
# these should work
k -n space1 exec app1-0 -- curl -m 1 microservice1.space2.svc.cluster.local
k -n space1 exec app1-0 -- curl -m 1 microservice2.space2.svc.cluster.local
k -n space1 exec app1-0 -- nslookup tester.default.svc.cluster.local
k -n kube-system exec -it validate-checker-pod -- curl -m 1 app1.space1.svc.cluster.local

# these should not work
k -n space1 exec app1-0 -- curl -m 1 tester.default.svc.cluster.local
k -n kube-system exec -it validate-checker-pod -- curl -m 1 microservice1.space2.svc.cluster.local
k -n kube-system exec -it validate-checker-pod -- curl -m 1 microservice2.space2.svc.cluster.local
k -n default run nginx --image=nginx:1.21.5-alpine --restart=Never -i --rm  -- curl -m 1 microservice1.space2.svc.cluster.local

```



### CASE11: NetworkPolicy Misconfigured

- edit the network policy and veridy the solution

```
kubectl edit networkpolicy np-100x
```

verify:
```
kubectl exec tester-0 -- curl tester.level-1000.svc.cluster.local
kubectl exec tester-0 -- curl tester.level-1001.svc.cluster.local
kubectl exec tester-0 -- curl tester.level-1002.svc.cluster.local
```


### CASE12: RBAC ServiceAccount Permissions / Control ServiceAccount permissions using RBAC

There are existing Namespaces ns1 and ns2 .

Create ServiceAccount pipeline in both Namespaces.

These SAs should be allowed to view almost everything in the whole cluster. You can use the default ClusterRole view for this.

These SAs should be allowed to create and delete Deployments in their Namespace.

Verify everything using kubectl auth can-i .


>Let's talk a little about RBAC resources:

```
A ClusterRole|Role defines a set of permissions and where it is available, in the whole cluster or just a single Namespace.

A ClusterRoleBinding|RoleBinding connects a set of permissions with an account and defines where it is applied, in the whole cluster or just a single Namespace.

Because of this there are 4 different RBAC combinations and 3 valid ones:

Role + RoleBinding (available in single Namespace, applied in single Namespace)
ClusterRole + ClusterRoleBinding (available cluster-wide, applied cluster-wide)
ClusterRole + RoleBinding (available cluster-wide, applied in single Namespace)
Role + ClusterRoleBinding (NOT POSSIBLE: available in single Namespace, applied cluster-wide)
```
Solution
```
# create SAs
k -n ns1 create sa pipeline
k -n ns2 create sa pipeline

# use ClusterRole view
k get clusterrole view # there is default one
k create clusterrolebinding pipeline-view --clusterrole view --serviceaccount ns1:pipeline --serviceaccount ns2:pipeline

# manage Deployments in both Namespaces
k create clusterrole -h # examples
k create clusterrole pipeline-deployment-manager --verb create,delete --resource deployments
# instead of one ClusterRole we could also create the same Role in both Namespaces

k -n ns1 create rolebinding pipeline-deployment-manager --clusterrole pipeline-deployment-manager --serviceaccount ns1:pipeline
k -n ns2 create rolebinding pipeline-deployment-manager --clusterrole pipeline-deployment-manager --serviceaccount ns2:pipeline

Verify

# namespace ns1 deployment manager
k auth can-i delete deployments --as system:serviceaccount:ns1:pipeline -n ns1 # YES
k auth can-i create deployments --as system:serviceaccount:ns1:pipeline -n ns1 # YES
k auth can-i update deployments --as system:serviceaccount:ns1:pipeline -n ns1 # NO
k auth can-i update deployments --as system:serviceaccount:ns1:pipeline -n default # NO

# namespace ns2 deployment manager
k auth can-i delete deployments --as system:serviceaccount:ns2:pipeline -n ns2 # YES
k auth can-i create deployments --as system:serviceaccount:ns2:pipeline -n ns2 # YES
k auth can-i update deployments --as system:serviceaccount:ns2:pipeline -n ns2 # NO
k auth can-i update deployments --as system:serviceaccount:ns2:pipeline -n default # NO

# cluster wide view role
k auth can-i list deployments --as system:serviceaccount:ns1:pipeline -n ns1 # YES
k auth can-i list deployments --as system:serviceaccount:ns1:pipeline -A # YES
k auth can-i list pods --as system:serviceaccount:ns1:pipeline -A # YES
k auth can-i list pods --as system:serviceaccount:ns2:pipeline -A # YES
k auth can-i list secrets --as system:serviceaccount:ns2:pipeline -A # NO (default view-role doesn't allow)
```


### CASE13: RBAC USER PERMISSIONS

There is existing Namespace applications .

User smoke should be allowed to create and delete Pods, Deployments and StatefulSets in Namespace applications.
User smoke should have view permissions (like the permissions of the default ClusterRole named view ) in all Namespaces but not in kube-system .
Verify everything using kubectl auth can-i .

```
1) RBAC for Namespace applications


k -n applications create role smoke --verb create,delete --resource pods,deployments,sts
k -n applications create rolebinding smoke --role smoke --user smoke

⁣2) view permission in all Namespaces but not kube-system


As of now it’s not possible to create deny-RBAC in K8s

So we allow for all other Namespaces


k get ns # get all namespaces
k -n applications create rolebinding smoke-view --clusterrole view --user smoke
k -n default create rolebinding smoke-view --clusterrole view --user smoke
k -n kube-node-lease create rolebinding smoke-view --clusterrole view --user smoke
k -n kube-public create rolebinding smoke-view --clusterrole view --user smoke


Verify

# applications
k auth can-i create deployments --as smoke -n applications # YES
k auth can-i delete deployments --as smoke -n applications # YES
k auth can-i delete pods --as smoke -n applications # YES
k auth can-i delete sts --as smoke -n applications # YES
k auth can-i delete secrets --as smoke -n applications # NO
k auth can-i list deployments --as smoke -n applications # YES
k auth can-i list secrets --as smoke -n applications # NO
k auth can-i get secrets --as smoke -n applications # NO

# view in all namespaces but not kube-system
k auth can-i list pods --as smoke -n default # YES
k auth can-i list pods --as smoke -n applications # YES
k auth can-i list pods --as smoke -n kube-public # YES
k auth can-i list pods --as smoke -n kube-node-lease # YES
k auth can-i list pods --as smoke -n kube-system # NO
```


### CASE14: Scheduling Priority

Find Pod with highest priority

```
highest priority number means higher priority
```
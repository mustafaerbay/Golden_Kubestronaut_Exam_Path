# Question 1 | DNS / FQDN / Headless Service

Solve this question on: ssh cka6016


The Deployment controller in Namespace lima-control communicates with various cluster internal endpoints by using their DNS FQDN values.

Update the ConfigMap used by the Deployment with the correct FQDN values for:

DNS_1: Service kubernetes in Namespace default

DNS_2: Headless Service department in Namespace lima-workload

DNS_3: Pod section100 in Namespace lima-workload. It should work even if the Pod IP changes

DNS_4: A Pod with IP 1.2.3.4 in Namespace kube-system

Ensure the Deployment works with the updated values.

---
### ANSWER

#### STEP 1

```
DNS_1=kubernetes.default.svc.cluster.local
```

```
➜ / # nslookup kubernetes.default.svc.cluster.local
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   kubernetes.default.svc.cluster.local
Address: 10.96.0.1
```

#### STEP 2

```
➜ / # nslookup department.lima-workload.svc.cluster.local
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   department.lima-workload.svc.cluster.local
Address: 10.32.0.2
Name:   department.lima-workload.svc.cluster.local
Address: 10.32.0.9
```
The value for DNS_2 is department.lima-workload.svc.cluster.local. It is the same structure as before but what's interesting here is that we get two IP addresses. These are the IP addresses of the Pods behind that Service.

This is the case because the Service is headless and doesn't have its own IP address, but it still has Endpoints and points properly to Pods:
 
```
➜ candidate@cka6016:~$ k -n lima-workload get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
department   ClusterIP   None           <none>        80/TCP    2m19s
section      ClusterIP   10.99.121.17   <none>        80/TCP    2m18s
```
```
➜ candidate@cka6016:~$ k -n lima-workload get endpointslice
NAME               ADDRESSTYPE   PORTS   ENDPOINTS                   AGE
department-wqvvq   IPv4          80      10.32.0.2:80,10.32.0.9:80   2m19s
section-dtt9s      IPv4          80      10.32.0.10:80,10.32.0.3:80  2m19s
```
This means the decision which Pod IP to contact is now in the hands of the application which performed the DNS query of the headless Service.

#### STEP 3
```
➜ / # nslookup section100.section.lima-workload.svc.cluster.local
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   section100.section.lima-workload.svc.cluster.local
Address: 10.32.0.10

➜ / # nslookup section200.section.lima-workload.svc.cluster.local
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   section200.section.lima-workload.svc.cluster.local
Address: 10.32.0.3
```
Hence the value for DNS_3 is section100.section.lima-workload.svc.cluster.local.

But this is only possible because the Pods behind the Service specify hostname and subdomain like this:
```
# kubectl -n lima-workload edit pod section100
apiVersion: v1
kind: Pod
metadata:
  name: section100
  namespace: lima-workload
  labels:
    name: section
spec:
  hostname: section100  # set hostname
  subdomain: section    # set subdomain to same name as service
  containers:
    - image: httpd:2-alpine
      name: pod
...
```
#### STEP 4

```
➜ / # nslookup 1-2-3-4.kube-system.pod.cluster.local
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   1-2-3-4.kube-system.pod.cluster.local
Address: 1.2.3.4
```
```
➜ candidate@cka6016:~$ $ k -n lima-control get cm                
NAME               DATA   AGE
control-config     4      10m

➜ candidate@cka6016:~$ k -n lima-control edit cm control-config
```
```
apiVersion: v1
data:
  DNS_1: kubernetes.default.svc.cluster.local                  # UPDATE
  DNS_2: department.lima-workload.svc.cluster.local            # UPDATE
  DNS_3: section100.section.lima-workload.svc.cluster.local    # UPDATE
  DNS_4: 1-2-3-4.kube-system.pod.cluster.local                 # UPDATE
kind: ConfigMap
metadata:
  name: control-config
  namespace: lima-control
...
```

```
➜ candidate@cka6016:~$ kubectl -n lima-control rollout restart deploy controller
deployment.apps/controller restarted
```
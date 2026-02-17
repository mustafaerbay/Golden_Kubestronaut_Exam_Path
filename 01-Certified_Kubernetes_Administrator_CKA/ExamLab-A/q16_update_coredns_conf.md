# Question 16 | Update CoreDNS Configuration

Solve this question on: ssh cka5774

 

The CoreDNS configuration in the cluster needs to be updated:

Make a backup of the existing configuration Yaml and store it at /opt/course/16/coredns_backup.yaml. You should be able to fast recover from the backup

Update the CoreDNS configuration in the cluster so that DNS resolution for SERVICE.NAMESPACE.custom-domain will work exactly like and in addition to SERVICE.NAMESPACE.cluster.local

Test your configuration for example from a Pod with busybox:1 image. These commands should result in an IP address:

nslookup kubernetes.default.svc.cluster.local
nslookup kubernetes.default.svc.custom-domain

---
1.

```
➜ candidate@cka5774:~$ k -n kube-system get cm
NAME                                                   DATA   AGE
coredns                                                1      42h
...

➜ candidate@cka5774:~$ k -n kube-system get cm coredns -oyaml > /opt/course/16/coredns_backup.yaml
```

```
apiVersion: v1
data:
  Corefile: |
    .:53 {
        errors
        health {
           lameduck 5s
        }
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
           ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf {
           max_concurrent 1000
        }
        cache 30 {
           disable success cluster.local
           disable denial cluster.local
        }
        loop
        reload
        loadbalance
    }
kind: ConfigMap
metadata:
  name: coredns
  namespace: kube-system
...
```

2.
update the domain

```
➜ candidate@cka5774:~$ k -n kube-system edit cm coredns
apiVersion: v1
data:
  Corefile: |
    .:53 {
        errors
        health {
           lameduck 5s
        }
        ready
        kubernetes custom-domain cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
           ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf {
           max_concurrent 1000
        }
        cache 30 {
           disable success cluster.local
           disable denial cluster.local
        }
        loop
        reload
        loadbalance
    }
kind: ConfigMap
metadata:
  creationTimestamp: "2024-12-26T20:35:11Z"
  name: coredns
  namespace: kube-system
  resourceVersion: "262"
  uid: c76d208f-1bc8-4c0f-a8e8-a8bfa440870e
```

```
➜ candidate@cka5774:~$ k -n kube-system rollout restart deploy coredns
deployment.apps/coredns restarted

➜ candidate@cka5774:~$ k -n kube-system get pod
NAME                              READY   STATUS    RESTARTS      AGE
coredns-77d6976b98-jkvqn          1/1     Running   0             13s
coredns-77d6976b98-zdxw8          1/1     Running   0             13s
...
```

test:
```
➜ candidate@cka5774:~$ k run bb --image=busybox:1 -- sh -c 'sleep 1d'

➜ candidate@cka5774:~$ k exec -it bb -- sh

➜ / # nslookup kubernetes.default.svc.custom-domain
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   kubernetes.default.svc.custom-domain
Address: 10.96.0.1

➜ / # nslookup kubernetes.default.svc.cluster.local
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   kubernetes.default.svc.cluster.local
Address: 10.96.0.1
```

# List the Pods and Their IP Addresses

```
controlplane:~$ kubectl -n kube-system get po -o wide > pod-ip-output.txt
controlplane:~$ cat pod-ip-output.txt
NAME                                      READY   STATUS    RESTARTS        AGE   IP            NODE           NOMINATED NODE   READINESS GATES
calico-kube-controllers-7bb4b4d4d-wxkp9   1/1     Running   1 (4m40s ago)   12d   192.168.0.3   controlplane   <none>           <none>
canal-vfr7w                               2/2     Running   2 (4m40s ago)   12d   172.30.1.2    controlplane   <none>           <none>
coredns-76bb9b6fb5-dhgw5                  1/1     Running   1 (4m40s ago)   12d   192.168.0.5   controlplane   <none>           <none>
coredns-76bb9b6fb5-ssjc6                  1/1     Running   1 (4m40s ago)   12d   192.168.0.4   controlplane   <none>           <none>
etcd-controlplane                         1/1     Running   1 (4m40s ago)   12d   172.30.1.2    controlplane   <none>           <none>
kube-apiserver-controlplane               1/1     Running   1 (4m40s ago)   12d   172.30.1.2    controlplane   <none>           <none>
kube-controller-manager-controlplane      1/1     Running   1 (4m40s ago)   12d   172.30.1.2    controlplane   <none>           <none>
kube-proxy-458zr                          1/1     Running   1 (4m40s ago)   12d   172.30.1.2    controlplane   <none>           <none>
kube-scheduler-controlplane               1/1     Running   1 (4m40s ago)   12d   172.30.1.2    controlplane   <none>           <none>

```
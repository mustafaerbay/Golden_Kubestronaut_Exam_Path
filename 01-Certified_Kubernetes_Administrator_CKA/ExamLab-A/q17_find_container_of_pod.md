# Question 17 | Find Container of Pod and check info
Solve this question on: ssh cka2556

In Namespace project-tiger create a Pod named tigers-reunite of image httpd:2-alpine with labels pod=container and container=pod. Find out on which node the Pod is scheduled. Ssh into that node and find the containerd container belonging to that Pod.

Using command crictl:

Write the ID of the container and the info.runtimeType into /opt/course/17/pod-container.txt

Write the logs of the container into /opt/course/17/pod-container.log

```
➜ ssh cka2556

➜ candidate@cka2556:~$ k -n project-tiger run tigers-reunite --image=httpd:2-alpine --labels "pod=container,container=pod"
pod/tigers-reunite created
```

1.

```
➜ root@cka2556-node1:~# crictl inspect ba62e5d465ff0 | grep runtimeType
    "runtimeType": "io.containerd.runc.v2",
```
```
# cka2556:/opt/course/17/pod-container.txt
ba62e5d465ff0 io.containerd.runc.v2
```

2.

```
➜ root@cka2556-node1:~# crictl logs ba62e5d465ff0

# cka2556:/opt/course/17/pod-container.log
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.44.0.37. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.44.0.37. Set the 'ServerName' directive globally to suppress this message
[Mon Sep 13 13:32:18.555280 2021] [mpm_event:notice] [pid 1:tid 139929534545224] AH00489: Apache/2.4.41 (Unix) configured -- resuming normal operations
[Mon Sep 13 13:32:18.555610 2021] [core:notice] [pid 1:tid 139929534545224] AH00094: Command line: 'httpd -D FOREGROUND'
```
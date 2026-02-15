# upgrade node

We need to take node01 out for maintenance. Empty the node of all applications and mark it unschedulable.

```

controlplane ~ ✖ k drain node01 --ignore-daemonsets
node/node01 already cordoned
Warning: ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-8vdfw, kube-system/kube-proxy-2th6f
evicting pod default/blue-759779556-bt9z9
evicting pod default/blue-759779556-7jzgv
pod/blue-759779556-bt9z9 evicted
pod/blue-759779556-7jzgv evicted
node/node01 drained
```


make it schedulable after os upgrade
```

controlplane ~ ➜  k uncordon node01
node/node01 uncordoned

```
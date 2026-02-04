# Cluster Upgrade

## Upgrade Controlplane

Upgrade the controlplane Node to a newer patch version.

Also upgrade kubectl and kubelet .

```
# see possible versions
kubeadm upgrade plan

# show available versions
apt-cache show kubeadm

# can be different for you
apt-get install kubeadm=1.34.3-1.1

# could be a different version for you, it can also take a bit to finish!
kubeadm upgrade apply v1.34.3
```

```
# can be a different version for you
apt-get install kubectl=1.34.3-1.1 kubelet=1.34.3-1.1

service kubelet restart
```

## Upgrade Worker Node

```
apt-get install kubeadm=1.34.3-1.1
kubeadm upgrade node
```

```
apt-get install kubelet=1.34.3-1.1

service kubelet restart
```

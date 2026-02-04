# Cluster Setup

## Install Controlplane

There are two VMs controlplane and node-summer , create a two Node Kubeadm Kubernetes cluster with them.

Kubeadm, kubelet and kubectl are already installed.

First make controlplane the controlplane. For this we should:

Use Kubernetes version v1.34.1
Use a Pod-Network-Cidr of 192.168.0.0/16
Ignore preflight errors for NumCPU and Mem
Copy the default kubectl admin.conf to /root/.kube/config

```
kubeadm init --kubernetes-version=1.34.1 --pod-network-cidr 192.168.0.0/16 --ignore-preflight-errors=NumCPU --ignore-preflight-errors=Mem

cp /etc/kubernetes/admin.conf /root/.kube/config

kubectl version

kubectl get pod -A
```

## Add Worker Node

```
kubeadm token create --print-join-command
```

```
kubeadm join 172.30.1.2:6443 --token t5xera.xz0d8h07l4g58ohx \
        --discovery-token-ca-cert-hash sha256:e0c74237052c5529f1598b1cca6b2c814875ae951f8114e849fefe44dd0d8486
```


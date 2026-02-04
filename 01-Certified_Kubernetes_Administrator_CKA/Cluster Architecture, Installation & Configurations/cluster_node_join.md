# Cluster Node Join

> Join Node using Kubeadm

```
kubeadm token create --print-join-command
```

```
kubeadm join 172.30.1.2:6443 --token 149l9e.q5i47tqdhe3xeh8w --discovery-token-ca-cert-hash sha256:7c0d0a78ebc5a980dfc06ba2d292c14daf3210b3f460e431951f6d58e626bfb6
```
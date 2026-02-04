# Kubelet Misconfigured

>check kubelet service status
```
service kubelet status
```


> Fix the ARG under /var/lib/kubelet
> ```
> vi /var/lib/kubelet/kubeadm-flags.env
>```
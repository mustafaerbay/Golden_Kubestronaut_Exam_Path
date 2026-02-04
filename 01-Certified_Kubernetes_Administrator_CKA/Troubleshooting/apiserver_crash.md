# Apiserver Crash

## Apiserver has invalid arguments
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
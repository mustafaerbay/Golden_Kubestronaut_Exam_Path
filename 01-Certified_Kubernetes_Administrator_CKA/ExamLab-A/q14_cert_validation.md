# Question 14 | Check how long certificates are valid

Solve this question on: ssh cka9412

Perform some tasks on cluster certificates:

Check how long the kube-apiserver server certificate is valid using openssl or cfssl. Write the expiration date into /opt/course/14/expiration. Run the kubeadm command to list the expiration dates and confirm both methods show the same one

Write the kubeadm command that would renew the kube-apiserver certificate into /opt/course/14/kubeadm-renew-certs.sh
---

### Answer:
```
➜ root@cka9412:~# openssl x509 -noout -text -in /etc/kubernetes/pki/apiserver.crt | grep Validity -A2
        Validity
            Not Before: Oct 29 14:14:27 2024 GMT
            Not After : Oct 29 14:19:27 2025 GMT
```

```
# cka9412:/opt/course/14/expiration
Oct 29 14:19:27 2025 GMT
```

```
➜ root@cka9412:~# kubeadm certs check-expiration | grep apiserver
apiserver                  Oct 29, 2025 14:19 UTC   356d    ca         no      
apiserver-etcd-client      Oct 29, 2025 14:19 UTC   356d    etcd-ca    no      
apiserver-kubelet-client   Oct 29, 2025 14:19 UTC   356d    ca         no 
```

```
# cka9412:/opt/course/14/kubeadm-renew-certs.sh
kubeadm certs renew apiserver
```
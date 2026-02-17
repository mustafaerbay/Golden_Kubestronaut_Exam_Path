# Question 3 | Kubelet client/server cert info

Solve this question on: ssh cka5248

Node cka5248-node1 has been added to the cluster using kubeadm and TLS bootstrapping.

Find the Issuer and Extended Key Usage values on cka5248-node1 for:

Kubelet Client Certificate, the one used for outgoing connections to the kube-apiserver

Kubelet Server Certificate, the one used for incoming connections from the kube-apiserver

Write the information into file /opt/course/3/certificate-info.txt.

---
### ANSWER

client cert
```
➜ root@cka5248-node1:~# openssl x509 -noout -text -in /var/lib/kubelet/pki/kubelet-client-current.pem | grep Issuer
        Issuer: CN = kubernetes
        
➜ root@cka5248-node1:~# openssl x509 -noout -text -in /var/lib/kubelet/pki/kubelet-client-current.pem | grep "Extended Key Usage" -A1
            X509v3 Extended Key Usage: 
                TLS Web Client Authentication
```
server cert
```
➜ root@cka5248-node1:~# openssl x509 -noout -text -in /var/lib/kubelet/pki/kubelet.crt | grep Issuer
        Issuer: CN = cka5248-node1-ca@1730211854

➜ root@cka5248-node1:~# openssl x509 -noout -text -in /var/lib/kubelet/pki/kubelet.crt | grep "Extended Key Usage" -A1
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication
```

We see that the server certificate was generated on the worker node itself and the client certificate was issued by the Kubernetes api. The Extended Key Usage also shows if it's for client or server authentication.

The solution file should look something like this:

```
# cka5248:/opt/course/3/certificate-info.txt
Issuer: CN = kubernetes
X509v3 Extended Key Usage: TLS Web Client Authentication
Issuer: CN = cka5248-node1-ca@1730211854
X509v3 Extended Key Usage: TLS Web Server Authentication
```
# Update API Server ServiceCIDR

>Change the service cluster IP range (serviceCIDR) that's given to each service to 100.96.0.0/12 so the API server hands out the new range before we recreate the cluster services.

```
vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

# Recreate Core Services in New CIDR

>With the API server now serving the new serviceCIDR, change the IP address associated with the cluster's DNS Service by deleting and re-creating the system services so they repopulate inside the new CIDR range.
The kube-dns (CoreDNS) service is the canonical discovery endpoint for every pod in the cluster; any mismatch between its ClusterIP and the DNS IPs pushed to pods will break name resolution. Recreating the kube-dns and kubernetes services guarantees they receive fresh IPs from the new CIDR before workloads pick up the updated DNS value.

>The servicecidrs.networking.k8s.io object stores the currently allocated service IP range so that components such as the API server and admission chain have a canonical source of truth. Removing it after you edit the API server manifest forces Kubernetes to recreate the resource with the new CIDR; otherwise, the legacy CIDR would continue to be advertised and the replacement services would fail to receive addresses inside the updated range.



```
# delete the existing DNS service that still points to the old CIDR
kubectl -n kube-system delete svc kube-dns
# remove the default 'kubernetes' service that was handed the old ClusterIP
kubectl -n default delete svc kubernetes
# delete the stored serviceCIDR so the apiserver repopulates it with the new range
kubectl delete servicecidrs kubernetes
# create the kube-dns service (CoreDNS) so it also lands in the new CIDR and points to the apiserver
cat <<'EOF' | kubectl create -f -
apiVersion: v1
kind: Service
metadata:
  labels:
    k8s-app: kube-dns
    kubernetes.io/cluster-service: "true"
    kubernetes.io/name: CoreDNS
  name: kube-dns
  namespace: kube-system
spec:
  ports:
  - name: dns
    port: 53
    protocol: UDP
    targetPort: 53
  - name: dns-tcp
    port: 53
    protocol: TCP
    targetPort: 53
  - name: metrics
    port: 9153
    protocol: TCP
    targetPort: 9153
  selector:
    k8s-app: kube-dns
  type: ClusterIP
EOF
```


```
# capture the new default service IP and the control-plane node IP to add them as Subject Alternative Names on the kube-apiserver cert
NEW_SERVICE_IP=$(kubectl -n default get svc kubernetes -o jsonpath='{.spec.clusterIP}')
# substitute the actual advertise address used in /etc/kubernetes/manifests/kube-apiserver.yaml (e.g., 172.30.1.2)
APISERVER_IP=$(grep -oP '(?<=--advertise-address=)[^\", ]+' /etc/kubernetes/manifests/kube-apiserver.yaml | head -n1)


# backup the existing apiserver certificate/key so kubeadm can write new files
cp /etc/kubernetes/pki/apiserver.crt /etc/kubernetes/pki/apiserver.crt.bak
cp /etc/kubernetes/pki/apiserver.key /etc/kubernetes/pki/apiserver.key.bak
sudo rm /etc/kubernetes/pki/apiserver.crt /etc/kubernetes/pki/apiserver.key


# reissue the apiserver certificate with the current advertise address, the legacy service IP, and the new service IP
kubeadm init phase certs apiserver \
  --apiserver-advertise-address="${APISERVER_IP}" \
  --apiserver-cert-extra-sans="${APISERVER_IP},10.96.0.1,${NEW_SERVICE_IP}"

```

>Regenerating the apiserver.crt file ensures the certificate is valid for the freshly issued ClusterIP (for example 100.96.0.1). Include any other SANs currently configured (such as the node IP or 10.96.0.1 for backward compatibility) so existing components continue to trust the API server. Without this step, components that contact the API via kubernetes.default —such as your CNI plugin—will fail TLS verification with errors like certificate is valid for 10.96.0.1 ... not 100.96.0.1 . The static pod automatically restarts when the certificate changes; you can watch kubectl get pods -n kube-system until the apiserver comes back to Running .


```
# restart Canal so the CNI plugin loads the new kubernetes.default service IP
kubectl -n kube-system rollout restart daemonset/canal

```

>Network plugins read KUBERNETES_SERVICE_HOST and KUBERNETES_SERVICE_PORT once per pod startup. Restarting the Canal daemonset ensures it reaches the API server via 100.96.0.1 instead of the old 10.96.0.1 IP, preventing sandbox creation errors such as failed to setup network ... dial tcp 10.96.0.1:443: i/o timeout . If the pod reports a TLS error, confirm /etc/kubernetes/pki/apiserver.crt contains the 100.96.0.1 SAN via openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text | grep 100.96.0.1.

```
# restart CoreDNS so it re-establishes watches against the API server using the refreshed ClusterIP
kubectl -n kube-system rollout restart deployment/coredns

```

>CoreDNS talks to the Kubernetes API via kubernetes.default.svc . After the serviceCIDR change and certificate rotation, restarting the deployment forces CoreDNS to reconnect with the new SAN/IP combo; otherwise, pods may see timeouts when querying 100.96.0.10 .

# Propagate New DNS IP to Kubelets
>With the serviceCIDR rotated and the core services recreated, update every kubelet so new pods use the DNS ClusterIP (100.96.0.10 ) coming from the fresh CIDR. Perform the change to the config.yaml on the node where pods run.

Then, edit the kubelet configMap in the kube-system namespace to use DNS 100.96.0.10. Reload the kubelet configuration without restarting the node.

```

# modify the kubelet config on the node
vim /var/lib/kubelet/config.yaml 

# within the config.yaml file, change the clusterDNS value to 100.96.0.10
...
cgroupDriver: systemd
clusterDNS:
- 100.96.0.10
clusterDomain: cluster.local
...
# edit kubelet configMap with 
k -n kube-system edit cm 
kubelet-config
# in the kubelet configMap, change the value for clusterDNS to 100.96.0.10

...
data:
  kubelet: |
    apiVersion: kubelet.config.k8s.io/v1beta1
    authentication:
      anonymous:
        enabled: false
      webhook:
        cacheTTL: 0s
        enabled: true
      x509:
        clientCAFile: /etc/kubernetes/pki/ca.crt
    authorization:
      mode: Webhook
      webhook:
        cacheAuthorizedTTL: 0s
        cacheUnauthorizedTTL: 0s
    cgroupDriver: systemd
    clusterDNS:
    - 100.96.0.10
    clusterDomain: cluster.local
...


# apply the update to the kubelet configuration immediately on the node
kubeadm upgrade node phase kubelet-config
systemctl daemon-reload
systemctl restart kubelet
```

>NOTE: Reloading and restarting the kubelet after the kubeadm upgrade node phase kubelet-config command guarantees the kubelet consumes the DNS IP that now lives inside the new serviceCIDR.



# Verify Pods Resolve Through New DNS

>Start a pod named 'netshoot' which uses the image `nicolaka/netshoot'. Ensure that the pod will stay in a running state.

Get a shell to the pod and cat the /etc/resolv.conf to confirm it is using the new DNS ClusterIP (100.96.0.10) that now falls inside the updated serviceCIDR. Use the nslookup tool to verify external name resolution (example.com) through the rebuilt DNS service.


```

# start a pod named 'netshoot' using the image 'nicolaka/netshoot' ensuring that the pod stays in a running state.
kubectl run netshoot --image=nicolaka/netshoot --command sleep --command "3600"
# get a shell to the running container named 'netshoot'
k exec -it netshoot -- bash

# cat the /etc/resolv.conf
cat /etc/resolv.conf
# run nslookup on example.com
nslookup example.com

```

>If lookups still time out, confirm the CoreDNS pods are running and Ready after the restart from Step 2: kubectl -n kube-system get pods -l k8s-app=kube-dns . Review their logs for TLS errors against https://kubernetes.default.svc and rerun the deployment/coredns restart once the API server certificate has the new service IP in its SAN list.
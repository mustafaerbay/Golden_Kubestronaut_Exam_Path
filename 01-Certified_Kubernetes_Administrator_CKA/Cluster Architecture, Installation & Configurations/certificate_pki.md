# Kubernetes PKI Essentials
Key takeaways:

- /etc/kubernetes/pki contains the crown jewels of a kubeadm control plane’s security.
- Certificates and keys here allow the API server, controller-manager, scheduler, and kubelet to trust and talk to each other.
- The API server is a static Pod; kubelet continuously tries to restart it if something goes wrong.
- admin.conf references the CA and client cert/key, letting kubectl authenticate.
Next time you troubleshoot API server issues, remember:
Check PKI before you panic.



```
controlplane:~$ ls -l /etc/kubernetes/pki/
total 60
-rw-r--r-- 1 root root 1123 Jan 21 15:06 apiserver-etcd-client.crt
-rw------- 1 root root 1675 Jan 21 15:06 apiserver-etcd-client.key
-rw-r--r-- 1 root root 1176 Jan 21 15:06 apiserver-kubelet-client.crt
-rw------- 1 root root 1679 Jan 21 15:06 apiserver-kubelet-client.key
-rw-r--r-- 1 root root 1289 Jan 21 15:06 apiserver.crt
-rw------- 1 root root 1675 Jan 21 15:06 apiserver.key
-rw-r--r-- 1 root root 1107 Jan 21 15:06 ca.crt
-rw------- 1 root root 1679 Jan 21 15:06 ca.key
drwxr-xr-x 2 root root 4096 Jan 21 15:06 etcd
-rw-r--r-- 1 root root 1123 Jan 21 15:06 front-proxy-ca.crt
-rw------- 1 root root 1675 Jan 21 15:06 front-proxy-ca.key
-rw-r--r-- 1 root root 1119 Jan 21 15:06 front-proxy-client.crt
-rw------- 1 root root 1675 Jan 21 15:06 front-proxy-client.key
-rw------- 1 root root 1675 Jan 21 15:06 sa.key
-rw------- 1 root root  451 Jan 21 15:06 sa.pub
```

```
controlplane:~$ sudo openssl x509 -in /etc/kubernetes/pki/apiserver.crt -noout -text | egrep 'Subject:|Issuer:|DNS:|IP Address:'
        Issuer: CN = kubernetes
        Subject: CN = kube-apiserver
                DNS:controlplane, DNS:kubernetes, DNS:kubernetes.default, DNS:kubernetes.default.svc, DNS:kubernetes.default.svc.cluster.local, IP Address:10.96.0.1, IP Address:172.30.1.2

```

```
controlplane:~$ sudo openssl x509 -in /etc/kubernetes/pki/ca.crt -noout -text | egrep 'Subject:|Issuer:'
        Issuer: CN = kubernetes
        Subject: CN = kubernetes

```

```
controlplane:~$ sudo egrep -n 'certificate-authority|client-certificate|client-key' /etc/kubernetes/admin.conf
4:    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJSCtLWjdmUmZTU3N3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TmpBeE1qRXhOVEF3TlRkYUZ3MHpOakF4TVRreE5UQTFOVGRhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURXWHZ3cWFGZjlEMGtDNHcxQXgxMGp1ZUVSa1FQUGY3SDd5a29NQmc0b3ZUVWMxNERaMStFaHBmUlkKQ1NlcGtyVE5Ub0xNckNnRS8zYkRZUVRiTHBOT2JXQSs2bEhrQUdYbFl5QmJVL2JmTmV6WGlqS3ViSjFyQ09BWApPT28zU09CS2lPUk1MTmsyMW00UDhjRllnRmNPWWx2NTZXU3I3TmpjaGhjdjQzUEdtL01SYk9xS2sycjZoeXJ6CmxTMkZ6aXk1T0N5Z3h0MXhESkYzSDJnMU94TnJ4dU9Pdm9rUUkrdFdZTExOZ2N5aUE1SmsyUWpnSXRZbDY1eisKb2NXQ2lRYnlzajI0dnZYRmRjV05yd09NelRyUkRwMThLSGNZQnZkK2V4UktnTis3YkFJaHgwSXFiOTRpZHlsbgpDZVdGcmgwaFhoT3hURWZUbnRjWnFBelg4MnpwQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJRWUd2aGRENU9talNBOXorK0xQM1poZEliYWt6QVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQUkrU1FnWjNxaAp3M2RFVjBQSk9IM2Rzd09Lekh3eUFGRDJOVkRYZFErbGFsOENtMGVzaFVSRzRxQTk5ckhZWEV5WlN6SDVpYWVLCm1RNlJRUjdVcktPdmpNRFlXcHZWeTh2Yk51bG1QckcrUVRiSEh4T3NEcVNoV2w4OGtpcERTQlExQWc1MDJaa28KanhnaEZCVFlRR09MQlMwckdJckswaVNyRXNiYlpmTEh2Q3JZeWhGSmJZelBGQnNoTnpGNmczNHpLVklvQTlSVwo0UlNXSTBaT3lOQ3Q2dDFnVzJSZUVleVQxU2xaNk1kOTZYUHJuYmlNN3JXN1ZUUWNtdWQ0V1ZCc0MwLzF5RFpICm1uMGFxQnJwZGZuVzk5R05zOHBWR0UyQ2dQMEJrbVlMZHhEZDRmRkZaMjFiVTlaajNIUDR4eWo0cXljV1ZyTnAKTVdmY2htZEhlMlkzCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
17:    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURLVENDQWhHZ0F3SUJBZ0lJWDk5enVkY3BkbUl3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TmpBeE1qRXhOVEF3TlRkYUZ3MHlOekF4TWpFeE5UQTFOVGRhTUR3eApIekFkQmdOVkJBb1RGbXQxWW1WaFpHMDZZMngxYzNSbGNpMWhaRzFwYm5NeEdUQVhCZ05WQkFNVEVHdDFZbVZ5CmJtVjBaWE10WVdSdGFXNHdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCQVFDL2lSQnQKb3NMRTJRbjRJczNpTHhQMGF4ZDltUXdtSy94aU4wdHRvRjRjS2VJbTRxSVVHMXlRQjNkL29sK010cDNKOGl3aApvajc3TCtXcHVBMzg1ditNUGVsdmtCbDVNc2hUMHNjbVgrbWZBNUs3ZFRQWjZtdXVPbUFvbURlSzlsUDhEQkRTCjREYU5sdmFPS0hQay9xYVVWZmI5NTRIZGtwYTg5QTZGdzhOSzdlM2RvajNSc3BOUXN5UHpyeHVUYmQ0Z2NZVUIKQ0hSbXhFNHNhaWVucExEK3ZKWU93MWh5TGZ3eEFZaVJjWnFWMVJ5Zlg2WktQaHVMYW5TRWZmNXZWcEZRcE5DSwpXMU5sYk5XOWsrNjJ4YXRwVGExTk1RaVFoZHBUL3FLaTZHUmh3bkdWN2krWm5yZENDUXdSUFlocGYwSzVBdEFnCkVXazVIZVNUL2RPM3MySExBZ01CQUFHalZqQlVNQTRHQTFVZER3RUIvd1FFQXdJRm9EQVRCZ05WSFNVRUREQUsKQmdnckJnRUZCUWNEQWpBTUJnTlZIUk1CQWY4RUFqQUFNQjhHQTFVZEl3UVlNQmFBRkJnYStGMFBrNmFOSUQzUAo3NHMvZG1GMGh0cVRNQTBHQ1NxR1NJYjNEUUVCQ3dVQUE0SUJBUUFNOHVJaHZ2Tis5TEZaUUozWjdMOWNZWjNtCms0UExCRnl3a1FNQXRCV0o3SjZqTysveGZhalZvM2g3UGJSdmxSZktNeGcwWHVCNkQ5a1hDdE11cXpZRWZGQmEKVStmMkY0MEh1aHVpNTh3YmJGYU94YVg4T0w2ZzdiaVV5VGpsb25DRFBNYVA4Y0ZCWHMvdW5XRE5EVCtGaDlaRwprUHo5SWhkUUFlUk82ODU1T2JMbGZIejRyS3dFVkppWjJlT2ZmQWJmRzI1ME9HZzkxbFg0WEdmSmdWeFJhNjN1ClQ4L0JPRUxqcmdRSWdaRFpZQjVsN1hhWVY3USsxbWcyalFLVjEyT1FMS1dQSTNIMnYxdHdBZjdrNHFLYXZXMUUKaFp4QktickNqQTlRdzlUMzR6L1RRelk5U1orNERaT29UYVRzZXBLeXpNS1VqOWlJVUE3RDE4RnVlWlN0Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
18:    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBdjRrUWJhTEN4TmtKK0NMTjRpOFQ5R3NYZlprTUppdjhZamRMYmFCZUhDbmlKdUtpCkZCdGNrQWQzZjZKZmpMYWR5ZklzSWFJKyt5L2xxYmdOL09iL2pEM3BiNUFaZVRMSVU5TEhKbC9wbndPU3UzVXoKMmVwcnJqcGdLSmczaXZaVC9Bd1EwdUEyalpiMmppaHo1UDZtbEZYMi9lZUIzWktXdlBRT2hjUERTdTN0M2FJOQowYktUVUxNajg2OGJrMjNlSUhHRkFRaDBac1JPTEdvbnA2U3cvcnlXRHNOWWNpMzhNUUdJa1hHYWxkVWNuMSttClNqNGJpMnAwaEgzK2IxYVJVS1RRaWx0VFpXelZ2WlB1dHNXcmFVMnRUVEVJa0lYYVUvNmlvdWhrWWNKeGxlNHYKbVo2M1Fna01FVDJJYVg5Q3VRTFFJQkZwT1Iza2svM1R0N05oeXdJREFRQUJBb0lCQUJmTG9YemRjYkVlL2J3VQpKdHZvMldQT1FqYmFZc1lEUlBHMnlPb3Z1RUZyZXFzMkVNakt0VzgzWGtNL0d2dlNYRHdRbUNiOWp6R0p1OUNnCkN5eldUZjFRVzhYK2N3dTVvV1c0bEFGU2ZTWENQZUtJSnc3MXJyY1FqWTI5aTNqNkxXanMzdEkwQk5NR1pFODQKKzEwQnZoUkRzZEhOaHpiTjFXaFlNYzJ3aW55dVozZ3BOUWoyN2RUNVhTYU56b1d2VCtXUFRTK3NoN3QyQXJOUwpNL2NZK1RscVFYVmZOTDZ0Rmh0emZuV3BmcDFaTnVoT2drUUFJY255UEU1eVhQM0JOMXVNZFl6T09QSEprTmI0CnpzVk9QaGZENHlxZ1NRY0FpNW54RDBJbC8vdkNpc1F5UE1jMTlHOUxjV3pWKzRVSVFpc1YzNzJ6V0xRNFgwbzEKR1FpLzY5RUNnWUVBMFFpcUoxeVlzZGcvV2EzNzhXMzVaK24yOEpFVUpYQ1Qvd0ZwdGJlZmhoODk5L2VwZWdlTwpzSDNlMUZoc0l1WHBlWDZxazgwQk0vTy9vVmhCNmxRMTFDWUZwZHNYZ1Jaam1YWlVXTlFLOS9kZ1B2MndhNDRDClZHRVR3cWRnb29vckovcWtNSEdKSFJTcmZkVTZ0K1lPQ3A5b3RPTStZd29veTQybWJSN2pxSjhDZ1lFQTZwSHAKNXJra2U2b240cXg4RzRLbmFTc2ZQOFBacWhTaDcycTh6ZlZtRDdIcmtWWW5FaHhXVTJybEJJMUpmOFhQTktmdApCVlNhT3htRmh0K082Z2h4NHcrMmxuMllCK0tyMVZJVFVzajZXN2pNVXlEc04rcEJDVHdvQmUwbk9mVXhWaGptCk8zckE2SzRIa2gvYmtGY05vdVc0dEcrWnFWRlNMbU1WTlR6b2UxVUNnWUFLanJONVZYWG8xWkV0aUZvSE1aUzkKS05YdUJJWE45a2VqUTRFQlNvcm1EVUhsK2o0M0NaYXRWMDRmejI5MnU4SDAvdTdDbEVJUlM2aE1EOWNVYkxoagpSS0JZWmg1anlLdXpIb1RZRDYyV0pJcFo1Qm82OUdzdHM5RjVyVloySHlCYTNvL1lXb09nVW1EdTlBd0pLYmRmCjFmbEYyWXhYR0RaRFFaNDhPS2txNVFLQmdFL0ptaG9VMThnSXROQnhneldJVjVGNlRZTFBCM2JHMWQ0dUhGS2kKS2pra2Q5QlQwYTVqWFNtNnJuUEI2MEkrOHFBaWpvakZva0NBQ2Q4Nm84NFBXVTIyeHBDaDM5aXV6V3dlSXR5Qgo2RWJTc1EyRm9WUFRwcE9SbHJ1TlUwNXZqSHlRczU5L3ZhWm5xOE9VZW9hNlZiVVhGcUNwWlVjbWxpR1pLbG1WCmdpNlJBb0dBV1BQcXJWTWkxVUZpK2ZDQkRZNWpnSVRCV3BINXd2UkhBUW10Q01nZXpKZ1hKekNWK3pla2UyY3QKcGU4OEl3US9ld1JBUzFHeUdrQ2pNM1VjU1djZmFTZ0JBTDZXaDBSbUJzQkx1NUowTkJtOU5hb0ZBaWdFZm0rSwo3bGhrem1vWnAwVmdKcHdTdE4vWTAwQldiSEI5TkdERlJrWFcxQ3VDRFNwdUpiS2FDcEE9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==
c
```



```
kubectl get --raw='/readyz?verbose'
```

```
[+]ping ok
[+]log ok
[+]etcd ok
[+]etcd-readiness ok
[+]informer-sync ok
[+]poststarthook/start-apiserver-admission-initializer ok
[+]poststarthook/generic-apiserver-start-informers ok
[+]poststarthook/priority-and-fairness-config-consumer ok
[+]poststarthook/priority-and-fairness-filter ok
[+]poststarthook/storage-object-count-tracker-hook ok
[+]poststarthook/start-apiextensions-informers ok
[+]poststarthook/start-apiextensions-controllers ok
[+]poststarthook/crd-informer-synced ok
[+]poststarthook/start-system-namespaces-controller ok
[+]poststarthook/start-cluster-authentication-info-controller ok
[+]poststarthook/start-kube-apiserver-identity-lease-controller ok
[+]poststarthook/start-kube-apiserver-identity-lease-garbage-collector ok
[+]poststarthook/start-legacy-token-tracking-controller ok
[+]poststarthook/start-service-ip-repair-controllers ok
[+]poststarthook/rbac/bootstrap-roles ok
[+]poststarthook/scheduling/bootstrap-system-priority-classes ok
[+]poststarthook/priority-and-fairness-config-producer ok
[+]poststarthook/bootstrap-controller ok
[+]poststarthook/start-kubernetes-service-cidr-controller ok
[+]poststarthook/aggregator-reload-proxy-client-cert ok
[+]poststarthook/start-kube-aggregator-informers ok
[+]poststarthook/apiservice-status-local-available-controller ok
[+]poststarthook/apiservice-status-remote-available-controller ok
[+]poststarthook/apiservice-registration-controller ok
[+]poststarthook/apiservice-discovery-controller ok
[+]poststarthook/kube-apiserver-autoregistration ok
[+]autoregister-completion ok
[+]poststarthook/apiservice-openapi-controller ok
[+]poststarthook/apiservice-openapiv3-controller ok
[+]shutdown ok
readyz check passed

```




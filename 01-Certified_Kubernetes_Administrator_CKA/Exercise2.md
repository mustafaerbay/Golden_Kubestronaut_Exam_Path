credit: https://killercoda.com/chadmcrowell/course/cka

# Monitoring Kubernetes with Metrics Server

> Get the CPU and Memory usages

```
k top no
k top pod
```

> Sort metrics by CPU or Memory

```
k top pod --sort-by=cpu

k top pod --sort-by=memory

k top pod php-apache  --containers --sort-by=memory
```

# Helm Charts

## Install Nginx

```
First, verify that Helm is installed and check the version:

helm version
Add the official nginx Helm repository:

helm repo add nginx-stable https://helm.nginx.com/stable
Update the repository to fetch the latest chart information:

helm repo update
Search for available nginx charts:

helm search repo nginx
Get detailed information about the nginx-ingress chart:

helm show chart nginx-stable/nginx-ingress
View the default values for this chart:

helm show values nginx-stable/nginx-ingress
List the configured repositories:

helm repo list

```

```
kubectl create namespace nginx-ingress
```

```
helm install my-nginx-ingress nginx-stable/nginx-ingress \
  --namespace nginx-ingress \
  --set controller.replicaCount=2 \
  --set controller.service.type=NodePort \
  --set controller.service.httpPort.nodePort=30080 \
  --set controller.service.httpsPort.nodePort=30443
```


```
helm status my-nginx-ingress -n nginx-ingress
```

>result:

```
controlplane:~$ helm status my-nginx-ingress -n nginx-ingress
NAME: my-nginx-ingress
LAST DEPLOYED: Sun Feb  1 17:18:15 2026
NAMESPACE: nginx-ingress
STATUS: deployed
REVISION: 1
DESCRIPTION: Install complete
RESOURCES:
==> v1/ServiceAccount
NAME               SECRETS   AGE
my-nginx-ingress   0         44s

==> v1/ClusterRole
NAME               CREATED AT
my-nginx-ingress   2026-02-01T17:18:16Z

==> v1/ClusterRoleBinding
NAME               ROLE                           AGE
my-nginx-ingress   ClusterRole/my-nginx-ingress   44s

==> v1/Role
NAME               CREATED AT
my-nginx-ingress   2026-02-01T17:18:16Z

==> v1/RoleBinding
NAME               ROLE                    AGE
my-nginx-ingress   Role/my-nginx-ingress   44s

==> v1/Service
NAME                          TYPE       CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
my-nginx-ingress-controller   NodePort   10.97.27.58   <none>        80:30080/TCP,443:30443/TCP   44s

==> v1/Deployment
NAME                          READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx-ingress-controller   2/2     2            2           44s

==> v1/Pod(related)
NAME                                           READY   STATUS    RESTARTS   AGE
my-nginx-ingress-controller-59cc8479cd-mbcqf   1/1     Running   0          43s
my-nginx-ingress-controller-59cc8479cd-sd6lk   1/1     Running   0          43s

==> v1/ConfigMap
NAME               DATA   AGE
my-nginx-ingress   0      44s
my-nginx-ingress-leader-election   0     44s

==> v1/IngressClass
NAME    CONTROLLER                     PARAMETERS   AGE
nginx   nginx.org/ingress-controller   <none>       44s

==> v1/Lease
NAME                               HOLDER                                         AGE
my-nginx-ingress-leader-election   my-nginx-ingress-controller-59cc8479cd-mbcqf   43s


TEST SUITE: None
NOTES:
NGINX Ingress Controller 5.3.2 has been installed.

For release notes, see: https://docs.nginx.com/nginx-ingress-controller/changelog/

For Helm installation instructions, see: https://docs.nginx.com/nginx-ingress-controller/install/helm/
controlplane:~$ 
```

Check the Helm release status:

helm status my-nginx-ingress -n nginx-ingress
List all Helm releases:

helm list -A
```
controlplane:~$ helm list -A
NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
my-nginx-ingress        nginx-ingress   1               2026-02-01 17:18:15.162500309 +0000 UTC deployed        nginx-ingress-2.4.2     5.3.2      

```

Verify the pods are running:

kubectl get pods -n nginx-ingress
Check the service configuration:

kubectl get svc -n nginx-ingress
Get the release history:

helm history my-nginx-ingress -n nginx-ingress
```
REVISION        UPDATED                         STATUS          CHART                   APP VERSION     DESCRIPTION     
1               Sun Feb  1 17:18:15 2026        deployed        nginx-ingress-2.4.2     5.3.2           Install complete

```

View the generated Kubernetes manifests:

helm get manifest my-nginx-ingress -n nginx-ingre

```
---
# Source: nginx-ingress/templates/controller-serviceaccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-nginx-ingress
  namespace: nginx-ingress
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm
---
# Source: nginx-ingress/templates/controller-configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-nginx-ingress
  namespace: nginx-ingress
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm
data:
  {}
---
# Source: nginx-ingress/templates/controller-leader-election-configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-nginx-ingress-leader-election
  namespace: nginx-ingress
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm
---
# Source: nginx-ingress/templates/clusterrole.yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: my-nginx-ingress
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm
rules:
- apiGroups:
  - ""
  resources:
  - configmaps
  - namespaces
  - pods
  - secrets
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - ""
  resources:
  - events
  verbs:
  - create
  - patch
  - list
- apiGroups:
  - ""
  resources:
  - services
  verbs:
  - get
  - list
  - watch
  - create
  - update
  - patch
  - delete
- apiGroups:
  - coordination.k8s.io
  resources:
  - leases
  verbs:
  - list
  - watch
- apiGroups:
  - discovery.k8s.io
  resources:
  - endpointslices
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - networking.k8s.io
  resources:
  - ingresses
  verbs:
  - get
  - list
  - watch
- apiGroups:
  - ""
  resources:
  - nodes
  verbs:
  - list
- apiGroups:
  - "apps"
  resources:
  - replicasets
  - daemonsets
  - statefulsets
  verbs:
  - get
- apiGroups:
  - networking.k8s.io
  resources:
  - ingressclasses
  verbs:
  - get
  - list
- apiGroups:
  - networking.k8s.io
  resources:
  - ingresses/status
  verbs:
  - update
- apiGroups:
  - k8s.nginx.org
  resources:
  - virtualservers
  - virtualserverroutes
  - globalconfigurations
  - transportservers
  - policies
  verbs:
  - list
  - watch
  - get
- apiGroups:
  - k8s.nginx.org
  resources:
  - virtualservers/status
  - virtualserverroutes/status
  - policies/status
  - transportservers/status
  verbs:
  - update
---
# Source: nginx-ingress/templates/clusterrolebinding.yaml
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: my-nginx-ingress
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm
subjects:
- kind: ServiceAccount
  name: my-nginx-ingress
  namespace: nginx-ingress
roleRef:
  kind: ClusterRole
  name: my-nginx-ingress
  apiGroup: rbac.authorization.k8s.io
---
# Source: nginx-ingress/templates/controller-role.yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: my-nginx-ingress
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm
  namespace: nginx-ingress
rules:
- apiGroups:
  - ""
  resources:
  - configmaps
  - pods
  - secrets
  - services
  verbs:
  - get
  - list
  - watch
- apiGroups:
    - ""
  resources:
    - namespaces
  verbs:
    - get
- apiGroups:
  - ""
  resources:
  - pods
  verbs:
  - update
- apiGroups:
  - ""
  resources:
  - events
  verbs:
  - create
  - patch
  - list
- apiGroups:
  - coordination.k8s.io
  resources:
  - leases
  resourceNames:
  - my-nginx-ingress-leader-election
  verbs:
  - get
  - update
- apiGroups:
  - coordination.k8s.io
  resources:
  - leases
  verbs:
  - create
---
# Source: nginx-ingress/templates/controller-rolebinding.yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: my-nginx-ingress
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm
  namespace: nginx-ingress
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: my-nginx-ingress
subjects:
- kind: ServiceAccount
  name: my-nginx-ingress
  namespace: nginx-ingress
---
# Source: nginx-ingress/templates/controller-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx-ingress-controller
  namespace: nginx-ingress
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm
spec:
  externalTrafficPolicy: Local
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    name: http
    nodePort: 30080
  - port: 443
    targetPort: 443
    protocol: TCP
    name: https
    nodePort: 30443
  selector:
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
---
# Source: nginx-ingress/templates/controller-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx-ingress-controller
  namespace: nginx-ingress
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm
spec:
  replicas: 2
  selector:
    matchLabels:
      app.kubernetes.io/name: nginx-ingress
      app.kubernetes.io/instance: my-nginx-ingress
  template:
    metadata:
      labels:
        app.kubernetes.io/name: nginx-ingress
        app.kubernetes.io/instance: my-nginx-ingress
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "9113"
        prometheus.io/scheme: "http"
    spec:      
      volumes: []
      serviceAccountName: my-nginx-ingress
      automountServiceAccountToken: true
      securityContext:
        seccompProfile:
          type: RuntimeDefault
      terminationGracePeriodSeconds: 30
      hostNetwork: false
      dnsPolicy: ClusterFirst
      containers:
      - image: nginx/nginx-ingress:5.3.2
        name: nginx-ingress
        imagePullPolicy: "IfNotPresent"
        ports:
        - name: http
          containerPort: 80
          protocol: TCP
        - name: https
          containerPort: 443
          protocol: TCP
        - name: prometheus
          containerPort: 9113
        - name: readiness-port
          containerPort: 8081
        readinessProbe:
          httpGet:
            path: /nginx-ready
            port: readiness-port
          periodSeconds: 1
          initialDelaySeconds: 0
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: false
          runAsUser: 101 #nginx
          runAsNonRoot: true
          capabilities:
            drop:
            - ALL
            add:
            - NET_BIND_SERVICE        
        volumeMounts: []
        env:
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        args:
          
          - -nginx-plus=false
          - -nginx-reload-timeout=60000
          - -enable-app-protect=false
          - -enable-app-protect-dos=false
          - -nginx-configmaps=$(POD_NAMESPACE)/my-nginx-ingress
          - -ingress-class=nginx
          - -health-status=false
          - -health-status-uri=/nginx-health
          - -nginx-debug=false
          - -log-level=info
          - -log-format=glog
          - -nginx-status=true
          - -nginx-status-port=8080
          - -nginx-status-allow-cidrs=127.0.0.1
          - -report-ingress-status
          - -enable-leader-election=true
          - -leader-election-lock-name=my-nginx-ingress-leader-election
          - -enable-prometheus-metrics=true
          - -prometheus-metrics-listen-port=9113
          - -prometheus-tls-secret=
          - -enable-service-insight=false
          - -service-insight-listen-port=9114
          - -service-insight-tls-secret=
          - -enable-custom-resources=true
          - -enable-snippets=false
          - -disable-ipv6=false
          - -enable-tls-passthrough=false
          - -enable-cert-manager=false
          - -enable-oidc=false
          - -enable-external-dns=false
          - -default-http-listener-port=80
          - -default-https-listener-port=443
          - -ready-status=true
          - -ready-status-port=8081
          - -enable-latency-metrics=false
          - -ssl-dynamic-reload=true
          - -enable-telemetry-reporting=true
          - -weight-changes-dynamic-reload=false
---
# Source: nginx-ingress/templates/controller-ingress-class.yaml
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: nginx
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm
spec:
  controller: nginx.org/ingress-controller
---
# Source: nginx-ingress/templates/controller-configmap.yaml
---
---
# Source: nginx-ingress/templates/controller-lease.yaml
apiVersion: coordination.k8s.io/v1
kind: Lease
metadata:
  name: my-nginx-ingress-leader-election
  namespace: nginx-ingress
  labels:
    helm.sh/chart: nginx-ingress-2.4.2
    app.kubernetes.io/name: nginx-ingress
    app.kubernetes.io/instance: my-nginx-ingress
    app.kubernetes.io/version: "5.3.2"
    app.kubernetes.io/managed-by: Helm

```


> Upgrade the release
```
helm upgrade my-nginx-ingress nginx-stable/nginx-ingress \
  --namespace nginx-ingress \
  --values /root/nginx-values.yaml
```



> below command upgrades the service annotations as well

```
helm upgrade my-nginx-ingress nginx-stable/nginx-ingress \
  --namespace nginx-ingress \
  --values /root/nginx-values.yaml \
  --set controller.service.annotations."service\.kubernetes\.io/load-balancer-source-ranges"="10.0.0.0/8"
```

```
controlplane:~$ helm history my-nginx-ingress -n nginx-ingress
REVISION        UPDATED                         STATUS          CHART                   APP VERSION     DESCRIPTION     
1               Sun Feb  1 17:18:15 2026        superseded      nginx-ingress-2.4.2     5.3.2           Install complete
2               Sun Feb  1 17:31:26 2026        superseded      nginx-ingress-2.4.2     5.3.2           Upgrade complete
3               Sun Feb  1 17:32:09 2026        deployed        nginx-ingress-2.4.2     5.3.2           Upgrade complete

```


## Rollback and Troubleshoot

```
First, let's create a problematic configuration by upgrading with invalid settings:

helm upgrade my-nginx-ingress nginx-stable/nginx-ingress \
  --namespace nginx-ingress \
  --set controller.image.tag="invalid-tag" \
  --set controller.replicaCount=5
Check the pod status - you should see some issues:

kubectl get pods -n nginx-ingress -w
Press Ctrl+C to stop watching after observing the failing pods.

View the release history to see all revisions:

helm history my-nginx-ingress -n nginx-ingress
Rollback to the previous working revision (revision 3):

helm rollback my-nginx-ingress 3 -n nginx-ingress
Check that the rollback was successful:

helm status my-nginx-ingress -n nginx-ingress
```
```
helm history my-nginx-ingress -n nginx-ingress

controlplane:~$ helm history my-nginx-ingress -n nginx-ingress
REVISION        UPDATED                         STATUS          CHART                   APP VERSION     DESCRIPTION     
1               Sun Feb  1 17:18:15 2026        superseded      nginx-ingress-2.4.2     5.3.2           Install complete
2               Sun Feb  1 17:31:26 2026        superseded      nginx-ingress-2.4.2     5.3.2           Upgrade complete
3               Sun Feb  1 17:32:09 2026        superseded      nginx-ingress-2.4.2     5.3.2           Upgrade complete
4               Sun Feb  1 17:34:23 2026        superseded      nginx-ingress-2.4.2     5.3.2           Upgrade complete
5               Sun Feb  1 17:37:59 2026        superseded      nginx-ingress-2.4.2     5.3.2           Upgrade complete
6               Sun Feb  1 17:38:54 2026        deployed        nginx-ingress-2.4.2     5.3.2           Rollback to 3   
controlplane:~$ 
```

```

Verify pods are healthy again:

kubectl get pods -n nginx-ingress
Get values for the current release:

helm get values my-nginx-ingress -n nginx-ingress
Get all information about the release:

helm get all my-nginx-ingress -n nginx-ingress

```


# Kubernetes PKI Essentials

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

Key takeaways:

- /etc/kubernetes/pki contains the crown jewels of a kubeadm control plane’s security.
- Certificates and keys here allow the API server, controller-manager, scheduler, and kubelet to trust and talk to each other.
- The API server is a static Pod; kubelet continuously tries to restart it if something goes wrong.
- admin.conf references the CA and client cert/key, letting kubectl authenticate.
Next time you troubleshoot API server issues, remember:
Check PKI before you panic.



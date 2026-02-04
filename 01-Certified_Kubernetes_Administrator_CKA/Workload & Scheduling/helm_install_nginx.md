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
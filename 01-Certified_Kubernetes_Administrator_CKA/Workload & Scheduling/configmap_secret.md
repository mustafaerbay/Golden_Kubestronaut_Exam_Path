# Kustomize Configmap Secret

> Use Kustomize to generate a ConfigMap and Secret and apply them to a pod. 

>Scaffold a Kustomize base containing a pod definition.
Use configMapGenerator and secretGenerator to create config data.
Apply the overlay and verify the pod receives both the ConfigMap and Secret.


```
controlplane:~$ mkdir -p kustomize-configmap-secret/base
```
```
cat <<'EOF_POD' > kustomize-configmap-secret/base/pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  containers:
  - name: app
    image: busybox:1.36
    command: ["sh", "-c", "env && sleep 3600"]
    envFrom:
    - configMapRef:
        name: app-config
    - secretRef:
        name: app-secret
EOF_POD
```

```
cat <<'EOF_K' > kustomize-configmap-secret/base/kustomization.yaml
resources:
  - pod.yaml
configMapGenerator:
  - name: app-config
    literals:
      - LOG_LEVEL=debug
      - FEATURE_FLAG=true
secretGenerator:
  - name: app-secret
    literals:
      - API_TOKEN=supersecret
      - API_URL=https://api.internal
EOF_K
```

```
controlplane:~$ kubectl apply -k kustomize-configmap-secret/base
configmap/app-config-6d496m7bt2 created
secret/app-secret-7hk9f66dff created
pod/app created
```

```
controlplane:~$ kubectl exec app -- printenv LOG_LEVEL FEATURE_FLAG API_TOKEN API_URL
debug
true
supersecret
https://api.internal
```

```
controlplane:~$ k get secrets -A
NAMESPACE   NAME                    TYPE     DATA   AGE
default     app-secret-7hk9f66dff   Opaque   2      3m59s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ k get secrets -A -o yaml
apiVersion: v1
items:
- apiVersion: v1
  data:
    API_TOKEN: c3VwZXJzZWNyZXQ=
    API_URL: aHR0cHM6Ly9hcGkuaW50ZXJuYWw=
  kind: Secret
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"v1","data":{"API_TOKEN":"c3VwZXJzZWNyZXQ=","API_URL":"aHR0cHM6Ly9hcGkuaW50ZXJuYWw="},"kind":"Secret","metadata":{"annotations":{},"name":"app-secret-7hk9f66dff","namespace":"default"},"type":"Opaque"}
    creationTimestamp: "2026-02-03T14:54:31Z"
    name: app-secret-7hk9f66dff
    namespace: default
    resourceVersion: "2618"
    uid: 62581b12-2c47-4510-9657-f4e3e636793a
  type: Opaque
kind: List
metadata:
  resourceVersion: ""
```
# Question 2 | MinIO Operator, CRD Config, Helm Install

>Solve this question on: ssh cka7968


Install the MinIO Operator using Helm in Namespace minio. Then configure and create the Tenant CRD:

1. Create Namespace minio

2. Install Helm chart minio/operator into the new Namespace. The Helm Release should be called minio-operator

3. Update the Tenant resource in /opt/course/2/minio-tenant.yaml to include enableSFTP: true under features

4. Create the Tenant resource from /opt/course/2/minio-tenant.yaml

---

### Answer:
1. 
```
k create ns minio
namespace/minio created
```

2. 
```
➜ candidate@cka7968:~$ helm repo list
NAME    URL                  
minio   http://localhost:6000
```

```
➜ candidate@cka7968:~$ helm search repo
NAME            CHART VERSION   APP VERSION     DESCRIPTION                    
minio/operator  6.0.4           v6.0.4          A Helm chart for MinIO Operator

```

```
➜ candidate@cka7968:~$ helm -n minio install minio-operator minio/operator
NAME: minio-operator
LAST DEPLOYED: Sun Dec 22 17:04:37 2024
NAMESPACE: minio
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

```
➜ candidate@cka7968:~$ helm -n minio ls
NAME            NAMESPACE   REVISION  ...  STATUS     CHART           APP VERSION
minio-operator  minio       1         ...  deployed   operator-6.0.4  v6.0.4   
```
```
➜ candidate@cka7968:~$ k -n minio get pod
NAME                              READY   STATUS    RESTARTS   AGE
minio-operator-7b595f559d-5hrj5   1/1     Running   0          24s
minio-operator-7b595f559d-sl22g   1/1     Running   0          25s
```

3. 
```
➜ candidate@cka7968:~$ vim /opt/course/2/minio-tenant.yaml

apiVersion: minio.min.io/v2
kind: Tenant
metadata:
  name: tenant
  namespace: minio
  labels:
    app: minio
spec:
  features:
    bucketDNS: false
    enableSFTP: true                     # ADD
  image: quay.io/minio/minio:latest
  pools:
    - servers: 1
      name: pool-0
      volumesPerServer: 0
      volumeClaimTemplate:
        apiVersion: v1
        kind: persistentvolumeclaims
        metadata: { }
        spec:
          accessModes:
            - ReadWriteOnce
          resources:
            requests:
              storage: 10Mi
          storageClassName: standard
        status: { }
  requestAutoCert: true

```
4. 
```
➜ candidate@cka7968:~$ k -f /opt/course/2/minio-tenant.yaml apply
tenant.minio.min.io/tenant created

➜ candidate@cka7968:~$ k -n minio get tenant
NAME     STATE                      HEALTH   AGE
tenant   empty tenant credentials            21s
```
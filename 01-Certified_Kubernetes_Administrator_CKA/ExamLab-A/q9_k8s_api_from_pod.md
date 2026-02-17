# Question 9 | Contact K8s Api from inside Pod

Solve this question on: ssh cka9412


There is ServiceAccount secret-reader in Namespace project-swan. Create a Pod of image nginx:1-alpine named api-contact which uses this ServiceAccount.

Exec into the Pod and use curl to manually query all Secrets from the Kubernetes Api.

Write the result into file /opt/course/9/result.json.

#### Create Pod which uses ServiceAccount
```
➜ ssh cka9412

➜ candidate@cka9412:~$ k run api-contact --image=nginx:1-alpine --dry-run=client -o yaml > 9.yaml
```
```
➜ candidate@cka9412:~$ vim 9.yaml

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: api-contact
  name: api-contact
  namespace: project-swan             # add
spec:
  serviceAccountName: secret-reader   # add
  containers:
  - image: nginx:1-alpine
    name: api-contact
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

```
➜ candidate@cka9412:~$ k -f 9.yaml apply
pod/api-contact created
```

#### Contact K8s Api from inside Pod

```
➜ candidate@cka9412:~$ k -n project-swan exec api-contact -it -- sh

➜ / # curl https://kubernetes.default
curl: (60) SSL peer certificate or SSH remote key was not OK
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the webpage mentioned above.

➜ / # curl -k https://kubernetes.default
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {},
  "status": "Failure",
  "message": "forbidden: User \"system:anonymous\" cannot get path \"/\"",
  "reason": "Forbidden",
  "details": {},
  "code": 403
}~ $ 

➜ / # curl -k https://kubernetes.default/api/v1/secrets
{
  "kind": "Status",
  "apiVersion": "v1",
  "metadata": {},
  "status": "Failure",
  "message": "secrets is forbidden: User \"system:anonymous\" cannot list resource \"secrets\" in API group \"\" at the cluster scope",
  "reason": "Forbidden",
  "details": {
    "kind": "secrets"
  },
  "code": 403
}
```

#### Use ServiceAccount Token to authenticate
```
➜ / # TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)

➜ / # curl -k https://kubernetes.default/api/v1/secrets -H "Authorization: Bearer ${TOKEN}"
{
  "kind": "SecretList",
  "apiVersion": "v1",
  "metadata": {
    "resourceVersion": "4881"
  },
  "items": [
    {
...
    {
      "metadata": {
        "name": "read-me",
        "namespace": "project-swan",
...
```

```
➜ candidate@cka9412:~$ k auth can-i get secret --as system:serviceaccount:project-swan:secret-reader
yes
```

#### Store result at requested location

```
# cka9412:/opt/course/9/result.json
{
  "kind": "SecretList",
  "apiVersion": "v1",
  "metadata": {
    "resourceVersion": "4881"
  },
  "items": [
    {
...
    {
      "metadata": {
        "name": "read-me",
        "namespace": "project-swan",
        "uid": "f7c9a279-9609-4f9a-aa30-d29e175b7a6c",
        "resourceVersion": "3380",
        "creationTimestamp": "2024-12-05T15:11:58Z",
        "managedFields": [
          {
            "manager": "kubectl-create",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2024-12-05T15:11:58Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:data": {
                ".": {},
                "f:token": {}
              },
              "f:type": {}
            }
          }
        ]
      },
      "data": {
        "token": "ZjMyMDEzOTYtZjVkOC00NTg0LWE2ZjEtNmYyZGZkYjM4NzVl"
      },
      "type": "Opaque"
    }
  ]
}
...
```

```
➜ / # curl -k https://kubernetes.default/api/v1/secrets -H "Authorization: Bearer ${TOKEN}" > result.json

➜ / # exit

➜ candidate@cka9412:~$ k -n project-swan exec api-contact -it -- cat result.json > /opt/course/9/result.json
```

#### Connect via HTTPS with correct CA
```
➜ / # CACERT=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt

➜ / # curl --cacert ${CACERT} https://kubernetes.default/api/v1/secrets -H "Authorization: Bearer ${TOKEN}"
```


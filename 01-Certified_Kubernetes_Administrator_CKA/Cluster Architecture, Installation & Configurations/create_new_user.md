# Create New User

>Role-based access in Kubernetes is used to permit actions being performed by users or service accounts on Kubernetes API resources. There are no "deny" rules in RBAC, so you must be careful to set actions that are absolutely necessary for that user or service account.

Roles are created in Kubernetes to assign the access (e.g. get, list, delete, etc.) to a Kubernetes resource in a given namespace

RoleBindings are created within a namespace and attached to Roles in order to assign that resource's permission to the service account or user


>🔥TIP🔥: ClusterRoles and ClusterRoleBindings work in the same way, but apply across ALL namespaces (cluster-wide)
In order to create a Role and RoleBinding in a namespace, we have to create the namespace "web" using the command


## Create a new role for user in RBAC
```
k create ns web

k -n web create role pod-reader --verb=get,list --resource=pods

k -n web create rolebinding pod-reader-binding --role=pod-reader --user=carlton
```

## Create a pod in the web namespace

```
k -n web run pod1 --image=nginx
```

## Create a certificate signing request

>Since Kubernetes doesn't have a "user" resource, all that's required is a client certificate and key with the common name (CN) to match the user's name.

In our case, when we created the RoleBinding, we assigned it to the user "carlton", so that user will assume the permissions from the Role for that resource.

As long as the CN in the key is "carlton", we will be able to use this to access the Kubernetes API.

To create a private key, we can use the openssl command-line tool. We'll use 2048 bit encryption and we'll name it carlton.key
```
openssl genrsa -out carlton.key 2048
```

>Kubernetes itself is a certificate authority, therefore, it can approve and generate certificates. How convenient!

Let's create a Certificate Signing Request (CSR) for the Kubernetes API using our private key and insert the common name and output that to a file named carlton.csr with the following command

>🛑IMPORTANT🛑: Make sure to insert the Common Name (CN) into your CSR, or else the certificate will become invalid

```
openssl req -new -key carlton.key -subj "/CN=carlton" -out carlton.csr

controlplane:~$ ls
carlton.csr  carlton.key  filesystem
```

## Submit CSR to Kubernetes API

> Now that we have a CSR, we can submit it to the Kubernetes API for approval.

First, let's store the value of the CSR in an environment variable named "REQUEST"


```
controlplane:~$ export REQUEST=$(cat carlton.csr | base64 -w 0)
controlplane:~$ 
controlplane:~$ echo $REQUEST 
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1Z6Q0NBVDhDQVFBd0VqRVFNQTRHQTFVRUF3d0hZMkZ5YkhSdmJqQ0NBU0l3RFFZSktvWklodmNOQVFFQgpCUUFEZ2dFUEFEQ0NBUW9DZ2dFQkFPcnVacHpCRVNMek1aVktUb25SNlRaa0ZDbDNmYU8xNmVIMDRheUpYcmJ4CnZobVVIUWQ0RlVUMnRKMjRBRkZ2MllNcFJtT3Z3QUJuN3VqMW1hYmI3U1Z0NkNGUXRudWNRcDZ5MzFHTTJQV0wKb0lYU1JramtDUU5sRGNWTWpNRmI4WjF4UVlHODkySGtMRm4zS0ZzaXRDSFFCeFJ3dlRxUENxb09KYnJWRHlJOApPMjQxNE1oSFV2ODlkK2lBelJvWkdaU3JuSTFCRXhOK1V3bVZLRXcybnZBK0NKUGUrQ0cwUEFSRWYxaXc2bXhyCnZUSk45d1Q5eWhlKzJDaWFQWmV0ZHptc0wzQkxacDR5aGNrNU83Um5qVytJUnJodDFRZWdDUXJlcFl5SG1uWDAKK1RCaWZnREZrUjQwek9kd3FaR0NiZTNOVFp1VXNwNVlBSUpQZm9VWWxkRUNBd0VBQWFBQU1BMEdDU3FHU0liMwpEUUVCQ3dVQUE0SUJBUUMwUlRzRFQraFVwd3VUUklpZWtSVlhJS2poRFI5eGZFUlphelRKWndqbllOUmlYUDJSCjNEWVZ6Mys5azZMRnczNGozeWhpeUtHQjJucUJYUkY4dGthS205U096WXkySVlLWmo1TE44azVGOWRzdU9LZ0IKZ2YxeWNLbzQwaFpKOGdYZmpjRFhteFBJekV1MjBSaEFLNjByeXgyYkh4ZEpiZ1RnQ0szazhoWWprK0x1aFNRegp2RklOMFhjT1RZYW9Nc0VxWEkxck5CbXY2dVAzdVB5YkJNLy9BNzhhbWhIR1JRbCtRR3NoT0I1MDV1RXptbFdtCmtjaER5SFZieGtvZWZndGhIMkdjR3Y0ME1oTExQQVZMMlJ1QllFcW9KN3NKNm94dGVTVmVZeGVHL21LMzl6Z1AKc1p1YmwrV0w0WVRMM2RabHJRUVZBc1VUa2JTQUhYZHNMVzFtCi0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
```

>Then, we can create a YAML manifest and sumbit it to the Kubernetes API. Insert the $REQUEST variable next to "request: " like so

```
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: carlton
spec:
  groups:
  - system:authenticated
  request: $REQUEST
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
EOF
```

```
controlplane:~$ k get csr
NAME        AGE   SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
carlton     6s    kubernetes.io/kube-apiserver-client           kubernetes-admin           <none>              Pending
csr-qkspw   12d   kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued
```

## Test role with the new user

>Let's assume the role that we set for our new user and test access to Kubernetes!

In order to get our client certificate that we can use in our kubeconfig, we'll approve the CSR we submitted to the Kubernetes API

```
k certificate approve carlton
```

```
controlplane:~$ k get csr
NAME        AGE   SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
carlton     67s   kubernetes.io/kube-apiserver-client           kubernetes-admin           <none>              Approved,Issued
csr-qkspw   12d   kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued
```

> We can extract the client certificate out from the "k get csr" command, decode it and save it to a file named carlton.crt
```
k get csr carlton -o jsonpath='{.status.certificate}' | base64 -d > carlton.crt
```

> Now that we have the key and certificate, we can set the credentials in our kubeconfig and embed the certs within

```
k config set-credentials carlton --client-key=carlton.key --client-certificate=carlton.crt --embed-certs
```


```
controlplane:~$ k config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://172.30.1.2:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
users:
- name: carlton
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
- name: kubernetes-admin
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
```

> Next, we'll set and use the context in which kubectl uses to access the Kubernetes API

```
controlplane:~$ k config set-context carlton --user=carlton --cluster=kubernetes
Context "carlton" created.
controlplane:~$ 
controlplane:~$ k config use-context carlton
Switched to context "carlton".
```

```
controlplane:~$ k -n web get po
NAME   READY   STATUS    RESTARTS   AGE
pod1   1/1     Running   0          9m29s
```
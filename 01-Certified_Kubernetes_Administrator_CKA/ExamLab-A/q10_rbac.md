# Question 10 | RBAC ServiceAccount Role RoleBinding 
Solve this question on: ssh cka3962

Create a new ServiceAccount processor in Namespace project-hamster. Create a Role and RoleBinding, both named processor as well. These should allow the new SA to only create Secrets and ConfigMaps in that Namespace.

---

```
➜ ssh cka3962

➜ candidate@cka3962:~$ k -n project-hamster create sa processor
serviceaccount/processor created
```

```
➜ candidate@cka3962:~$ k -n project-hamster create role processor --verb=create --resource=secret --resource=configmap
role.rbac.authorization.k8s.io/processor created
```
```
# kubectl -n project-hamster get role processor --verb=create --resource=secret --resource=configmap -o yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: processor
  namespace: project-hamster
rules:
- apiGroups:
  - ""
  resources:
  - secrets
  - configmaps
  verbs:
  - create
```

bind role to serviceaccount

```
➜ candidate@cka3962:~$ k -n project-hamster create rolebinding processor --role processor --serviceaccount project-hamster:processor
rolebinding.rbac.authorization.k8s.io/processor created
```

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: processor
  namespace: project-hamster
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: processor
subjects:
- kind: ServiceAccount
  name: processor
  namespace: project-hamster
```

verify:

```
➜ candidate@cka3962:~$ k -n project-hamster auth can-i create secret --as system:serviceaccount:project-hamster:processor
yes

➜ candidate@cka3962:~$ k -n project-hamster auth can-i create configmap --as system:serviceaccount:project-hamster:processor
yes

➜ candidate@cka3962:~$ k -n project-hamster auth can-i create pod --as system:serviceaccount:project-hamster:processor
no

➜ candidate@cka3962:~$ k -n project-hamster auth can-i delete secret --as system:serviceaccount:project-hamster:processor
no

➜ candidate@cka3962:~$ k -n project-hamster auth can-i get configmap --as system:serviceaccount:project-hamster:processor
no
```
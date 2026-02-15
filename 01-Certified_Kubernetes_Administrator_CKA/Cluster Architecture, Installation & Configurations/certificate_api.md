# Certificates API

```

controlplane ~ ✖ cat akshay.csr | base64 -w 0
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQTNENmpMaitHZmo5cWhxOHJVVTJZclB1QVdURjhDY0F5S1pNaEpFMWwyelA5Cnk5K2VUaVdVekRQaUFSMm1NdndUQ0k1VXhPZ3JBRGlzaWRwQzQrUkpVVkxxQXVZVjF2YkYvcG5wV3dNQm9SejkKb1V3RDZLb1Z5ZkpKdVlUSmMxQ09WVHI3eUZxWENjeGdhaEIzLzY1TDhsZEptc3loZG5xakJQUlE3ZmN5WWc0MQp4SlVGdlkzSW8rTTE4R3NHWDhQMC9XenFuYWkrMm5EeS9oUnJoSzhQalRoUi9QZGMvY2cwK2lJT2ZsWStZME8wCmYvU0lJUEV0ZmlFaG1hVEgyTXNtUEdBdFpZbEQ3aDhPc0dLNEJCaS96Z2ZENk1wdEd3VWNlTUM1a1hyRkpDUkMKa0E3azRQbVNmaW9DWWZIc2dvbDFDTFVwWnk5SWpqakxaWFdtWGdMbGp3SURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBS1N2c2ZIOUxKRVhhbzhCRzZIUUVRZzBzckRERERmWHpjSENMOUt4WVJNNHBMWU5WU2txCldOVElCbjRacnJua1Q5NTY3R01Oc2lCNWdPY2krSE1mZFdrMUNDUmhyWk8xMGJKQmZwZnJaZFJINVgyWnExZSsKc3dXSWxxelJXSGZCYStQWXBKUGRDQWdWRkhEalNyWUQ0a0V6SnhBOUE2ak9SMWNRQ0t6anNiRElVQk4yOHN6eApuekNVUUd0QzhLYzkvS2ovTXpBWFZpVXBBeHB6YjVwczdoejVaM3ljbW0rTHUrQm50dzFYUkJRbjFoclFDekxKCkJML1VwUWF3K1lVME8wNDRFUGhGRDg5MGo1MzZESVR1ZHhuRGJIN0ZnSzFRWCtHODJNZVM1SGN4eW9hdjBHbm4KYVBYTXE1OTJlQ3czSmcwdzdLM25xY2R0OGlRMUQzTklGbGs9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
controlplane ~ ➜  

```

```

controlplane ~ ➜  export REQUEST=$(cat akshay.csr | base64 -w 0)

controlplane ~ ➜  

controlplane ~ ➜  cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: akshay 
spec:
  groups:
  - system:authenticated
  request: $REQUEST
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
EOF
certificatesigningrequest.certificates.k8s.io/akshay created
```

## Approve the CSR

```

controlplane ~ ✖ kubectl get csr
NAME        AGE    SIGNERNAME                                    REQUESTOR                  REQUESTEDDURATION   CONDITION
akshay      2m8s   kubernetes.io/kube-apiserver-client           kubernetes-admin           <none>              Pending
csr-6h9pn   25m    kubernetes.io/kube-apiserver-client-kubelet   system:node:controlplane   <none>              Approved,Issued

controlplane ~ ➜  k certificate approve akshay
certificatesigningrequest.certificates.k8s.io/akshay approved
```

## Deny the CSR

```

controlplane ~ ➜  k certificate deny agent-smith
certificatesigningrequest.certificates.k8s.io/agent-smith denied

```
# Create a DaemonSet that configures Nodes

Create a DaemonSet named configurator , it should:

be in Namespace configurator
use image bash
mount /configurator as HostPath volume on the Node it's running on
write aba997ac-1c89-4d64 into file /configurator/config on its Node via the command: section
be kept running using sleep 1d or similar after the file write command


```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: configurator
  namespace: configurator
  labels:
    k8s-app: configurator
spec:
  selector:
    matchLabels:
      name: configurator
  template:
    metadata:
      labels:
        name: configurator 
    spec:
      containers:
      - name: fluentd-elasticsearch
        image: bash
        command: ['sh', '-c', 'echo "aba997ac-1c89-4d64" > /configurator/config  && sleep 3600']
        volumeMounts:
        - name: varlog
          mountPath: /configurator
      # it may be desirable to set a high priority class to ensure that a DaemonSet Pod
      # preempts running Pods
      # priorityClassName: important
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /configurator
```
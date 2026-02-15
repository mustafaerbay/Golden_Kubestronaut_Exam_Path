# VPA CPU Optimization Lab

Objectives:
Deploy a sample application and verify that the pods are running correctly.
Monitor pod resource usage using the kubectl top command to assess CPU and memory consumption.
Deploy a Vertical Pod Autoscaler (VPA) to observe how it adjusts CPU and memory resource recommendations based on the application’s current usage.
Introduce load to the application and monitor how VPA dynamically updates its recommendations in response to increased demand.
Interpret VPA recommendations by understanding key parameters like lowerBound, upperBound, and uncappedTarget for resource management.

----


```
controlplane ~ ➜  k apply -f vpa-cpu-testing.yml 
deployment.apps/flask-app-4 created
service/flask-app-4-service created
```

```

controlplane ~ ➜  cat vpa-cpu-testing.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app-4
  labels:
    app: flask-app-4
spec:
  replicas: 2
  selector:
    matchLabels:
      app: flask-app-4
  template:
    metadata:
      labels:
        app: flask-app-4
    spec:
      containers:
      - name: flask-app-4
        image:  kodekloud/flask-session-app:1 
        ports:
        - name: http
          containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: flask-app-4-service
  labels:
    app: flask-app-4
spec:
  type: NodePort
  selector:
    app: flask-app-4
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 8080
    nodePort: 30080

```

## Monitoring Pod Resource Usage

```

controlplane ~ ➜  k top pod
NAME                         CPU(cores)   MEMORY(bytes)   
flask-app-4-668b99c9-2kzv8   1m           19Mi            
flask-app-4-668b99c9-tf2xk   1m           19Mi            

```


```

controlplane ~ ✖ cat vpa-cpu.yml 
---
apiVersion: "autoscaling.k8s.io/v1"
kind: VerticalPodAutoscaler
metadata:
  name: flask-app
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind: Deployment
    name: flask-app-4
  updatePolicy:
    updateMode: "Off"  # You can set this to "Auto" if you want automatic updates
  resourcePolicy:
    containerPolicies:
      - containerName: '*'
        minAllowed:
          cpu: 100m
        maxAllowed:
          cpu: 1000m
        controlledResources: ["cpu"]

```

```

controlplane ~ ➜  k get vpa
NAME        MODE   CPU    MEM   PROVIDED   AGE
flask-app   Off    100m         True       61s

```


```

controlplane ~ ➜  cat load.sh 
#!/bin/bash

echo "Load initiated in the background. Please do not terminate this process."

timeout 1000s bash -c 'for i in {1..10}; do (while true; do curl -s http://controlplane:30080 > /dev/null; done) & done; wait'

```

```

controlplane ~ ➜  k describe vpa flask-app
Name:         flask-app
Namespace:    default
Labels:       <none>
Annotations:  <none>
API Version:  autoscaling.k8s.io/v1
Kind:         VerticalPodAutoscaler
Metadata:
  Creation Timestamp:  2026-02-06T07:24:59Z
  Generation:          1
  Resource Version:    4836
  UID:                 c60586b5-ad95-4d7e-a9bd-8989afe26e80
Spec:
  Resource Policy:
    Container Policies:
      Container Name:  *
      Controlled Resources:
        cpu
      Max Allowed:
        Cpu:  1000m
      Min Allowed:
        Cpu:  100m
  Target Ref:
    API Version:  apps/v1
    Kind:         Deployment
    Name:         flask-app-4
  Update Policy:
    Update Mode:  Off
Status:
  Conditions:
    Last Transition Time:  2026-02-06T07:25:57Z
    Status:                True
    Type:                  RecommendationProvided
  Recommendation:
    Container Recommendations:
      Container Name:  flask-app-4
      Lower Bound:
        Cpu:  179m
      Target:
        Cpu:  323m
      Uncapped Target:
        Cpu:  323m
      Upper Bound:
        Cpu:  1
Events:       <none>
```

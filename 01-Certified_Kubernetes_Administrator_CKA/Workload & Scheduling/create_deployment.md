# Change Deployment Image

> Create a deployment named “apache” that uses the image httpd:2.4.54 and contains three pod replicas

```
k create deployment apache --image httpd:2.4.54 --replicas 3
```

> Scale Deployment

```
k scale deploy apache --replicas 5
```

## Change the Pod Image
> Change the image used for the pods in the 'apache' deployment to httpd:alpine .

```
controlplane:~$ kubectl set image deploy apache httpd=httpd:latest httpd=httpd:alpine
controlplane:~$ 
controlplane:~$ # list the image used in the deployment to verify the change was successful
controlplane:~$ kubectl get deploy apache -o yaml | grep image
      - image: httpd:alpine
        imagePullPolicy: IfNotPresent
```

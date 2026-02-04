# Resolve Service IP from Pod

## Create a Deployment
```
# create a deployment named apache that uses the httpd image
kubectl create deploy apache --image httpd
```

## Expose the Deployment
```
k expose deploy apache --name apache-svc --port 80
```

## Create a pod and use DNS

```
controlplane:~$ kubectl run netshoot --image=nicolaka/netshoot --rm -it -- sh
All commands and output from this session will be recorded in container logs, including credentials and sensitive information passed through the command prompt.
If you don't see a command prompt, try pressing enter.
~ # # verify that you can reach the service
~ # wget -O- apache-svc
Connecting to apache-svc (10.108.248.22:80)
writing to stdout
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<title>It works! Apache httpd</title>
</head>
<body>
<p>It works!</p>
</body>
</html>
-                    100% |*******************************************************************************************************|   191  0:00:00 ETA
written to stdout
```
# Question 13 | Gateway Api Ingress

Solve this question on: ssh cka7968

The team from Project r500 wants to replace their Ingress (networking.k8s.io) with a Gateway Api (gateway.networking.k8s.io) solution. The old Ingress is available at /opt/course/13/ingress.yaml.

Perform the following in Namespace project-r500 and for the already existing Gateway:

Create a new HTTPRoute named traffic-director which replicates the routes from the old Ingress

Extend the new HTTPRoute with path /auto which forwards to mobile backend if the User-Agent is exactly mobile and to desktop backend otherwise

The existing Gateway is reachable at http://r500.gateway:30080 which means your implementation should work for these commands:

----

#### Investigate CRDs
```
➜ ssh cka7968

➜ candidate@cka7968:~$ k get crd
NAME                                        CREATED AT
clientsettingspolicies.gateway.nginx.org    2024-12-28T13:11:21Z
gatewayclasses.gateway.networking.k8s.io    2024-12-28T13:11:21Z
gateways.gateway.networking.k8s.io          2024-12-28T13:11:21Z
grpcroutes.gateway.networking.k8s.io        2024-12-28T13:11:21Z
httproutes.gateway.networking.k8s.io        2024-12-28T13:11:22Z
nginxgateways.gateway.nginx.org             2024-12-28T13:11:23Z
nginxproxies.gateway.nginx.org              2024-12-28T13:11:23Z
observabilitypolicies.gateway.nginx.org     2024-12-28T13:11:23Z
referencegrants.gateway.networking.k8s.io   2024-12-28T13:11:23Z
snippetsfilters.gateway.nginx.org           2024-12-28T13:11:23Z

➜ candidate@cka7968:~$ k get gateway -A
NAMESPACE      NAME   CLASS   ADDRESS   PROGRAMMED   AGE
project-r500   main   nginx             True         2m

➜ candidate@cka7968:~$ k get gatewayclass -A
NAME    CONTROLLER                                   ACCEPTED   AGE
nginx   gateway.nginx.org/nginx-gateway-controller   True       2m12s
```

1. 
```
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: traffic-director
  namespace: project-r500
spec:
  parentRefs:
    - name: main   # use the name of the existing Gateway
  hostnames:
    - "r500.gateway"
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /desktop
      backendRefs:
        - name: web-desktop
          port: 80
    - matches:
        - path:
            type: PathPrefix
            value: /mobile
      backendRefs:
        - name: web-mobile
          port: 80
```

test:

```
➜ candidate@cka7968:~$ k -n project-r500 get httproute
NAME               HOSTNAMES       AGE
traffic-director   ["r500.gateway"]   7s

➜ candidate@cka7968:~$ curl r500.gateway:30080/desktop
Web Desktop App

➜ candidate@cka7968:~$ curl r500.gateway:30080/mobile
Web Mobile App

➜ candidate@cka7968:~$ curl r500.gateway:30080
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

2. 

```
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: traffic-director
  namespace: project-r500
spec:
  parentRefs:
    - name: main
  hostnames:
    - "r500.gateway"
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /desktop
      backendRefs:
        - name: web-desktop
          port: 80
    - matches:
        - path:
            type: PathPrefix
            value: /mobile
      backendRefs:
        - name: web-mobile
          port: 80
# NEW FROM HERE ON
    - matches:
        - path:
            type: PathPrefix
            value: /auto
          headers:
          - type: Exact
            name: user-agent
            value: mobile
      backendRefs:
        - name: web-mobile
          port: 80
    - matches:
        - path:
            type: PathPrefix
            value: /auto
      backendRefs:
        - name: web-desktop
          port: 80
```

> Note that we use - path: and header:, not - path: and - header:. This means both path and header will be connected AND. So only if the path is /auto AND the header user-agent is mobile we route to mobile.

```
# WRONG EXAMPLE for explanation
    - matches:
        - path:
            type: PathPrefix
            value: /auto
        - headers:            # WRONG because now path and header are connected OR
          - type: Exact
            name: user-agent
            value: mobile
      backendRefs:
        - name: web-mobile
          port: 80
```

verify:

```
➜ candidate@cka7968:~$ curl -H "User-Agent: mobile" r500.gateway:30080/auto
Web Mobile App

➜ candidate@cka7968:~$ curl -H "User-Agent: something" r500.gateway:30080/auto
Web Desktop App

➜ candidate@cka7968:~$ curl r500.gateway:30080/auto
Web Desktop App
```
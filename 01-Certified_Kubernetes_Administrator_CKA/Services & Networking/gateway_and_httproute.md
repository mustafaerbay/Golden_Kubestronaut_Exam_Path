# Create a Gateway and HTTPRoute


```
controlplane:~$ kubectl get crds | grep gateway
clientsettingspolicies.gateway.nginx.org              2026-02-01T17:59:48Z
gatewayclasses.gateway.networking.k8s.io              2026-02-01T17:59:46Z
gateways.gateway.networking.k8s.io                    2026-02-01T17:59:47Z
grpcroutes.gateway.networking.k8s.io                  2026-02-01T17:59:47Z
httproutes.gateway.networking.k8s.io                  2026-02-01T17:59:47Z
nginxgateways.gateway.nginx.org                       2026-02-01T17:59:48Z
nginxproxies.gateway.nginx.org                        2026-02-01T17:59:48Z
observabilitypolicies.gateway.nginx.org               2026-02-01T17:59:48Z
referencegrants.gateway.networking.k8s.io             2026-02-01T17:59:47Z
snippetsfilters.gateway.nginx.org                     2026-02-01T17:59:48Z
upstreamsettingspolicies.gateway.nginx.org            2026-02-01T17:59:48Z

```

A Gateway in Kubernetes is a networking resource that controls external traffic into a cluster, supporting HTTP, HTTPS, TCP, and UDP protocols. It acts as a central entry point, replacing Ingress, and works with GatewayClasses and Routes (HTTPRoute, TCPRoute, UDPRoute) for flexible traffic management.

A GatewayClass defines the implementation of a Gateway , specifying which controller (e.g., NGINX, Istio, Cilium) will manage it. It acts as a template for Gateways, similar to how storageClass works for PersistentVolumes.

Install a basic Gateway resource named my-gateway in the default namespace. The gateway should be based on the gateway class nginx . You can view the gatewayClass with the command kubectl get gatewayclass .

The gateway will be listening on port 80.

```
# Deploy a basic Gateway that allows access to port 80 into the cluster
cat <<EOF | kubectl apply -f -
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
 name: my-gateway
 namespace: default
spec:
 gatewayClassName: nginx
 listeners:
 - name: http
   protocol: HTTP
   port: 80
EOF
```


```

# create a new deployment based on the `nginx` <span class='kc-markdown-code-copy'></span> image and expose the container on port 80
kubectl create deployment web --image=nginx --port=80

# create the service by exposing the deployment `web` <span class='kc-markdown-code-copy'></span> and set the service to be exposed on port 80 and target port 80 in the pod
kubectl expose deployment web --port=80 --target-port=80
# check that the `web` <span class='kc-markdown-code-copy'></span> deployment & service have been created
kubectl get deploy,svc
```

------
An HTTPRoute in Kubernetes defines routing rules for HTTP traffic, specifying how requests are forwarded from a Gateway to backend services. It supports host-based, path-based, and header-based routing, along with traffic splitting, retries, and filters.

Create a new HTTPRoute named web-route that will direct HTTP traffic to the underlying web service created in the previous step. Use path-based routing, and ensure all traffic to the domain handled by my-gateway is routed to the web service (setting the path to the root of the domain).

```
# create an HTTPRoute named `web-route` <span class='kc-markdown-code-copy'></span> and direct HTTP requests to the service `web` <span class='kc-markdown-code-copy'></span> on port 80
cat <<EOF | kubectl apply -f -
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: web-route
  namespace: default
spec:
  parentRefs:
  - name: my-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: "/"
    backendRefs:
    - name: web
      port: 80
EOF
```

```
controlplane:~$ kubectl get httproute
NAME        HOSTNAMES   AGE
web-route               42s

```
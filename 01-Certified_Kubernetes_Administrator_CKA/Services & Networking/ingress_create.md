# Create Ingress for existing Services


```
k create ingress world --rule="world.universe.mine/europe=europe:80" --rule="world.universe.mine/asia=asia:80" --dry-run=client -o yaml > ingress.yaml
```

Update the pathType: Prefix
```
sed -i "s|Exact|Prefix|g" ingress.yml
```

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: world
  namespace: world
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: world.universe.mine
    http:
      paths:
      - backend:
          service:
            name: europe
            port:
              number: 80
        path: /europe
        pathType: Prefix
      - backend:
          service:
            name: asia
            port:
              number: 80
        path: /asia
        pathType: Prefix
status:
  loadBalancer: {}
```

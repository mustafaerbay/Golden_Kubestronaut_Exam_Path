# NetworkPolicy Misconfigured

- edit the network policy and verify the solution

```
kubectl edit networkpolicy np-100x
```

verify:
```
kubectl exec tester-0 -- curl tester.level-1000.svc.cluster.local
kubectl exec tester-0 -- curl tester.level-1001.svc.cluster.local
kubectl exec tester-0 -- curl tester.level-1002.svc.cluster.local
```
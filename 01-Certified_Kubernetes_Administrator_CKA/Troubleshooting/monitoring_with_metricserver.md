# Monitoring Kubernetes with Metrics Server

> Get the CPU and Memory usages

```
k top no
k top pod
```

> Sort metrics by CPU or Memory

```
k top pod --sort-by=cpu

k top pod --sort-by=memory

k top pod php-apache  --containers --sort-by=memory
```

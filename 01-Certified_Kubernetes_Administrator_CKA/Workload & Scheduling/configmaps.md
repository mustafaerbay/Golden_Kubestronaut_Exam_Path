# Configmaps

>Create a configmap named redis-config . Within the configMap, use the key maxmemory with value 2mb and key maxmemory-policy with value allkeys-lru .

HINT: try k create cm -h for command options and examples

```
k create cm redis-config --from-literal=maxmemory=2mb --from-literal=maxmemory-policy=allkeys-lru
```

## Create the YAML for a pod with configMap attached

```
k run redis-pod --image=redis:7 --port 6379 --command 'redis-server' '/redis-master/redis.conf' --dry-run=client -o yaml > redis-pod.yaml
```

```
controlplane:~$ cat redis-pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: redis-pod
  name: redis-pod
spec:
  containers:
  - command:
    - redis-server
    - /redis-master/redis.conf
    image: redis:7
    name: redis-pod
    ports:
    - containerPort: 6379
    resources: {}
    volumeMounts:
    - name: config-volume
      mountPath: /redis-master/redis.conf
  volumes:
    - name: config-volume
      configMap:
        # Provide the name of the ConfigMap containing the files you want
        # to add to the container
        name: redis-config
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

## Check the pod configuration settings

```
Get a shell to the redis-pod pod and open the redis cli to confirm the values have been applied.

HINT: Use redis-cli to get to the redis cli

Solution

Run the following command to get a shell to the redis-pod pod

k exec -it redis-pod -- sh

Run the following command to get the maxmemory configuration setting:

CONFIG GET maxmemory
Run the following command to get the maxmemory-policy configuration setting:

CONFIG GET maxmemory-policy
```

```
controlplane:~$ k logs redis-pod
1:C 03 Feb 2026 16:29:45.368 * oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 03 Feb 2026 16:29:45.368 * Redis version=7.4.7, bits=64, commit=00000000, modified=0, pid=1, just started
1:C 03 Feb 2026 16:29:45.369 * Configuration loaded
1:M 03 Feb 2026 16:29:45.370 * monotonic clock: POSIX clock_gettime
1:M 03 Feb 2026 16:29:45.372 * Running mode=standalone, port=6379.
1:M 03 Feb 2026 16:29:45.373 * Server initialized
1:M 03 Feb 2026 16:29:45.374 * Ready to accept connections tcp
controlplane:~$ k exec -ti redis-pod -- sh
# pwd
/data
# cd /redis-master
# ls
redis.conf
# cat redis.conf
maxmemory 2mb
maxmemory-policy allkeys-lru
# 
# redis-cli
127.0.0.1:6379> CONFIG GET maxmemory
1) "maxmemory"
2) "2097152"
127.0.0.1:6379> 
127.0.0.1:6379> CONFIG GET maxmemory-policy
1) "maxmemory-policy"
2) "allkeys-lru"
127.0.0.1:6379> 
127.0.0.1:6379>
```
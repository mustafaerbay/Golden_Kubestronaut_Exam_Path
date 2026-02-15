

```

etcdctl --endpoints=https://[127.0.0.1]:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /opt/snapshot-pre-boot.db
```

```

controlplane ~ ✖ etcdctl --endpoints=https://[127.0.0.1]:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /opt/snapshot-pre-boot.db
{"level":"info","ts":"2026-02-06T08:44:15.171424Z","caller":"snapshot/v3_snapshot.go:65","msg":"created temporary db file","path":"/opt/snapshot-pre-boot.db.part"}
{"level":"info","ts":"2026-02-06T08:44:15.175191Z","logger":"client","caller":"v3@v3.5.16/maintenance.go:212","msg":"opened snapshot stream; downloading"}
{"level":"info","ts":"2026-02-06T08:44:15.175277Z","caller":"snapshot/v3_snapshot.go:73","msg":"fetching snapshot","endpoint":"https://[127.0.0.1]:2379"}
{"level":"info","ts":"2026-02-06T08:44:15.180103Z","logger":"client","caller":"v3@v3.5.16/maintenance.go:220","msg":"completed snapshot read; closing"}
{"level":"info","ts":"2026-02-06T08:44:15.201788Z","caller":"snapshot/v3_snapshot.go:88","msg":"fetched snapshot","endpoint":"https://[127.0.0.1]:2379","size":"1.8 MB","took":"now"}
{"level":"info","ts":"2026-02-06T08:44:15.201883Z","caller":"snapshot/v3_snapshot.go:97","msg":"saved","path":"/opt/snapshot-pre-boot.db"}
Snapshot saved at /opt/snapshot-pre-boot.db

```
Complete Restore Process

Step 1: Stop the kube-apiserver

Execute the following commands:

mv /etc/kubernetes/manifests/kube-apiserver.yaml /tmp/
sleep 30
Step 2: Restore the etcd snapshot

Run the command below to restore the snapshot:

etcdutl snapshot restore /opt/snapshot-pre-boot.db --data-dir /var/lib/etcd-from-backup
Expected output:

2025-04-24T09:38:07Z    info    snapshot/v3_snapshot.go:265     restoring snapshot
2025-04-24T09:38:07Z    info    membership/store.go:141 Trimming membership information from the backend...
2025-04-24T09:38:07Z    info    membership/cluster.go:421       added member
2025-04-24T09:38:07Z    info    snapshot/v3_snapshot.go:293     restored snapshot
Step 3: Update the etcd configuration

Open the etcd configuration file for editing:

vi /etc/kubernetes/manifests/etcd.yaml
Modify the volumes section as follows:

From:

  volumes:
  - hostPath:
      path: /etc/kubernetes/pki/etcd
      type: DirectoryOrCreate
    name: etcd-certs
  - hostPath:
      path: /var/lib/etcd                    # OLD directory
      type: DirectoryOrCreate
    name: etcd-data
To:

  volumes:
  - hostPath:
      path: /etc/kubernetes/pki/etcd
      type: DirectoryOrCreate
    name: etcd-certs
  - hostPath:
      path: /var/lib/etcd-from-backup        # NEW restored directory
      type: DirectoryOrCreate
    name: etcd-data
Upon saving this file, the etcd pod will restart automatically due to static pod behavior.

Step 4: Restart the kube-apiserver

Move the kube-apiserver manifest back to its original location:

mv /tmp/kube-apiserver.yaml /etc/kubernetes/manifests/
Wait for 60 seconds to allow the kube-apiserver to start.

Step 5: Restart other control plane components

Execute the following commands to restart additional components:

# Restart kube-controller-manager
mv /etc/kubernetes/manifests/kube-controller-manager.yaml /tmp/
sleep 20
mv /tmp/kube-controller-manager.yaml /etc/kubernetes/manifests/

# Restart kube-scheduler
mv /etc/kubernetes/manifests/kube-scheduler.yaml /tmp/
sleep 20
mv /tmp/kube-scheduler.yaml /etc/kubernetes/manifests/

# Restart kubelet
systemctl restart kubelet
Step 6: Monitor the restart process

Use the following command to monitor the restart:

watch crictl ps
Key indicators to observe:

All components should show STATUS = Running
The entire process should take approximately 2-3 minutes.
Step 7: Verify the restore

Run the commands below to check resource status across all namespaces:

# Check all resources across all namespaces
kubectl get deployments,services --all-namespaces

# Verify specific resources if needed
kubectl get pods --all-namespaces
kubectl get nodes
You should now observe all the resources that existed at the time the snapshot was taken.
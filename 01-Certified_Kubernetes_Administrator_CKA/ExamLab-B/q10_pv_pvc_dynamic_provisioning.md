# Question 10 | PV PVC Dynamic Provisioning
Solve this question on: ssh cka6016

There is a backup Job which needs to be adjusted to use a PVC to store backups.

Create a StorageClass named local-backup which uses provisioner: rancher.io/local-path and volumeBindingMode: WaitForFirstConsumer. To prevent possible data loss the StorageClass should keep a PV retained even if a bound PVC is deleted.

Adjust the Job at /opt/course/10/backup.yaml to use a PVC which request 50Mi storage and uses the new StorageClass.

Deploy your changes, verify the Job completed once and the PVC was bound to a newly created PV.

---

### Answer:

```
➜ candidate@cka6016:~$ vim sc.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: local-backup
provisioner: rancher.io/local-path
reclaimPolicy: Retain
volumeBindingMode: WaitForFirstConsumer
```

```
➜ candidate@cka6016:~$ k -f sc.yaml apply
storageclass.storage.k8s.io/local-backup created

➜ candidate@cka6016:~$ k get sc
NAME           PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ...
local-backup   rancher.io/local-path   Retain          WaitForFirstConsumer   ...
local-path     rancher.io/local-path   Delete          WaitForFirstConsumer   ...
```

```
➜ candidate@cka6016:~$ cd /opt/course/10

➜ candidate@cka6016:/opt/course/10$ cp backup.yaml backup.yaml_ori

➜ candidate@cka6016:/opt/course/10$ vim backup.yaml
```

```
# cka6016:/opt/course/10/backup.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: project-bern            # use same Namespace
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi                  # request the required size
  storageClassName: local-backup     # use the new StorageClass
---
apiVersion: batch/v1
kind: Job
metadata:
  name: backup
  namespace: project-bern
spec:
  backoffLimit: 0
  template:
    spec:
      volumes:
        - name: backup
          persistentVolumeClaim:     # CHANGE
            claimName: backup-pvc    # CHANGE
      containers:
        - name: bash
          image: bash:5
          command:
            - bash
            - -c
            - |
              set -x
              touch /backup/backup-$(date +%Y-%m-%d-%H-%M-%S).tar.gz
              sleep 15
          volumeMounts:
            - name: backup
              mountPath: /backup
      restartPolicy: Never
```

Deploy changes and verify
First we delete the existing Job because we did create it once before without any changes. And then we deploy:

```
➜ candidate@cka6016:/opt/course/10$ k delete -f backup.yaml 
job.batch "backup" deleted

➜ candidate@cka6016:/opt/course/10$ k apply -f backup.yaml 
persistentvolumeclaim/backup-pvc created
job.batch/backup created
```

Then we should see the Job execution created a Pod which used the PVC which created a PV:
```
➜ candidate@cka6016:/opt/course/10$ k -n project-bern get job,pod,pvc,pv
NAME               STATUS    COMPLETIONS   DURATION   AGE
job.batch/backup   Running   0/1           13s        13s

NAME               READY   STATUS    RESTARTS   AGE
pod/backup-q7dgx   1/1     Running   0          13s

NAME         STATUS   VOLUME                                     CAPACITY   ...
backup-pvc   Bound    pvc-dbccec94-cc31-4e30-b5fe-7cb42a85fe7a   50Mi       ...

NAME          CAPACITY   ...  RECLAIM POLICY  STATUS  CLAIM                     ...
pvc-dbcce...  50Mi       ...  Retain          Bound   project-bern/backup-pvc   ...
```
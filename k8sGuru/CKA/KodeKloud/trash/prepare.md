# KodeCloud
## Download etcd
```sh
# DownLoad from github
curl -L https://github.com/etcd-io/etcd/releases/download/v3.5.10/etcd-v3.5.10-linux-amd64.tar.gz
# extract
tar xvzf etcd-v3.5.10-linux-amd64.tar.gz
# run by default to port 2379
./etcd
```
to store/get a key/value object:
```sh
#put
etcdctl put key1 value
# get
etcdctl get key1
```

check version
```sh
etcdctl version
```



## Scheduling

### Manual Scheduling

add `nodeName` in the pod spec

example:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: manual-shedule-pod
spec:
  nodeName: worker-node1
  containers: 
  - name: busybox
    image: busybox
```


### Taints and Tolerations

Taints are set on Node and Toleration are set on Pod



When a Node is taint only Pod which have a toleration on that node can be scheduled on.

Taint a Node
```sh
kubectl taint node <node name> <key=value:taint-effect[NoSchedule | ]>

#example
kubectl taint node worker-node1 key=value:taint-effect

```

Tolerate a Pod
```sh
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
  - key: "example-key"
    operator: "Exists"
    effect: "NoSchedule"
```


### NodeAffinity


Back Up the etcd Data
From the terminal, log in to the etcd server:

ssh etcd1
Back up the etcd data:

ETCDCTL_API=3 etcdctl snapshot save /home/cloud_user/etcd_backup.db \
--endpoints=https://etcd1:2379 \
--cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
--cert=/home/cloud_user/etcd-certs/etcd-server.crt \
--key=/home/cloud_user/etcd-certs/etcd-server.key

Restore the etcd Data from the Backup
Stop etcd:

sudo systemctl stop etcd
Delete the existing etcd data:

sudo rm -rf /var/lib/etcd
Restore etcd data from a backup:

sudo ETCDCTL_API=3 etcdctl snapshot restore /home/cloud_user/etcd_backup.db \
--initial-cluster etcd-restore=https://etcd1:2380 \
--initial-advertise-peer-urls https://etcd1:2380 \
--name etcd-restore \
--data-dir /var/lib/etcd
Set database ownership:

sudo chown -R etcd:etcd /var/lib/etcd
Start etcd:

sudo systemctl start etcd
Verify the system is working:

ETCDCTL_API=3 etcdctl get cluster.name \
--endpoints=https://etcd1:2379 \
--cacert=/home/cloud_user/etcd-certs/etcd-ca.pem \
--cert=/home/cloud_user/etcd-certs/etcd-server.crt \
--key=/home/cloud_user/etcd-certs/etcd-server.key




-------------------------------

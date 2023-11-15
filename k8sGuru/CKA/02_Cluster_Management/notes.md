# Cluster Management
## Intro to HA
- K8s facilitates HA and you can design your cluster itself to be HA.
- to do this you need multiple control plane nodes

### HA Kube-api-server
![](../images/Screenshot%202023-11-07%20at%2015.18.25.png)

### HA etcd
#### Stacked etcd

![](../images/Screenshot%202023-11-07%20at%2015.21.30.png)

### External etcd

![](../images/Screenshot%202023-11-07%20at%2015.22.54.png)

## Intro to k8s Management tools
- kubectl
- kubeadm
- minikube
- Helm
- Kompose
- Kustomize

## Safely draining a k8s node

- when we performing maintenance we may need sometimes to remove kubernetes node from service
- To do this, we can `drain` the node
- Container running on the node will be terminated and rescheduled on another node

### Commands
- draining a node
```sh
kubectl drain <node name>
```
- Ignoring DaemonSets

When draining the node, we may need to ignore DaemonSets
```sh
kubectl drain <node name> --ignore-daemonsets
```
- Uncordoning a node

When maintenance are done we may need the node to run containers.
```sh
kubectl uncordon <node name>
```

## Upgrading k8s with kubeadm

### Control plane upgrade steps
- Drain the control plane node
    ```sh
    kubectl drain master-node --ignore-daemonsets
    ```
- Upgrade kubeadm on the control plane node
    ```sh
    sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubeadm=1.28.0-00
    ```
- Plan the upgrade (`kubeadm upgrade plan`)
    ```sh
    sudo kubeadm upgrade plan v1.28.0-00
    ```
- Apply the upgrade (`kubeadm upgrade apply`)
    ```sh
    kubeadm upgrade apply v1.28.0
    ```
- Update Kubelet & Kubectl
    ```sh
    sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubelet=1.28.0-00 kubectl=1.28.0-00
    ```
- Reload kubelet
    ```sh
    vagrant@master-node:~$ sudo systemctl daemon-reload
    vagrant@master-node:~$ sudo systemctl restart kubelet
    ```
    
- Uncordon Control Plan
    ```sh
    vagrant@master-node:~$ kubectl uncordon master-node
    ```
- `TO UPGRADE A WORKER NODE`
    ```sh
    vagrant@master-node:~$ kubectl drain worker-node01 
    ```
### Worker node upgrade steps
- Drain each worker node on the control plan
    ```sh
    See Previous step!!! (`kubectl drain worker-node01 `)
    ```
- upgrade kubeadm
    ```sh
    sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubeadm=1.28.0-00
    ```
- Upgrade the kubelet config (`kubeadm upgrade node`)
   ```sh
   sudo kubeadm upgrade node
   ```
- Upgrade Kubelet and kubectl
    ```sh
    sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubelet=1.28.0-00 kubectl=1.28.0-00
    ```
- Uncordon the node on the control plan
    ```sh
    vagrant@master-node:~$ kubectl uncordon worker-node01 
    ```
- Reload kubelet

    ```sh
    vagrant@worker-node01:~$ sudo systemctl daemon-reload
    vagrant@worker-node01:~$  sudo systemctl restart kubelet
    ```

#### Demo 
[link demo](demo_upgrade_kubernetes.md)


## Backing Up and Restoring etcd Cluster Data
`etcd` is the backend data storage for your k8s cluster

You can use the etcd comman line tool `etcdctl`
- Backup Data


    To Backup data use the `etcdctl snapshot save` command
    ```sh
    ETCDCTL_API=3 etcdtcl --endpoints $ENDPOINT snapshot save <file name>
    ```

 - Restore etcd data

    To Restore data use the `etcdctl snapshot restore` command
    ```sh
    ETCDCTL_API=3 etcdtcl snapshot restore <file name>
    ```

### Demo
[link demo](demo_backup_restore_etcd.md)

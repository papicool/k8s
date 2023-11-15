# Upgrade k8s 
## Control Plan
1- Drain Control

    ```sh
    kubectl get nodes
    sudo kubectl drain <control_plan>
    ```
2- Upgrade kubeadm

    ```sh
    sudo apt-get update && sudo apt-get install -y --allow-change-help-packages kubeadm=1.28.0-00
    ```

3- plan the upgrade

    ```sh
    sudo kubectl upgrade plan v1.28.0-00
    ```
4- apply the upgrade

    ```sh
    sudo kubectl upgrade apply v1.28.0
    ```

5- upgrade kubelet/kubectl

    ```sh
    sudo apt-get update && sudo apt-get install -y --allow-change-help-packages kubelet=1.28.0-00 kubectl=1.28.0-00
    ```

6- daemon-reload - restart kubelet

    ```sh
    sudo systemctl daemon-reload
    sudo systemctl restart kubelet
    ```
7- uncordon control plan
    ```sh
    sudo kubectl uncordon <control_plan>
    ```
8- drain worker
    ```sh
    sudo kubectl drain <worker>
    ```
## Worker Node

1- upgrade kubeadm

    ```sh
    sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubeadm=1.28.0-00
    ```

2- Plan the upgrade

    ```sh
    sudo kubectl upgrade node
    ```

3- upgrade kubelet/kubectl

    ```sh
    sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubectl=1.28.0-00 kubelet=1.28.0-00
    ```

4- Uncordon on the control plan

    ```sh
    sudo kubectl uncordon <worker>
    ```

5- daemon-reload and restart kubelet

    ```sh
        sudo systemctl daemon-reload
        sudo systemctl restart kubelet
    ```    

# Backing up and restoring etcd

 - backup etcd

    ```sh
    sudo ETCDCTL_API=3 etcdctl --endpoints $ENDPOINT snapshot save <file name>
    ```
 - restore etcd

    ```sh
    sudo ETCDCTL_API=3 etcdctl snapshot restore <file name>
    ```



# Kubectl Tips for CKA exam
### Imperative or declarative command
When we deploy objects like pod we use:
- imperative commands: define objects using yaml or json file after that we use `kubectl apply -f ...`
- declarative command: define object using kubectl command


    ```sh
    kubectl create deployment my-deployment --image=nginx
    ```

### Quick sample yaml
We can run an imperative command without create object with `--dry-run` option

    ```sh
    kubectl create deployment my-deployment --image=nginx --dry-run -o yaml
    ```

### Record a command
We can record a command that was made to make a change for a later review.
```sh
kubectl scale deployment my-deployment --replicas=5 --record
```
### Use Documentations

#### Create RBAC file from kubectl
- Create Role
```sh
kubectl create role pod-reader --verb=get,list,watch --resource=pods,pods/logs --dry-run=client -o yaml > pod-reader.yml
```
- Create RoleBinding
```sh
 kubectl create rolebinding pod-reader --role=pod-reader --user=dev --dry-run=client -o yaml > pod-readerBinding.yml
```
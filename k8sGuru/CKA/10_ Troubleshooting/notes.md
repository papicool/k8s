# Troubleshooting
## Troubleshooting Your K8s Cluster
### Kube API server down
if the kube API server is down, you will not be able to use kubectl to interact with the cluster.

We may get a message like :
```sh
vagrant@vagrant:~$ kubectl get nodes
The connection to the server localhost:6443 was refused - did you specify the right host or port
```

Assuming your kubeconfig is set up correctly, this may mean the API server is down.
`Possible fixes:`
Make sure the docker and kubelet services are up and running on your control plane node(s).
### Checking Node status
```sh
vagrant@vagrant:~$ kubectl get nodes
```
or 
```sh
vagrant@vagrant:~$ kubectl describe node <node name>
```

---------------------


if a node is having problems, it may be because a service is down on that node

`Possible fixes:`

```sh
vagrant@vagrant:~$ systemctl status kubelet
```

```sh
vagrant@vagrant:~$ systemctl start kubelet
```

```sh
vagrant@vagrant:~$ systemctl enable kubelet
```
---------------------

if services are healthy we may check the system Pods.

In a kubeadm cluster, several k8s components run as pods in the kube-system namespace

- check the status of these components:
```sh
vagrant@vagrant:~$ kubectl get pods -n kube-system
vagrant@vagrant:~$ kubectl describe pod <pod name> -n kube-system
```

## Checking Cluster and Node Logs

You can check logs for k8s-related services on each node using `journalctl`:

```sh
vagrant@vagrant:~$ sudo journalctl -u kubelet
vagrant@vagrant:~$ sudo journalctl -u docker
```
### Cluster component logs

logs are redirected to `/var/log`

## Troubleshooting Your Applications

### Checking pod status

```sh
vagrant@vagrant:~$ kubectl get pods
or 

vagrant@vagrant:~$ kubectl describe pod <pod name>
```

### Running Commands inside Container

```sh
vagrant@vagrant:~$ kubectl exec <pod name> -c <container name> -- <command>
```
## Checking Container Logs
```sh

vagrant@vagrant:~$ kubectl logs  <pod name> -c <container name>
```

## Troubleshooting K8s Networking Issues
### netshoot
You can run a container in the cluster that you
can use to run commands to test and gather
information about network functionality

The nicolaka/netshoot image is a great tool for
this.
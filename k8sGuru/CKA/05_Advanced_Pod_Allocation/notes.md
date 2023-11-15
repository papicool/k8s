# Advanced Pod Allocation
## Exploring K8s Scheduling

Scheduling is the process of assigning Pod to a Node.

The scheduler is a component of the control plan doing the scheduling process
### NodeSelector
NodeSelector limit which nodes the pods can be scheduled on. Pod will be assign on Node with `Label` identic to `app` in this example
```yaml
apuVersion: v1
Kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  nodeSelector:
    mylabel: app
```

### nodeName
We can bypass scheduling and assign a Pod to a specific Node by name using nodeName
### Demo
how to attach a label to a node?
```sh
kubectl label nodes <node name> <label name>=<label value>
```
example
```sh
vagrant@master-node:~$ kubectl get nodes
NAME            STATUS   ROLES           AGE   VERSION
master-node     Ready    control-plane   24h   v1.27.1
worker-node01   Ready    worker          24h   v1.27.1
vagrant@master-node:~$ kubectl label nodes worker-node01 special=true
```
files:
- [nodeselector pod file](demo/01_Exploring_k8s_scheduling/nodeselector-pod.yml)
- [nodename pod file](demo/01_Exploring_k8s_scheduling/nodename-pod.yml)

## Using DaemonSets
a `DaemontSets` automatically runs a copie of a pod on each node.

a `DaemontSets` will run a copy of the pod on new node added to the cluster

- [nodename pod file](demo/02_DaemonSets/my-daemonset.yml)

## Using Static Pods

A `static Pod` is a pod that is managed directly by the kubelet and not the k8s API server

A `Mirror Pod`will be create by kubelet for each `static Pod`. The Mirror Pod allow you to see the status of the static pod via the k8s API

To create a `static Pod`, we have to create the file in the `kubelet manifest directory` (/etc/kubernetes/manifests) on the worker node
- [nodename pod file](demo/02_DaemonSets/static-pod.yml)

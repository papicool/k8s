# Storage

## Kubernetes Storage Big Picture
Volume is to abstract storage of data from Pod
Volume can be shared between multiple Pods
- Fundamental Storage
    - Speed
    - Resilience
    - Replicated

![](../images/Screenshot%202023-10-31%20at%2016.14.30.png)

CSI: Container Storage Interface (help connect PV, PVC or SC with Entreprise DataSource)

PV : Persistent Volume


PVC: Persistent Volume Claim


SC: Storage Class

## Container Storage Interface

![](../images/Screenshot%202023-10-31%20at%2016.21.43.png)



![](../images/Screenshot%202023-10-31%20at%2016.22.11.png)



![](../images/Screenshot%202023-10-31%20at%2016.24.32.png)

## The Kubernetes Persistent Volume and Persistent Volume Claims


![](../images/Screenshot%202023-10-31%20at%2016.29.05.png)

PV have attributs like Size and IOPS
GCEPersistentDisk : is a plugin for Google Cloud to connect storage with PV and share storage

![](../images/Screenshot%202023-10-31%20at%2016.30.29.png)


a PV is an object independently like Pods, Node, Service. To Use a PV, a Pod need to claim it (define a PVC in the Pod)

a PVC is a resource in the API and an object in the cluster.

![](../images/Screenshot%202023-10-31%20at%2016.35.22.png)


So like a Pod, we must have to create it with a yaml file

to get PV in kubernetes

```sh
kubectl get pv
```
![](../images/Screenshot%202023-10-31%20at%2016.46.01.png)

ACCESS MODE: RWO (ReadWriteOnce), RWM (ReadWriteMany),ROM(ReadOnlyMany)
RECLAIM POLICY: Retain or Delete : this what a cluster does when a volume is released. It retain the volume and the content or delete them

## Storage Class

a Storage Class is an API resource referencing a backend data source
Storage Class enable Dynamic provisionning of volumes


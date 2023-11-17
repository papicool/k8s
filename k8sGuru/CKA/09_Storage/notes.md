# Storage
## K8s Storage Overview
### Container File systems
The Container File system is ephemeral.
### Volumes
Volumes are persistent, they allow to store data outside the container file system while allowing the container to access the data at runtime
### Persistent Volumes
Volumes offer a simple way to externalize storage.

Persistent Volumes are more advanced form of volume. They allow you to treat storage as an abstract resource and consume it using your Pods
## Using K8s Volumes
### Volumes & VolumeMounts
Volumes are set in the Pod's spec.

VolumeMounts are set in the container's spec.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-pod
spec:
  containers:
  - name: busybox
    image: busybox
    volumeMounts:
    - name: my-volume
      mountPath: /output
  volumes:
  - name: my-volume
    hostPath:
      path: /data
```

### Common Volume Types
`hostpath` : stores data in a specified directory on the k8s node
`emptyDit` : Stores data in a dynamically created location on the node. Data will exists as long as Pod exists

files:
- [volume](demo/01_using_k8s_volumes/volume-pod.yml)
- [shared-volume](demo/01_using_k8s_volumes/shared-volume-pod.yml)

## Exploring K8s Persistent Volumes
### PersistentVolumes
`PersistentVolumes` are k8s objects that alow you to treat storage as an abstract resource to be consumed by pods
### Storage Class
`Storage Classes` allow k8s administrators to specify the types of storage services they offer in their platform
### allowVolumeExpansion
The `allowVolumeExpansion` property of the StorageClass determines whether or not the StorageClass supports the ability to resize volumes after they are created
```yaml
allowVolumeExpansion: true
```
### reclaimPolicies
A `persistentVolume`'s `persistentVolumeReclaimPolicy` determines how the storage resources can be reused when the `PersistentVolume`'s associated `PersistentVolumeClaims` are deleted

```yaml
persistentVolumeReclaimPolicy: Recycle
```
others Options:
- Retain: keeps all data
- Delete: deletes the underlying staorage resource automatically

#### Demo

##### Control Plan

```sh
vagrant@master-node:~$ kubectl get nodes
NAME            STATUS   ROLES           AGE   VERSION
master-node     Ready    control-plane   43h   v1.27.1
worker-node01   Ready    worker          43h   v1.27.1
vagrant@master-node:~$ kubectl drain master-node --ignore-daemonsets
node/master-node cordoned
Warning: ignoring DaemonSet-managed Pods: kube-system/calico-node-pjgbz, kube-system/kube-proxy-ndt92
node/master-node drained
vagrant@master-node:~$ sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubeadm=1.28.0-00
Get:1 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/1.27/xUbuntu_22.04  InRelease [1632 B]
Get:2 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_22.04  InRelease [1639 B]                                          
Hit:4 http://us.archive.ubuntu.com/ubuntu jammy InRelease                                                                           
Hit:5 http://us.archive.ubuntu.com/ubuntu jammy-updates InRelease
Get:3 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [8993 B]  
Hit:6 http://us.archive.ubuntu.com/ubuntu jammy-backports InRelease
Hit:7 http://us.archive.ubuntu.com/ubuntu jammy-security InRelease
Fetched 12.3 kB in 1s (11.1 kB/s)
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be upgraded:
  kubeadm
1 upgraded, 0 newly installed, 0 to remove and 55 not upgraded.
Need to get 10.3 MB of archives.
After this operation, 2593 kB of additional disk space will be used.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.28.0-00 [10.3 MB]
Fetched 10.3 MB in 1s (12.5 MB/s)
(Reading database ... 44603 files and directories currently installed.)
Preparing to unpack .../kubeadm_1.28.0-00_amd64.deb ...
Unpacking kubeadm (1.28.0-00) over (1.27.1-00) ...
Setting up kubeadm (1.28.0-00) ...
Scanning processes...                                                                                                                                                                                                                         
Scanning linux images...                                                                                                                                                                                                                      

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
vagrant@master-node:~$ sudo  kubeadm upgrade plan v1.28.0-00
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[preflight] Running pre-flight checks.
[upgrade] Running cluster health checks
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: v1.27.7
[upgrade/versions] kubeadm version: v1.28.0
W1108 09:56:24.212812  480802 compute.go:99] [upgrade/versions] WARNING: Couldn't parse version stable version: illegal zero-prefixed version component "00" in "v1.28.0-00"
W1108 09:56:24.214867  480802 compute.go:100] [upgrade/versions] WARNING: Falling back to current kubeadm version as latest stable version
W1108 09:56:24.232005  480802 compute.go:150] [upgrade/versions] WARNING: Couldn't parse version version in the v1.27 series: illegal zero-prefixed version component "00" in "v1.28.0-00"

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   CURRENT       TARGET
kubelet     2 x v1.27.1   v1.28.0

Upgrade to the latest stable version:

COMPONENT                 CURRENT   TARGET
kube-apiserver            v1.27.7   v1.28.0
kube-controller-manager   v1.27.7   v1.28.0
kube-scheduler            v1.27.7   v1.28.0
kube-proxy                v1.27.7   v1.28.0
CoreDNS                   v1.10.1   v1.10.1
etcd                      3.5.7-0   3.5.9-0

You can now apply the upgrade by executing the following command:

	kubeadm upgrade apply v1.28.0

_____________________________________________________________________


The table below shows the current state of component configs as understood by this version of kubeadm.
Configs that have a "yes" mark in the "MANUAL UPGRADE REQUIRED" column require manual config upgrade or
resetting to kubeadm defaults before a successful upgrade can be performed. The version to manually
upgrade to is denoted in the "PREFERRED VERSION" column.

API GROUP                 CURRENT VERSION   PREFERRED VERSION   MANUAL UPGRADE REQUIRED
kubeproxy.config.k8s.io   v1alpha1          v1alpha1            no
kubelet.config.k8s.io     v1beta1           v1beta1             no
_____________________________________________________________________

vagrant@master-node:~$ kubeadm upgrade apply v1.28.0
couldn't create a Kubernetes client from file "/etc/kubernetes/admin.conf": failed to load admin kubeconfig: open /etc/kubernetes/admin.conf: permission denied
To see the stack trace of this error execute with --v=5 or higher
vagrant@master-node:~$ sudo  kubeadm upgrade apply v1.28.0
[upgrade/config] Making sure the configuration is correct:
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[preflight] Running pre-flight checks.
[upgrade] Running cluster health checks
[upgrade/version] You have chosen to change the cluster version to "v1.28.0"
[upgrade/versions] Cluster version: v1.27.7
[upgrade/versions] kubeadm version: v1.28.0
[upgrade] Are you sure you want to proceed? [y/N]: y
[upgrade/prepull] Pulling images required for setting up a Kubernetes cluster
[upgrade/prepull] This might take a minute or two, depending on the speed of your internet connection
[upgrade/prepull] You can also perform this action in beforehand using 'kubeadm config images pull'
[upgrade/apply] Upgrading your Static Pod-hosted control plane to version "v1.28.0" (timeout: 5m0s)...
[upgrade/etcd] Upgrading to TLS for etcd
[upgrade/staticpods] Preparing for "etcd" upgrade
[upgrade/staticpods] Renewing etcd-server certificate
[upgrade/staticpods] Renewing etcd-peer certificate
[upgrade/staticpods] Renewing etcd-healthcheck-client certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/etcd.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2023-11-08-09-58-09/etcd.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
[apiclient] Found 1 Pods for label selector component=etcd
[upgrade/staticpods] Component "etcd" upgraded successfully!
[upgrade/etcd] Waiting for etcd to become available
[upgrade/staticpods] Writing new Static Pod manifests to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests1216779811"
[upgrade/staticpods] Preparing for "kube-apiserver" upgrade
[upgrade/staticpods] Renewing apiserver certificate
[upgrade/staticpods] Renewing apiserver-kubelet-client certificate
[upgrade/staticpods] Renewing front-proxy-client certificate
[upgrade/staticpods] Renewing apiserver-etcd-client certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/kube-apiserver.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2023-11-08-09-58-09/kube-apiserver.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
[apiclient] Found 1 Pods for label selector component=kube-apiserver
[upgrade/staticpods] Component "kube-apiserver" upgraded successfully!
[upgrade/staticpods] Preparing for "kube-controller-manager" upgrade
[upgrade/staticpods] Renewing controller-manager.conf certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/kube-controller-manager.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2023-11-08-09-58-09/kube-controller-manager.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
[apiclient] Found 1 Pods for label selector component=kube-controller-manager
[upgrade/staticpods] Component "kube-controller-manager" upgraded successfully!
[upgrade/staticpods] Preparing for "kube-scheduler" upgrade
[upgrade/staticpods] Renewing scheduler.conf certificate
[upgrade/staticpods] Moved new manifest to "/etc/kubernetes/manifests/kube-scheduler.yaml" and backed up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2023-11-08-09-58-09/kube-scheduler.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This might take a minute or longer depending on the component/version gap (timeout 5m0s)
[apiclient] Found 1 Pods for label selector component=kube-scheduler
[upgrade/staticpods] Component "kube-scheduler" upgraded successfully!
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[upgrade] Backing up kubelet config file to /etc/kubernetes/tmp/kubeadm-kubelet-config1895103593/config.yaml
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

[upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.28.0". Enjoy!

[upgrade/kubelet] Now that your control plane is upgraded, please proceed with upgrading your kubelets if you haven't already done so.
vagrant@master-node:~$ sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubelet=1.28.0-00 kubectl=1.28.0-00
Get:1 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/1.27/xUbuntu_22.04  InRelease [1632 B]
Get:2 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_22.04  InRelease [1639 B]                                               
Hit:4 http://us.archive.ubuntu.com/ubuntu jammy InRelease                                                                           
Get:5 http://us.archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Hit:3 https://packages.cloud.google.com/apt kubernetes-xenial InRelease      
Hit:6 http://us.archive.ubuntu.com/ubuntu jammy-backports InRelease
Get:7 http://us.archive.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Fetched 232 kB in 1s (193 kB/s)  
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be upgraded:
  kubectl kubelet
2 upgraded, 0 newly installed, 0 to remove and 54 not upgraded.
Need to get 29.8 MB of archives.
After this operation, 5231 kB of additional disk space will be used.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.28.0-00 [10.3 MB]
Get:2 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.28.0-00 [19.5 MB]
Fetched 29.8 MB in 3s (8634 kB/s)  
(Reading database ... 44603 files and directories currently installed.)
Preparing to unpack .../kubectl_1.28.0-00_amd64.deb ...
Unpacking kubectl (1.28.0-00) over (1.27.1-00) ...
Preparing to unpack .../kubelet_1.28.0-00_amd64.deb ...
Unpacking kubelet (1.28.0-00) over (1.27.1-00) ...
Setting up kubectl (1.28.0-00) ...
Setting up kubelet (1.28.0-00) ...
Scanning processes...                                                                                                                                                                                                                         
Scanning linux images...                                                                                                                                                                                                                      

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
vagrant@master-node:~$ sudo systemctl daemon-reload
vagrant@master-node:~$ sudo systemctl restart kubelet
vagrant@master-node:~$ kubectl uncordon master-node
node/master-node uncordoned
vagrant@master-node:~$ kubectl get nodes
NAME            STATUS   ROLES           AGE   VERSION
master-node     Ready    control-plane   44h   v1.28.0
worker-node01   Ready    worker          44h   v1.27.1
vagrant@master-node:~$ kubectl drain worker-node01 --ignore-daemonsets
node/worker-node01 already cordoned
Warning: ignoring DaemonSet-managed Pods: kube-system/calico-node-dh98r, kube-system/kube-proxy-lhzzs
node/worker-node01 drained
vagrant@master-node:~$ kubectl uncordon worker-node01 
node/worker-node01 already uncordoned
vagrant@master-node:~$ kubectl get nodes
NAME            STATUS   ROLES           AGE   VERSION
master-node     Ready    control-plane   44h   v1.28.0
worker-node01   Ready    worker          44h   v1.28.0
vagrant@master-node:~$ 

```


##### Worker Node

```sh
vagrant@worker-node01:~$ sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubeadm=1.28.0-00
Hit:1 http://us.archive.ubuntu.com/ubuntu jammy InRelease
Get:3 http://us.archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
Get:4 http://us.archive.ubuntu.com/ubuntu jammy-backports InRelease [109 kB]
Get:5 http://us.archive.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Get:6 http://us.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [1153 kB]
Get:7 http://us.archive.ubuntu.com/ubuntu jammy-updates/main Translation-en [246 kB]
Get:8 http://us.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [995 kB]
Get:9 http://us.archive.ubuntu.com/ubuntu jammy-updates/universe Translation-en [218 kB]
Get:10 http://us.archive.ubuntu.com/ubuntu jammy-security/main amd64 Packages [945 kB]
Get:11 http://us.archive.ubuntu.com/ubuntu jammy-security/main Translation-en [187 kB]
Get:12 http://us.archive.ubuntu.com/ubuntu jammy-security/universe amd64 Packages [793 kB]
Get:13 http://us.archive.ubuntu.com/ubuntu jammy-security/universe Translation-en [146 kB]
Get:14 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_22.04  InRelease [1639 B]
Get:15 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/1.27/xUbuntu_22.04  InRelease [1632 B]                                                                        
Hit:2 https://packages.cloud.google.com/apt kubernetes-xenial InRelease                                                                                                                                    
Fetched 5024 kB in 11s (472 kB/s)                                                                                                                                                                          
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be upgraded:
  kubeadm
1 upgraded, 0 newly installed, 0 to remove and 55 not upgraded.
Need to get 10.3 MB of archives.
After this operation, 2593 kB of additional disk space will be used.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.28.0-00 [10.3 MB]
Fetched 10.3 MB in 1s (11.8 MB/s)
(Reading database ... 44603 files and directories currently installed.)
Preparing to unpack .../kubeadm_1.28.0-00_amd64.deb ...
Unpacking kubeadm (1.28.0-00) over (1.27.1-00) ...
Setting up kubeadm (1.28.0-00) ...
Scanning processes...                                                                                                                                                                                       
Scanning linux images...                                                                                                                                                                                    

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
vagrant@worker-node01:~$ sudo kubeadm upgrade node
[upgrade] Reading configuration from the cluster...
[upgrade] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[preflight] Running pre-flight checks
[preflight] Skipping prepull. Not a control plane node.
[upgrade] Skipping phase. Not a control plane node.
[upgrade] Backing up kubelet config file to /etc/kubernetes/tmp/kubeadm-kubelet-config2344969919/config.yaml
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[upgrade] The configuration for this node was successfully updated!
[upgrade] Now you should go ahead and upgrade the kubelet package using your package manager.
vagrant@worker-node01:~$ sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubelet=1.28.0-00 kubectl=1.28.0-00
Get:1 http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/1.27/xUbuntu_22.04  InRelease [1632 B]
Get:2 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_22.04  InRelease [1639 B]                                                                         
Hit:3 http://us.archive.ubuntu.com/ubuntu jammy InRelease                                                                                                     
Hit:5 http://us.archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:6 http://us.archive.ubuntu.com/ubuntu jammy-backports InRelease
Hit:7 http://us.archive.ubuntu.com/ubuntu jammy-security InRelease
Hit:4 https://packages.cloud.google.com/apt kubernetes-xenial InRelease
Fetched 3271 B in 6s (573 B/s)
Reading package lists... Done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be upgraded:
  kubectl kubelet
2 upgraded, 0 newly installed, 0 to remove and 54 not upgraded.
Need to get 29.8 MB of archives.
After this operation, 5231 kB of additional disk space will be used.
Get:1 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.28.0-00 [10.3 MB]
Get:2 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.28.0-00 [19.5 MB]
Fetched 29.8 MB in 6s (4891 kB/s)                                                                                                                                                                          
(Reading database ... 44603 files and directories currently installed.)
Preparing to unpack .../kubectl_1.28.0-00_amd64.deb ...
Unpacking kubectl (1.28.0-00) over (1.27.1-00) ...
Preparing to unpack .../kubelet_1.28.0-00_amd64.deb ...
Unpacking kubelet (1.28.0-00) over (1.27.1-00) ...
Setting up kubectl (1.28.0-00) ...
Setting up kubelet (1.28.0-00) ...
Scanning processes...                                                                                                                                                                                       
Scanning linux images...                                                                                                                                                                                    

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
vagrant@worker-node01:~$ sudo systemctl daemon-reload
vagrant@worker-node01:~$  sudo systemctl restart kubelet
vagrant@worker-node01:~$ 
```
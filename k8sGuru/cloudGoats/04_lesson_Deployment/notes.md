# Kubernetes Deployment

 Deployment rappe Pod as Pod rappe container

 ![](../images/Screenshot%202023-11-05%20at%2017.01.24.png)

 ```sh
 apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: prod-redis
  name: prod-redis
spec:
  selector:
    matchLabels:                  # Label Selector: modification will just concern pod with this label
      run: prod-redis
  replicas: 3                      # specify the nomber of Pod we will have --> to do this, the deployment update the replicaSet (RS)
  minReadySeconds: 300            # time to make to add a new pod everytime
  strategy:                       ##BEGIN# This section show how to update the live app anytime we made change
    rollingUpdate:
      maxSurge: 1                 # When MaxSurge=1 in this case we will have 4 pod in the cluster. will have one pod in the new desired state and we will delete one-by-one old pod version everytime we add a new pod with a desired state
      maxUnavailable: 0
    type: RollingUpdate           ##END#
  template:
    metadata:
      labels:
        run: prod-redis             # Label Selector: modification will just concern pod with this label
    spec:                           ##BEGIN# Pod definition, in this Pod we will have a redis container
      containers:
      - image: redis:4.0            # we can update the version of redis. atfer that we need to do `kubectl apply -f deployment.yml`
        name: redis                ##END#

 ```


 ## Demo

 ```sh
 
 user@xaadimulxadiim Lab01-Building_k8s_Cluster % vagrant ssh master
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-83-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Nov  6 02:12:43 PM UTC 2023

  System load:     0.8095703125       IPv4 address for cni0:  10.85.0.1
  Usage of /:      19.0% of 30.34GB   IPv6 address for cni0:  1100:200::1
  Memory usage:    24%                IPv4 address for eth0:  10.0.2.15
  Swap usage:      0%                 IPv4 address for eth1:  10.0.0.10
  Processes:       197                IPv4 address for tunl0: 172.16.77.128
  Users logged in: 0


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
vagrant@master-node:~$ ls
calico.yaml
vagrant@master-node:~$ git clone https://github.com/ACloudGuru-Resources/Course_Kubernetes_Deep_Dive_NP.git
Cloning into 'Course_Kubernetes_Deep_Dive_NP'...
remote: Enumerating objects: 573, done.
remote: Total 573 (delta 0), reused 0 (delta 0), pack-reused 573
Receiving objects: 100% (573/573), 428.62 KiB | 5.04 MiB/s, done.
Resolving deltas: 100% (115/115), done.
vagrant@master-node:~$ ls
Course_Kubernetes_Deep_Dive_NP  calico.yaml
vagrant@master-node:~$ cd Course_Kubernetes_Deep_Dive_NP/
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP$ ls
LICENSE  README.md  code-k8s  lesson-auto-scaling  lesson-code-k8s  lesson-deployment  lesson-networking  lesson-rbac  lesson-storage  sample-app
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP$ cd lesson-deployment/
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ ls
deploy.yml  deployment.yml
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ x
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get nodes
NAME            STATUS   ROLES           AGE     VERSION
master-node     Ready    control-plane   11m     v1.27.1
worker-node01   Ready    worker          9m15s   v1.27.1
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl version
WARNING: This version information is deprecated and will be replaced with the output from kubectl version --short.  Use --output=yaml|json to get the full version.
Client Version: version.Info{Major:"1", Minor:"27", GitVersion:"v1.27.1", GitCommit:"4c9411232e10168d7b050c49a1b59f6df9d7ea4b", GitTreeState:"clean", BuildDate:"2023-04-14T13:21:19Z", GoVersion:"go1.20.3", Compiler:"gc", Platform:"linux/amd64"}
Kustomize Version: v5.0.1
Server Version: version.Info{Major:"1", Minor:"27", GitVersion:"v1.27.7", GitCommit:"07a61d861519c45ef5c89bc22dda289328f29343", GitTreeState:"clean", BuildDate:"2023-10-18T11:33:23Z", GoVersion:"go1.20.10", Compiler:"gc", Platform:"linux/amd64"}
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl version -o yml
error: --output must be 'yaml' or 'json'
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl version -o yaml
clientVersion:
  buildDate: "2023-04-14T13:21:19Z"
  compiler: gc
  gitCommit: 4c9411232e10168d7b050c49a1b59f6df9d7ea4b
  gitTreeState: clean
  gitVersion: v1.27.1
  goVersion: go1.20.3
  major: "1"
  minor: "27"
  platform: linux/amd64
kustomizeVersion: v5.0.1
serverVersion:
  buildDate: "2023-10-18T11:33:23Z"
  compiler: gc
  gitCommit: 07a61d861519c45ef5c89bc22dda289328f29343
  gitTreeState: clean
  gitVersion: v1.27.7
  goVersion: go1.20.10
  major: "1"
  minor: "27"
  platform: linux/amd64

vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl apply -f deploy.yml 
deployment.apps/test created
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get deploy test --watch
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
test   3/3     3            0           11s
test   3/3     3            3           14s
^Cvagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl describe deploy test
Name:                   test
Namespace:              default
CreationTimestamp:      Mon, 06 Nov 2023 14:22:24 +0000
Labels:                 run=test
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=test
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        5
RollingUpdateStrategy:  0 max unavailable, 1 max surge
Pod Template:
  Labels:  run=test
  Containers:
   nginx:
    Image:        nginx:1.12
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   test-66ccf8f4d5 (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  52s   deployment-controller  Scaled up replica set test-66ccf8f4d5 to 3
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get rs
NAME              DESIRED   CURRENT   READY   AGE
test-66ccf8f4d5   3         3         3       2m48s
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ vi deploy.yml 
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl apply -f deploy.yml 
deployment.apps/test configured
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get deploy test --watch
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
test   6/6     6            6           4m21s
^Cvagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get rs
NAME              DESIRED   CURRENT   READY   AGE
test-66ccf8f4d5   6         6         6       5m10s
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ vi deploy.yml 
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl apply -f deploy.yml 
deployment.apps/test configured
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get deploy test --watch
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
test   6/6     1            6           5m39s
test   7/6     1            6           5m42s
test   7/6     1            7           5m47s
test   6/6     1            6           5m47s
test   6/6     2            6           5m47s
test   7/6     2            6           5m48s
test   7/6     2            7           5m53s
test   6/6     2            6           5m53s
test   6/6     3            6           5m53s
test   7/6     3            6           5m54s
test   7/6     3            7           5m59s
test   6/6     3            6           5m59s
test   6/6     4            6           5m59s
test   7/6     4            6           6m
test   7/6     4            7           6m5s
test   6/6     4            6           6m5s
test   6/6     5            6           6m5s
test   7/6     5            6           6m6s
test   7/6     5            7           6m11s
test   6/6     5            6           6m11s
test   6/6     6            6           6m11s
test   7/6     6            6           6m12s
test   7/6     6            7           6m17s
test   6/6     6            6           6m17s
^Cvagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get rs -o wide
NAME              DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES       SELECTOR
test-665d45f69b   6         6         6       78s     nginx        nginx:1.13   pod-template-hash=665d45f69b,run=test
test-66ccf8f4d5   0         0         0       6m54s   nginx        nginx:1.12   pod-template-hash=66ccf8f4d5,run=test
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ vi deploy.yml 
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ vi deploy.yml 
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl apply -f deploy.yml  --record
Flag --record has been deprecated, --record will be removed in the future
deployment.apps/test configured
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ watch kubectl get rs -o wide
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get rs -o wide
NAME              DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES       SELECTOR
test-665d45f69b   0         0         0       4m9s    nginx        nginx:1.13   pod-template-hash=665d45f69b,run=test
test-66ccf8f4d5   0         0         0       9m45s   nginx        nginx:1.12   pod-template-hash=66ccf8f4d5,run=test
test-84f8bd6779   6         6         6       43s     nginx        nginx:1.14   pod-template-hash=84f8bd6779,run=test
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl rollout history deploy test
deployment.apps/test 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         kubectl apply --filename=deploy.yml --record=true

vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl rollout history deploy test --revision=3
deployment.apps/test with revision #3
Pod Template:
  Labels:	pod-template-hash=84f8bd6779
	run=test
  Annotations:	kubernetes.io/change-cause: kubectl apply --filename=deploy.yml --record=true
  Containers:
   nginx:
    Image:	nginx:1.14
    Port:	<none>
    Host Port:	<none>
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>

vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ # Rollout to the previous version
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl rollout undo deploy test
deployment.apps/test rolled back
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get deploy test --watch
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
test   6/6     6            6           13m
^Cvagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl get rs -o wide
NAME              DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES       SELECTOR
test-665d45f69b   6         6         6       8m2s    nginx        nginx:1.13   pod-template-hash=665d45f69b,run=test
test-66ccf8f4d5   0         0         0       13m     nginx        nginx:1.12   pod-template-hash=66ccf8f4d5,run=test
test-84f8bd6779   0         0         0       4m36s   nginx        nginx:1.14   pod-template-hash=84f8bd6779,run=test
vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ kubectl rollout history deploy test
deployment.apps/test 
REVISION  CHANGE-CAUSE
1         <none>
3         kubectl apply --filename=deploy.yml --record=true
4         <none>

vagrant@master-node:~/Course_Kubernetes_Deep_Dive_NP/lesson-deployment$ 
 ```
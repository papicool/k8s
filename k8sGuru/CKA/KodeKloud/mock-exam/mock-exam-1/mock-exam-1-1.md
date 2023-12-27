info_outline
Question
SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1

Create a service account called deploy-cka20-arch. Further create a cluster role called deploy-role-cka20-arch with permissions to get the deployments in cluster1.


Finally create a cluster role binding called deploy-role-binding-cka20-arch to bind deploy-role-cka20-arch cluster role with deploy-cka20-arch service account.


info_outline
Solution
Create the service account, cluster role and role binding:

student-node ~ ➜  kubectl --context cluster1 create serviceaccount deploy-cka20-arch
student-node ~ ➜  kubectl --context cluster1 create clusterrole deploy-role-cka20-arch --resource=deployments --verb=get
student-node ~ ➜  kubectl --context cluster1 create clusterrolebinding deploy-role-binding-cka20-arch --clusterrole=deploy-role-cka20-arch --serviceaccount=default:deploy-cka20-arch



You can verify it as below:

student-node ~ ➜  kubectl --context cluster1 auth can-i get deployments --as=system:serviceaccount:default:deploy-cka20-arch
yes
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 2
info_outline
Question
SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


There is a script located at /root/pod-cka26-arch.sh on the student-node. Update this script to add a command to filter/display the label with value component of the pod called kube-apiserver-cluster1-controlplane (on cluster1) using jsonpath.


info_outline
Solution
Update pod-cka26-arch.sh script:

student-node ~ ➜ vi pod-cka26-arch.sh



Add below command in it:

kubectl --context cluster1 get pod -n kube-system kube-apiserver-cluster1-controlplane  -o jsonpath='{.metadata.labels.component}'
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 3
info_outline
Question
SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


Run a pod called alpine-sleeper-cka15-arch using the alpine image in the default namespace that will sleep for 7200 seconds.


info_outline
Solution
Create the pod definition:

student-node ~ ➜  vi alpine-sleeper-cka15-arch.yaml



##Content should be:

apiVersion: v1
kind: Pod
metadata:
  name: alpine-sleeper-cka15-arch
spec:
  containers:
  - name: alpine
    image: alpine
    command: ["/bin/sh", "-c", "sleep 7200"]



Create the pod:

student-node ~ ➜  kubectl apply -f alpine-sleeper-cka15-arch.yaml --context cluster3
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 4
info_outline
Question
SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


An etcd backup is already stored at the path /opt/cluster1_backup_to_restore.db on the cluster1-controlplane node. Use /root/default.etcd as the --data-dir and restore it on the cluster1-controlplane node itself.


You can ssh to the controlplane node by running ssh root@cluster1-controlplane from the student-node.

CheckCompleteIncomplete
Q. 5
info_outline
Question
SECTION: ARCHITECTURE, INSTALL AND MAINTENANCE


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


A pod called logger-complete-cka04-arch has been created in the default namespace. Inspect this pod and save ALL the logs to the file /root/logger-complete-cka04-arch on the student-node.


info_outline
Solution
Run the command kubectl logs logger-complete-cka04-arch --context cluster3 > /root/logger-complete-cka04-arch on the student-node.

Run the command :

student-node ~ ➜ kubectl logs logger-complete-cka04-arch --context cluster3 > /root/logger-complete-cka04-arch

student-node ~ ➜  head /root/logger-complete-cka04-arch
INFO: Wed Oct 19 10:50:54 UTC 2022 Logger is running
CRITICAL: Wed Oct 19 10:50:54 UTC 2022 Logger encountered errors!
SUCCESS: Wed Oct 19 10:50:54 UTC 2022 Logger re-started!
INFO: Wed Oct 19 10:50:54 UTC 2022 Logger is running
CRITICAL: Wed Oct 19 10:50:54 UTC 2022 Logger encountered errors!
SUCCESS: Wed Oct 19 10:50:54 UTC 2022 Logger re-started!
INFO: Wed Oct 19 10:50:54 UTC 2022 Logger is running

student-node ~ ➜
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 6
info_outline
Question
SECTION: TROUBLESHOOTING


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


A pod called check-time-cka03-trb is continuously crashing. Figure out what is causing this and fix it.




Make sure that the check-time-cka03-trb POD is in running state.

This pod prints the current date and time at a pre-defined frequency and saves it to a file. Ensure that it continues this operation once you have fixed it.

info_outline
Solution
Look into the POD logs
kubectl logs -f check-time-cka03-trb
You may not see any logs so look into the kubernetes events for check-time-cka03-trb POD

Look into the POD events
kubectl get event --field-selector involvedObject.name=check-time-cka03-trb
You will see an error something like:

Error: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "/bin/bash": stat /bin/bash: no such file or directory: unknown
From the error we can see that its not able to execute /bin/bash so let's try /bin/sh

Edit the pod
kubectl get pod check-time-cka03-trb -o=yaml > check-time-cka03-trb.yaml
Make the required changes in check-time-cka03-trb.yaml template
vi check-time-cka03-trb.yaml
Under spec: -> containers: -> command: change /bin/bash to /bin/sh and save the file.
Delete the old pod.
kubectl delete pod check-time-cka03-trb
Apply the updated template
kubectl apply -f check-time-cka03-trb.yaml
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 7
info_outline
Question
SECTION: TROUBLESHOOTING


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


The green-deployment-cka15-trb deployment is having some issues since the corresponding POD is crashing and restarting multiple times continuously.


Investigate the issue and fix it, make sure the POD is in running state and its stable (i.e NO RESTARTS!).

info_outline
Solution
List the pods to check its status
kubectl get pod
its must have crashed already so lets look into the logs.

kubectl logs -f green-deployment-cka15-trb-xxxx
You will see some logs like these

2022-09-18 17:13:25 98 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
2022-09-18 17:13:25 98 [Note] InnoDB: Memory barrier is not used
2022-09-18 17:13:25 98 [Note] InnoDB: Compressed tables use zlib 1.2.11
2022-09-18 17:13:25 98 [Note] InnoDB: Using Linux native AIO
2022-09-18 17:13:25 98 [Note] InnoDB: Using CPU crc32 instructions
2022-09-18 17:13:25 98 [Note] InnoDB: Initializing buffer pool, size = 128.0M
Killed
This might be due to the resources issue, especially the memory, so let's try to recreate the POD to see if it helps.

kubectl delete pod green-deployment-cka15-trb-xxxx
Now watch closely the POD status

kubectl get pod
Pretty soon you will see the POD status has been changed to OOMKilled which confirms its the memory issue. So let's look into the resources that are assigned to this deployment.

kubectl get deploy
kubectl edit deploy green-deployment-cka15-trb
Under resources: -> limits: change memory from 256Mi to 512Mi and save the changes.
Now watch closely the POD status again

kubectl get pod
It should be stable now.

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 8
info_outline
Question
SECTION: TROUBLESHOOTING


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


The deployment called web-dp-cka17-trb has 0 out of 1 pods up and running. Troubleshoot this issue and fix it. Make sure all required POD(s) are in running state and stable (not restarting).

The application runs on port 80 inside the container and is exposed on the node port 30090.

info_outline
Solution
List out the PODs
kubectl get pod
Let's look into the relevant events:

kubectl get event --field-selector involvedObject.name=<pod-name>
You should see some errors as below:

Warning   FailedScheduling   pod/web-dp-cka17-trb-9bdd6779-fm95t   0/3 nodes are available: 3 persistentvolumeclaim "web-pvc-cka17-trbl" not found. preemption: 0/3 nodes are available: 3 Preemption is not helpful for scheduling.
From the error we can see that its something related to the PVCs. So let' look into that.

kubectl get pv
kubectl get pvc
You will notice that web-pvc-cka17-trb is stuck in pending and also the capacity of web-pv-cka17-trb volume is 100Mi.
Now let's dig more into the PVC:

kubectl get pvc web-pvc-cka17-trb -o yaml
Notice the storage which is 150Mi which means its trying to claim 150Mi of storage from a 100Mi PV. So let's edit this PV.

kubectl edit pv web-pv-cka17-trb
Change storage: 100Mi to storage: 150Mi
Check again the pvc
kubectl get pvc
web-pvc-cka17-trb should be good now. let's see the PODs

kubectl get pod
POD should not be in pending state now but it must be crashing with Init:CrashLoopBackOff status, which means somehow the init container is crashing. So let's check the logs.

kubectl get event --field-selector involvedObject.name=<pod-name>
You should see someting like

Warning   Failed      pod/web-dp-cka17-trb-67c9bdcd85-4tvpr   Error: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "/bin/bsh\\": stat /bin/bsh\: no such file or directory: unknown
Let's look into the deployment:

kubectl edit deploy web-dp-cka17-trb
Under initContainers: -> - command: change /bin/bsh\ to /bin/bash
let's see the PODs
kubectl get pod
Wait for some time to make sure it is stable, but you will notice that its restart so still something must be wrong.

So let's check the events again.

kubectl get event --field-selector involvedObject.name=<pod-name>
You should see someting like

Warning   Unhealthy   pod/web-dp-cka17-trb-647f69f8bd-67xmx   Liveness probe failed: Get "http://10.50.64.1:81/": dial tcp 10.50.64.1:81: connect: connection refused
Seems like its not able to connect to a service, let's look into the deployment to understand

kubectl edit deploy web-dp-cka17-trb
Notice that containerPort: 80 but under livenessProbe: the port: 81 so seems like livenessProbe is using wrong port. let's change port: 81 to port: 80

See the PODs now

kubectl get pod
It should be good now.

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 9
info_outline
Question
SECTION: TROUBLESHOOTING


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


The purple-app-cka27-trb pod is an nginx based app on the container port 80. This app is exposed within the cluster using a ClusterIP type service called purple-svc-cka27-trb.


There is another pod called purple-curl-cka27-trb which continuously monitors the status of the app running within purple-app-cka27-trb pod by accessing the purple-svc-cka27-trb service using curl.


Recently we started seeing some errors in the logs of the purple-curl-cka27-trb pod.


Dig into the logs to identify the issue and make sure it is resolved.


Note: You will not be able to access this app directly from the student-node but you can exec into the purple-app-cka27-trb pod to check.

info_outline
Solution
Check the purple-curl-cka27-trb pod logs

kubectl logs purple-curl-cka27-trb
You will see some logs as below

Not able to connect to the nginx app on http://purple-svc-cka27-trb
Now to debug let's try to access this app from within the purple-app-cka27-trb pod

kubectl exec -it purple-app-cka27-trb -- bash
curl http://purple-svc-cka27-trb
exit
You will notice its stuck, so app is not reachable. Let's look into the service to see its configured correctly.

kubectl edit svc purple-svc-cka27-trb
Under ports: -> port: and targetPort: is set to 8080 but nginx default port is 80 so change 8080 to 80 and save the changes
Let's check the logs now

kubectl logs purple-curl-cka27-trb
You will see Thank you for using nginx. in the output now.

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 10
info_outline
Question
SECTION: TROUBLESHOOTING


For this question, please set the context to cluster4 by running:


kubectl config use-context cluster4


There is a pod called pink-pod-cka16-trb created in the default namespace in cluster4. This app runs on port tcp/5000 and it is exposed to end-users using an ingress resource called pink-ing-cka16-trb in such a way that it is supposed to be accessible using the command: curl http://kodekloud-pink.app on cluster4-controlplane host.


However, this is not working. Troubleshoot and fix this issue, making any necessary to the objects.



Note: You should be able to ssh into the cluster4-controlplane using ssh cluster4-controlplane command.

info_outline
Solution
SSH into the cluster4-controlplane host and try to access the app.
ssh cluster4-controlplane
curl kodekloud-pink.app
You must be getting 503 Service Temporarily Unavailabl error.
Let's look into the service:

kubectl edit svc pink-svc-cka16-trb
Under ports: change protocol: UDP to protocol: TCP
Try to access the app again

curl kodekloud-pink.app
You must be getting curl: (6) Could not resolve host: example.com error, from the error we can see that its not able to resolve example.com host which indicated that it can be some issue related to the DNS. As we know CoreDNS is a DNS server that can serve as the Kubernetes cluster DNS, so it can be something related to CoreDNS.

Let's check if we have CoreDNS deployment running:

kubectl get deploy -n kube-system
You will see that for coredns all relicas are down, you will see 0/0 ready pods. So let's scale up this deployment.

kubectl scale --replicas=2 deployment coredns -n kube-system
Once CoreDBS is up let's try to access to app again.

curl kodekloud-pink.app
It should work now.

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 11
info_outline
Question
SECTION: TROUBLESHOOTING


For this question, please set the context to cluster2 by running:


kubectl config use-context cluster2


The yello-cka20-trb pod is stuck in a Pending state. Fix this issue and get it to a running state. Recreate the pod if necessary.

Do not remove any of the existing taints that are set on the cluster nodes.


info_outline
Solution
Let's check the POD status
kubectl get pod --context=cluster2
So you will see that yello-cka20-trb pod is in Pending state. Let's check out the relevant events.

kubectl get event --field-selector involvedObject.name=yello-cka20-trb --context=cluster2
You will see some errors like:

Warning   FailedScheduling   pod/yello-cka20-trb   0/2 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/master: }, 1 node(s) had untolerated taint {node: node01}. preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
Notice this error 1 node(s) had untolerated taint {node: node01} so we can see that one of nodes have taints applied. We don't want to remove the node taints and we are not going to re-create the POD so let's look into the POD config if its using any other toleration settings.

kubectl get pod yello-cka20-trb --context=cluster2 -o yaml
You will notice this in the output

tolerations:
  - effect: NoSchedule
    key: node
    operator: Equal
    value: cluster2-node01
Here notice that the value for key node is cluster2-node01 but the node has different value applied i.e node01 so let's update the taints values for the node as needed.

kubectl --context=cluster2 taint nodes cluster2-node01 node=cluster2-node01:NoSchedule --overwrite=true
Let's check the POD status again

kubectl get pod --context=cluster2
It should be in Running state now.

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 12
info_outline
Question
SECTION: SCHEDULING


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


Create a new deployment called ocean-tv-wl09 in the default namespace using the image kodekloud/webapp-color:v1.
Use the following specs for the deployment:


1. Replica count should be 3.

2. Set the Max Unavailable to 40% and Max Surge to 55%.

3. Create the deployment and ensure all the pods are ready.

4. After successful deployment, upgrade the deployment image to kodekloud/webapp-color:v2 and inspect the deployment rollout status.

5. Check the rolling history of the deployment and on the student-node, save the current revision count number to the /opt/revision-count.txt file.

6. Finally, perform a rollback and revert back the deployment image to the older version.



info_outline
Solution
Set the correct context: -

kubectl config use-context cluster1


Use the following template to create a deployment called ocean-tv-wl09: -

---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: ocean-tv-wl09
  name: ocean-tv-wl09
spec:
  replicas: 3
  selector:
    matchLabels:
      app: ocean-tv-wl09
  strategy: 
   type: RollingUpdate
   rollingUpdate:
     maxUnavailable: 40%
     maxSurge: 55%
  template:
    metadata:
      labels:
        app: ocean-tv-wl09
    spec:
      containers:
      - image: kodekloud/webapp-color:v1
        name: webapp-color


Now, create the deployment by using the kubectl create -f command in the default namespace: -

kubectl create -f <FILE-NAME>.yaml


After sometime, upgrade the deployment image to kodekloud/webapp-color:v2: -

kubectl set image deploy ocean-tv-wl09 webapp-color=kodekloud/webapp-color:v2


And check out the rollout history of the deployment ocean-tv-wl09: -

kubectl rollout history deploy ocean-tv-wl09
deployment.apps/ocean-tv-wl09 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>


NOTE: - Revision count is 2. In your lab, it could be different.



On the student-node, store the revision count to the given file: -

echo "2" > /opt/revision-count.txt


In final task, rollback the deployment image to an old version: -

kubectl rollout undo deployment ocean-tv-wl09


Verify the image name by using the following command: -

kubectl describe deploy ocean-tv-wl09


It should be kodekloud/webapp-color:v1 image.

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 13
info_outline
Question
SECTION: SCHEDULING


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


A manifest file is available at the /root/app-wl03/ on the student-node node. There are some issues with the file; hence couldn't deploy a pod on the cluster3-controlplane node.

After fixing the issues, deploy the pod, and it should be in a running state.


NOTE: - Ensure that the existing limits are unchanged.


info_outline
Solution
Set the correct context: -

kubectl config use-context cluster3


Use the cd command to move to the given directory: -

cd /root/app-wl03/
While creating the resource, you will see the error output as follows: -

kubectl create -f app-wl03.yaml 
The Pod "app-wl03" is invalid: spec.containers[0].resources.requests: Invalid value: "1Gi": must be less than or equal to memory limit
In the spec.containers.resources.requests.memory value is not configured as compare to the memory limit.

As a fix, open the manifest file with the text editor such as vim or nano and set the value to 100Mi or less than 100Mi.

It should be look like as follows: -

resources:
     requests:
       memory: 100Mi
     limits:
       memory: 100Mi
Final, create the resource from the kubectl create command: -

kubectl create -f app-wl03.yaml 
pod/app-wl03 created
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 14
info_outline
Question
SECTION: SCHEDULING


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


We have deployed a simple web application called frontend-wl04 on cluster1. This version of the application has some issues from a security point of view and needs to be updated to version 2.


Update the image and wait for the application to fully deploy.


You can verify the running application using the curl command on the terminal:

student-node ~ ➜  curl http://cluster1-node01:30080
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #2980b9;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  <h1>Hello from frontend-wl04-84fc69bd96-p7rbl!</h1>



  <h2>
    Application Version: v1
  </h2>


</div>
student-node ~ ➜ 


Version 2 Image details as follows:

1. Current version of the image is `v1`, we need to update with the image to kodekloud/webapp-color:v2.
2. Use the imperative command to update the image.




info_outline
Solution
Set the context: -

kubectl config use-context cluster1


Now, test the current version of the application as follows:

student-node ~ ➜  curl http://cluster1-node01:30080
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #2980b9;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  <h1>Hello from frontend-wl04-84fc69bd96-p7rbl!</h1>



  <h2>
    Application Version: v1
  </h2>


</div>
student-node ~ ➜


Let's update the image, First, run the below command to check the existing image: -

kubectl get deploy frontend-wl04 -oyaml | grep -i image 


After checking the existing image, we have to use the imperative command (It will take less than a minute) to update the image: -

kubectl set image deploy frontend-wl04 simple-webapp=kodekloud/webapp-color:v2


Finally, run the below command to check the updated image: -

kubectl get deploy frontend-wl04 -oyaml | grep -i image 


It should be the kodekloud/webapp-color:v2 image and the same should be visible when you run the curl command again:

student-node ~ ➜  curl http://cluster1-node01:30080
<!doctype html>
<title>Hello from Flask</title>
<body style="background: #16a085;"></body>
<div style="color: #e4e4e4;
    text-align:  center;
    height: 90px;
    vertical-align:  middle;">

  <h1>Hello from frontend-wl04-6c54f479df-5tddd!</h1>



  <h2>
    Application Version: v2
  </h2>


</div>
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 15
info_outline
Question
SECTION: STORAGE


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


We want to deploy a python based application on the cluster using a template located at /root/olive-app-cka10-str.yaml on student-node. However, before you proceed we need to make some modifications to the YAML file as per details given below:


The YAML should also contain a persistent volume claim with name olive-pvc-cka10-str to claim a 100Mi of storage from olive-pv-cka10-str PV.


Update the deployment to add a sidecar container, which can use busybox image (you might need to add a sleep command for this container to keep it running.)

Share the python-data volume with this container and mount the same at path /usr/src. Make sure this container only has read permissions on this volume.


Finally, create a pod using this YAML and make sure the POD is in Running state.
info_outline
Solution
Update olive-app-cka10-str.yaml template so that it looks like as below:

---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: olive-pvc-cka10-str
spec:
  accessModes:
  - ReadWriteMany
  storageClassName: olive-stc-cka10-str
  volumeName: olive-pv-cka10-str
  resources:
    requests:
      storage: 100Mi

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: olive-app-cka10-str
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: olive-app-cka10-str
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: kubernetes.io/hostname
                operator: In
                values:
                  - cluster1-node01
      containers:
      - name: python
        image: poroko/flask-demo-app
        ports:
        - containerPort: 5000
        volumeMounts:
        - name: python-data
          mountPath: /usr/share/
      - name: busybox
        image: busybox
        command:
          - "bin/sh"
          - "-c"
          - "sleep 10000"
        volumeMounts:
          - name: python-data
            mountPath: "/usr/src"
            readOnly: true
      volumes:
      - name: python-data
        persistentVolumeClaim:
          claimName: olive-pvc-cka10-str
  selector:
    matchLabels:
      app: olive-app-cka10-str

---
apiVersion: v1
kind: Service
metadata:
  name: olive-svc-cka10-str
spec:
  type: NodePort
  ports:
    - port: 5000
      nodePort: 32006
  selector:
    app: olive-app-cka10-str
Apply the template:

kubectl apply -f olive-app-cka10-str.yaml
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 16
info_outline
Question
SECTION: STORAGE


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


Create a storage class with the name banana-sc-cka08-str as per the properties given below:


- Provisioner should be kubernetes.io/no-provisioner,

- Volume binding mode should be WaitForFirstConsumer.

- Volume expansion should be enabled.


info_outline
Solution
Create a yaml template as below:
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: banana-sc-cka08-str
provisioner: kubernetes.io/no-provisioner
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
Apply the template:
kubectl apply -f <template-file-name>.yaml
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 17
info_outline
Question
SECTION: SERVICE NETWORKING


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


There is a deployment nginx-deployment-cka04-svcn in cluster3 which is exposed using service nginx-service-cka04-svcn.



Create an ingress resource nginx-ingress-cka04-svcn to load balance the incoming traffic with the following specifications:


pathType: Prefix and path: /

Backend Service Name: nginx-service-cka04-svcn

Backend Service Port: 80

ssl-redirect is set to false
info_outline
Solution
First change the context to "cluster3":



student-node ~ ➜  kubectl config use-context cluster3
Switched to context "cluster3".



Now apply the ingress resource with the given requirements:



kubectl apply -f - << EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress-cka04-svcn
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service-cka04-svcn
            port:
              number: 80
EOF



Check if the ingress resource was successfully created:



student-node ~ ➜  kubectl get ingress
NAME                       CLASS    HOSTS   ADDRESS       PORTS   AGE
nginx-ingress-cka04-svcn   <none>   *       172.25.0.10   80      13s



As the ingress controller is exposed on cluster3-controlplane using traefik service, we need to ssh to cluster3-controlplane first to check if the ingress resource works properly:



student-node ~ ➜  ssh cluster3-controlplane

cluster3-controlplane:~# curl -I 172.25.0.11
HTTP/1.1 200 OK
...
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 18
info_outline
Question
SECTION: SERVICE NETWORKING


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


Create a ReplicaSet with name checker-cka10-svcn in ns-12345-svcn namespace with image registry.k8s.io/e2e-test-images/jessie-dnsutils:1.3.


Make sure to specify the below specs as well:


command sleep 3600
replicas set to 2
container name: dns-image



Once the checker pods are up and running, store the output of the command nslookup kubernetes.default from any one of the checker pod into the file /root/dns-output-12345-cka10-svcn on student-node.

info_outline
Solution
Change to the cluster4 context before attempting the task:

kubectl config use-context cluster3



Create the ReplicaSet as per the requirements:



kubectl apply -f - << EOF
---
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: null
  name: ns-12345-svcn
spec: {}
status: {}

---
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: checker-cka10-svcn
  namespace: ns-12345-svcn
  labels:
    app: dns
    tier: testing
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: testing
  template:
    metadata:
      labels:
        tier: testing
    spec:
      containers:
      - name: dns-image
        image: registry.k8s.io/e2e-test-images/jessie-dnsutils:1.3
        command:
          - sleep
          - "3600"
EOF



Now let's test if the nslookup command is working :


student-node ~ ➜  k get pods -n ns-12345-svcn 
NAME                       READY   STATUS    RESTARTS   AGE
checker-cka10-svcn-d2cd2   1/1     Running   0          12s
checker-cka10-svcn-qj8rc   1/1     Running   0          12s

student-node ~ ➜  POD_NAME=`k get pods -n ns-12345-svcn --no-headers | head -1 | awk '{print $1}'`

student-node ~ ➜  kubectl exec -n ns-12345-svcn -i -t $POD_NAME -- nslookup kubernetes.default
;; connection timed out; no servers could be reached

command terminated with exit code 1



There seems to be a problem with the name resolution. Let's check if our coredns pods are up and if any service exists to reach them:



student-node ~ ➜  k get pods -n kube-system | grep coredns
coredns-6d4b75cb6d-cprjz                        1/1     Running   0             42m
coredns-6d4b75cb6d-fdrhv                        1/1     Running   0             42m

student-node ~ ➜  k get svc -n kube-system 
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   62m



Everything looks okay here but the name resolution problem exists, let's see if the kube-dns service have any active endpoints:

student-node ~ ➜  kubectl get ep -n kube-system kube-dns 
NAME       ENDPOINTS   AGE
kube-dns   <none>      63m



Finally, we have our culprit.


If we dig a little deeper, we will it is using wrong labels and selector:



student-node ~ ➜  kubectl describe svc -n kube-system kube-dns 
Name:              kube-dns
Namespace:         kube-system
....
Selector:          k8s-app=core-dns
Type:              ClusterIP
...

student-node ~ ➜  kubectl get deploy -n kube-system --show-labels | grep coredns
coredns   2/2     2            2           66m   k8s-app=kube-dns



Let's update the kube-dns service it to point to correct set of pods:



student-node ~ ➜  kubectl patch service -n kube-system kube-dns -p '{"spec":{"selector":{"k8s-app": "kube-dns"}}}'
service/kube-dns patched

student-node ~ ➜  kubectl get ep -n kube-system kube-dns 
NAME       ENDPOINTS                                              AGE
kube-dns   10.50.0.2:53,10.50.192.1:53,10.50.0.2:53 + 3 more...   69m



NOTE: We can use any method to update kube-dns service. In our case, we have used kubectl patch command.




Now let's store the correct output to /root/dns-output-12345-cka10-svcn:



student-node ~ ➜  kubectl exec -n ns-12345-svcn -i -t $POD_NAME -- nslookup kubernetes.default
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   kubernetes.default.svc.cluster.local
Address: 10.96.0.1


student-node ~ ➜  kubectl exec -n ns-12345-svcn -i -t $POD_NAME -- nslookup kubernetes.default > /root/dns-output-12345-cka10-svcn
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 19
info_outline
Question
SECTION: SERVICE NETWORKING


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


Create a deployment named hr-web-app-cka08-svcn using the image kodekloud/webapp-color with 2 replicas.



Expose the hr-web-app-cka08-svcn as service hr-web-app-service-cka08-svcn application on port 30082 on the nodes of the cluster.

The web application listens on port 8080.



info_outline
Solution
Switch to cluster3 :



kubectl config use-context cluster3



On student-node, use the command: kubectl create deployment hr-web-app-cka08-svcn --image=kodekloud/webapp-color --replicas=2



Now we can run the command: kubectl expose deployment hr-web-app-cka08-svcn --type=NodePort --port=8080 --name=hr-web-app-service-cka08-svcn --dry-run=client -o yaml > hr-web-app-service-cka08-svcn.yaml to generate a service definition file.




Now, in generated service definition file add the nodePort field with the given port number under the ports section and create a service.

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 20
info_outline
Question
SECTION: SERVICE NETWORKING


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


Create a loadbalancer service with name wear-service-cka09-svcn to expose the deployment webapp-wear-cka09-svcn application in app-space namespace.

info_outline
Solution
Switch to cluster3 :



kubectl config use-context cluster3



On student node run the command:


student-node ~ ➜  kubectl expose -n app-space deployment webapp-wear-cka09-svcn --type=LoadBalancer --name=wear-service-cka09-svcn --port=8080
service/wear-service-cka09-svcn exposed

student-node ~ ➜  k get svc -n app-space
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
wear-service-cka09-svcn   LoadBalancer   10.43.68.233   172.25.0.14   8080:32109/TCP   14s
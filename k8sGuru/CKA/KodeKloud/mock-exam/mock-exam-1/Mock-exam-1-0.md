Q. 9
info_outline
Question
SECTION: TROUBLESHOOTING


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


The blue-dp-cka09-trb deployment is having 0 out of 1 pods running. Fix the issue to make sure that pod is up and running.

info_outline
Solution
List the pods
kubectl get pod
Most probably you see Init:Error or Init:CrashLoopBackOff for the corresponding pod.

Look into the logs
kubectl logs blue-dp-cka09-trb-xxxx -c init-container
You will see an error something like

sh: can't open 'echo 'Welcome!'': No such file or directory
Edit the deployment
kubectl edit deploy blue-dp-cka09-trb
Under initContainers: -> - command: add -c to the next line of - sh, so final command should look like this
   initContainers:
   - command:
     - sh
     - -c
     - echo 'Welcome!'
If you will check pod then it must be failing again but with different error this time, let's find that out

kubectl get event --field-selector involvedObject.name=blue-dp-cka09-trb-xxxxx
You will see an error something like

Warning   Failed      pod/blue-dp-cka09-trb-69dd844f76-rv9z8   Error: failed to create containerd task: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: error mounting "/var/lib/kubelet/pods/98182a41-6d6d-406a-a3e2-37c33036acac/volumes/kubernetes.io~configmap/nginx-config" to rootfs at "/etc/nginx/nginx.conf": mount /var/lib/kubelet/pods/98182a41-6d6d-406a-a3e2-37c33036acac/volumes/kubernetes.io~configmap/nginx-config:/etc/nginx/nginx.conf (via /proc/self/fd/6), flags: 0x5001: not a directory: unknown
Edit the deployment again
kubectl edit deploy blue-dp-cka09-trb
Under volumeMounts: -> - mountPath: /etc/nginx/nginx.conf -> name: nginx-config add subPath: nginx.conf and save the changes.
Finally the pod should be in running state.
-------------------------------------------------------------------------------------------
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 10
info_outline
Question
SECTION: TROUBLESHOOTING


For this question, please set the context to cluster4 by running:


kubectl config use-context cluster4


cluster4-node01 node that belongs to cluster4 seems to be in the NotReady state. Fix the issue and make sure this node is in Ready state.



Note: You can ssh into the node using ssh cluster4-node01.

info_outline
Solution
SSH into the cluster4-node01 and check if kubelet service is running
ssh cluster4-node01
systemctl status kubelet
You will see its inactive, so try to start it.

systemctl start kubelet
Check again the status

systemctl status kubelet
Its still failing, so let's look into some latest error logs:

journalctl -u kubelet --since "30 min ago" | grep 'Error:'
You will see some errors as below:

cluster4-node01 kubelet[6301]: Error: failed to construct kubelet dependencies: unable to load client CA file /etc/kubernetes/pki/CA.crt: open /etc/kubernetes/pki/CA.crt: no such file or directory
Check if /etc/kubernetes/pki/CA.crt file exists:

ls /etc/kubernetes/pki/
You will notice that the file name is ca.crt instead of CA.crt so possibly kubelet is looking for a wrong file. Let's fix the config:

vi /var/lib/kubelet/config.yaml
  
Change clientCAFile from /etc/kubernetes/pki/CA.crt to /etc/kubernetes/pki/ca.crt
Try to start it again

systemctl start kubelet
Service should start now but there might be an error as below

ReportingIn
stance:""}': 'Post "https://cluster4-controlplane:6334/api/v1/namespaces/default/events": dial tcp 10.9.63.18:633
4: connect: connection refused'(may retry after sleeping)
Sep 18 09:21:47 cluster4-node01 kubelet[6803]: E0918 09:21:47.641184    6803 kubelet.go:2419] "Error getting node
" err="node \"cluster4-node01\" not found"
You must have noticed that its trying to connect to the api server on port 6334 but the default port for kube-apiserver is 6443. Let's fix this:

Edit the kubelet config
vi /etc/kubernetes/kubelet.conf
    
Change server
server: https://cluster4-controlplane:6334
to

server: https://cluster4-controlplane:6443
Finally restart kublet service
systemctl restart kubelet
Check from the student-node now and cluster4-node01 should be ready now.

kubectl get node --context=cluster4
-------------------------------------------------------------------------------------------

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 11
info_outline
Question
SECTION: TROUBLESHOOTING


For this question, please set the context to cluster2 by running:


kubectl config use-context cluster2


The cat-cka22-trb pod is stuck in Pending state. Look into the issue to fix the same. Make sure that the pod is in running state and its stable (i.e not restarting or crashing).


Note: Do not make any changes to the pod (No changes to pod config but you may destory and re-create).

info_outline
Solution
Let's check the POD status
kubectl get pod
You will see that cat-cka22-trb pod is stuck in Pending state. So let's try to look into the events

kubectl --context cluster2 get event --field-selector involvedObject.name=cat-cka22-trb
You will see some logs as below

Warning   FailedScheduling   pod/cat-cka22-trb   0/3 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/master: }, 2 node(s) didn't match Pod's node affinity/selector. preemption: 0/2 nodes are available: 3 Preemption is not helpful for scheduling.
So seems like this POD is using the node affinity, let's look into the POD to understand the node affinity its using.

kubectl --context cluster2 get pod cat-cka22-trb -o yaml
Under affinity: you will see its looking for key: node and values: cluster2-node02 so let's verify if node01 has these labels applied.

kubectl --context cluster2 get node cluster2-node01 -o yaml
Look under labels: and you will not find any such label, so let's add this label to this node.

kubectl label node cluster2-node01 node=cluster2-node01
Check again the node details

kubectl get node cluster2-node01 -o yaml
The new label should be there, let's see if POD is scheduled now on this node

kubectl --context cluster2 get pod
Its is but it must be crashing or restarting, so let's look into the pod logs

kubectl --context cluster2 logs -f cat-cka22-trb
You will see logs as below:

The HOST variable seems incorrect, it must be set to kodekloud
Let's look into the POD env variables to see if there is any HOST env variable

kubectl --context cluster2 get pod -o yaml
Under env: you will see this

env:
- name: HOST
  valueFrom:
    secretKeyRef:
      key: hostname
      name: cat-cka22-trb
So we can see that HOST variable is defined and its value is being retrieved from a secret called "cat-cka22-trb". Let's look into this secret.

kubectl --context cluster2 get secret
kubectl --context cluster2 get secret cat-cka22-trb -o yaml
You will find a key/value pair under data:, let's try to decode it to see its value:

echo "<the decoded value you see for hostname" | base64 -d
ok so the value is set to kodekloude which is incorrect as it should be set to kodekloud. So let's update the secret:

echo "kodekloud" | base64
kubectl edit secret cat-cka22-trb
Change requests storage hostname: a29kZWtsb3Vkdg== to hostname: a29kZWtsb3VkCg== (values may vary)
POD should be good now.
-------------------------------------------------------------------------------------------

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 12
info_outline
Question
SECTION: SCHEDULING


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


One of our Junior DevOps engineers have deployed a pod nginx-wl06 on the cluster3-controlplane node. However, while specifying the resource limits, instead of using Mebibyte as the unit, Gebibyte was used.

As a result, the node doesn't have sufficient resources to deploy this pod and it is stuck in a pending state

Fix the units and re-deploy the pod (Delete and recreate the pod if needed).


 

info_outline
Solution
Solution
Set the correct context: -

kubectl config use-context cluster3
Run the following command to check the pending pods on all the namespaces: -

kubectl get pods -A


After that, inspect the pod Events as follows: -

kubectl get pods -A | grep -i pending

kubectl describe po nginx-wl06


Make use of the kubectl edit command to update the values from Gi to Mi:-

kubectl edit po nginx-wl06


It will save the temporary file under the /tmp/ directory. Use the kubectl replace command as follows: -

kubectl replace -f /tmp/kubectl-edit-xxxx.yaml --force


It will delete the existing pod and will re-create it again with new changes.
-------------------------------------------------------------------------------------------

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 13
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
-------------------------------------------------------------------------------------------

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 14
info_outline
Question
SECTION: SCHEDULING


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


One of our applications runs on the cluster3-controlplane node. Due to the possibility of traffic increase, we want to scale the application pods to loadbalance the traffic and provide a smoother user experience.

cluster3-controlplane node has enough resources to deploy more application pods. Scale the deployment called essports-wl02 to 5.


info_outline
Solution

-------------------------------------------------------------------------------------------

CheckCompleteIncomplete
format_list_bulleted
Details
Q. 15
info_outline
Question
SECTION: STORAGE


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


There is a persistent volume named apple-pv-cka04-str. Create a persistent volume claim named apple-pvc-cka04-str and request a 40Mi of storage from apple-pv-cka04-str PV.
The access mode should be ReadWriteOnce and storage class should be manual.

info_outline
Solution
Set context to cluster1:

Create a yaml template as below:
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: apple-pvc-cka04-str
spec:
  volumeName: apple-pv-cka04-str
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 40Mi
Apply the template:
kubectl apply -f <template-file-name>.yaml
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 16
info_outline
Question
SECTION: STORAGE


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


A persistent volume called papaya-pv-cka09-str is already created with a storage capacity of 150Mi. It's using the papaya-stc-cka09-str storage class with the path /opt/papaya-stc-cka09-str.


Also, a persistent volume claim named papaya-pvc-cka09-str has also been created on this cluster. This PVC has requested 50Mi of storage from papaya-pv-cka09-str volume.


Resize the PVC to 80Mi and make sure the PVC is in Bound state.


info_outline
Solution
Edit papaya-pv-cka09-str PV:

kubectl get pv papaya-pv-cka09-str -o yaml > /tmp/papaya-pv-cka09-str.yaml
Edit the template:

vi /tmp/papaya-pv-cka09-str.yaml
Delete all entries for uid:, annotations, status:, claimRef: from the template.
Edit papaya-pvc-cka09-str PVC:

kubectl get pvc papaya-pvc-cka09-str -o yaml > /tmp/papaya-pvc-cka09-str.yaml
Edit the template:

vi /tmp/papaya-pvc-cka09-str.yaml
Under resources: -> requests: change storage: 50Mi to storage: 80Mi and save the template.
Delete the exsiting PVC:

kubectl delete pvc papaya-pvc-cka09-str
Delete the exsiting PV and create using the template:

kubectl delete pv papaya-pv-cka09-str
kubectl apply -f /tmp/papaya-pv-cka09-str.yaml
Create the PVC using template:

kubectl apply -f /tmp/papaya-pvc-cka09-str.yaml
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 17
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
Q. 18
info_outline
Question
SECTION: SERVICE NETWORKING


For this question, please set the context to cluster1 by running:


kubectl config use-context cluster1


John is setting up a two tier application stack that is supposed to be accessible using the service curlme-cka01-svcn. To test that the service is accessible, he is using a pod called curlpod-cka01-svcn. However, at the moment, he is unable to get any response from the application.



Troubleshoot and fix this issue so the application stack is accessible.



While you may delete and recreate the service curlme-cka01-svcn, please do not alter it in anyway.

info_outline
Solution
Test if the service curlme-cka01-svcn is accessible from pod curlpod-cka01-svcn or not.


kubectl exec curlpod-cka01-svcn -- curl curlme-cka01-svcn

.....
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:--  0:00:10 --:--:--     0


We did not get any response. Check if the service is properly configured or not.


kubectl describe svc curlme-cka01-svcn ''

....
Name:              curlme-cka01-svcn
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          run=curlme-ckaO1-svcn
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.109.45.180
IPs:               10.109.45.180
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         <none>
Session Affinity:  None
Events:            <none>


The service has no endpoints configured. As we can delete the resource, let's delete the service and create the service again.

To delete the service, use the command kubectl delete svc curlme-cka01-svcn.
You can create the service using imperative way or declarative way.


Using imperative command:
kubectl expose pod curlme-cka01-svcn --port=80


Using declarative manifest:


apiVersion: v1
kind: Service
metadata:
  labels:
    run: curlme-cka01-svcn
  name: curlme-cka01-svcn
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: curlme-cka01-svcn
  type: ClusterIP


You can test the connection from curlpod-cka-1-svcn using following.


kubectl exec curlpod-cka01-svcn -- curl curlme-cka01-svcn
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 19
info_outline
Question
SECTION: SERVICE NETWORKING


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


We have an external webserver running on student-node which is exposed at port 9999. We have created a service called external-webserver-cka03-svcn that can connect to our local webserver from within the kubernetes cluster3 but at the moment it is not working as expected.



Fix the issue so that other pods within cluster3 can use external-webserver-cka03-svcn service to access the webserver.


info_outline
Solution
Let's check if the webserver is working or not:


student-node ~ ➜  curl student-node:9999
...
<h1>Welcome to nginx!</h1>
...



Now we will check if service is correctly defined:

student-node ~ ➜  kubectl describe svc external-webserver-cka03-svcn 
Name:              external-webserver-cka03-svcn
Namespace:         default
.
.
Endpoints:         <none> # there are no endpoints for the service
...



As we can see there is no endpoints specified for the service, hence we won't be able to get any output. Since we can not destroy any k8s object, let's create the endpoint manually for this service as shown below:


student-node ~ ➜  export IP_ADDR=$(ifconfig eth0 | grep inet | awk '{print $2}')

student-node ~ ➜ kubectl --context cluster3 apply -f - <<EOF
apiVersion: v1
kind: Endpoints
metadata:
  # the name here should match the name of the Service
  name: external-webserver-cka03-svcn
subsets:
  - addresses:
      - ip: $IP_ADDR
    ports:
      - port: 9999
EOF



Finally check if the curl test works now:

student-node ~ ➜  kubectl --context cluster3 run --rm  -i test-curl-pod --image=curlimages/curl --restart=Never -- curl -m 2 external-webserver-cka03-svcn
...
<title>Welcome to nginx!</title>
...
CheckCompleteIncomplete
format_list_bulleted
Details
Q. 20
info_outline
Question
SECTION: SERVICE NETWORKING


For this question, please set the context to cluster3 by running:


kubectl config use-context cluster3


Part I:



Create a ClusterIP service .i.e. service-3421-svcn in the spectra-1267 ns which should expose the pods namely pod-23 and pod-21 with port set to 8080 and targetport to 80.



Part II:



Store the pod names and their ip addresses from the spectra-1267 ns at /root/pod_ips_cka05_svcn where the output is sorted by their IP's.

Please ensure the format as shown below:



POD_NAME        IP_ADDR
pod-1           ip-1
pod-3           ip-2
pod-2           ip-3
...



info_outline
Solution
Switching to cluster3:



kubectl config use-context cluster3



The easiest way to route traffic to a specific pod is by the use of labels and selectors . List the pods along with their labels:



student-node ~ ➜  kubectl get pods --show-labels -n spectra-1267
NAME     READY   STATUS    RESTARTS   AGE     LABELS
pod-12   1/1     Running   0          5m21s   env=dev,mode=standard,type=external
pod-34   1/1     Running   0          5m20s   env=dev,mode=standard,type=internal
pod-43   1/1     Running   0          5m20s   env=prod,mode=exam,type=internal
pod-23   1/1     Running   0          5m21s   env=dev,mode=exam,type=external
pod-32   1/1     Running   0          5m20s   env=prod,mode=standard,type=internal
pod-21   1/1     Running   0          5m20s   env=prod,mode=exam,type=external



Looks like there are a lot of pods created to confuse us. But we are only concerned with the labels of pod-23 and pod-21.



As we can see both the required pods have labels mode=exam,type=external in common. Let's confirm that using kubectl too:



student-node ~ ➜  kubectl get pod -l mode=exam,type=external -n spectra-1267                                    
NAME     READY   STATUS    RESTARTS   AGE
pod-23   1/1     Running   0          9m18s
pod-21   1/1     Running   0          9m17s



Nice!! Now as we have figured out the labels, we can proceed further with the creation of the service:



student-node ~ ➜  kubectl create service clusterip service-3421-svcn -n spectra-1267 --tcp=8080:80 --dry-run=client -o yaml > service-3421-svcn.yaml



Now modify the service definition with selectors as required before applying to k8s cluster:



student-node ~ ➜  cat service-3421-svcn.yaml 
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: service-3421-svcn
  name: service-3421-svcn
  namespace: spectra-1267
spec:
  ports:
  - name: 8080-80
    port: 8080
    protocol: TCP
    targetPort: 80
  selector:
    app: service-3421-svcn  # delete 
    mode: exam    # add
    type: external  # add
  type: ClusterIP
status:
  loadBalancer: {}



Finally let's apply the service definition:



student-node ~ ➜  kubectl apply -f service-3421-svcn.yaml
service/service-3421 created

student-node ~ ➜  k get ep service-3421-svcn -n spectra-1267
NAME           ENDPOINTS                     AGE
service-3421   10.42.0.15:80,10.42.0.17:80   52s



To store all the pod name along with their IP's , we could use imperative command as shown below:



student-node ~ ➜  kubectl get pods -n spectra-1267 -o=custom-columns='POD_NAME:metadata.name,IP_ADDR:status.podIP' --sort-by=.status.podIP

POD_NAME   IP_ADDR
pod-12     10.42.0.18
pod-23     10.42.0.19
pod-34     10.42.0.20
pod-21     10.42.0.21
...

# store the output to /root/pod_ips
student-node ~ ➜  kubectl get pods -n spectra-1267 -o=custom-columns='POD_NAME:metadata.name,IP_ADDR:status.podIP' --sort-by=.status.podIP > /root/pod_ips_cka05_svcn
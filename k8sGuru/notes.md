A `k8s cluster` is a collection of worker machines running containers
`control plane`:
`worker node`
`kubeadm` is a tool that help setting up our k8s cluster


`Pod` is a collection of containers sharing same resources (networking, capacity ...)

```sh
apiVersion: v1 # the k8s API version wr're using
kind: Pod # Specify the type of Object we want to create
metadata:
  name: web-frontend # the name of our object in the cluster
spec: # help to define a list of one or more containers in the pod
  containers: # define a container we will use and the image we want to use
  - name: nginx 
    image: nginx
    ports:
    - containerPort: 80 # expose the port 80
```

## kubernetes cheatsheet
https://kubernetes.io/docs/reference/kubectl/cheatsheet/
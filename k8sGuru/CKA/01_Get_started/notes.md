# Using Namespace in k8s
## What's Namespace?
- Namespace are virtual clusters backed by the same physical cluster.
- K8s objects such as pod and container live in namespaces.
- Namespace are a way to separate and organize objects in your cluster

## Commandes

- list existing namespaces

```sh
kubectl get ns
```

- specify a namespace

this command will get all pods belong to the namespace `my-namespace`
```sh
kubectl get pods --namespace my-namespace
```
or 
```sh
kubectl get pods -n my-namespace
```
or 
to get all pods from all namespaces
```sh
kubectl get pods --all-namespaces
```
- Create Namespace
```sh
kubectl create namespace my-namespace
```

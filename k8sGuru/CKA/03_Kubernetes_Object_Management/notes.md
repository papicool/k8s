# Kubernetes Object Management
## working with Kubectl
- kubectl get

To get list of objects 
```sh
kubectl get <object type> <object name> -o <output> --sort-by <JSONPath> --selector <selector>
```

- kubectl describe

To get details informations
```sh
kubectl describe <object type> <object name>
```

- kubectl create

To create an object from existing file
```sh
kubectl create -f <filename>
```
[!] If you attempt to create an object that already exists, an error will occur.

- kubectl apply

Similar to `kubectl create`. Object will be created if not exist. If it exist. Object will be updated

```sh
kubectl apply -f <file name>
```


- kubectl delete

```sh 
kubectl delete <object type> <object name>
```

- kubectl exec

This command can be use to execute command on the container

```sh
 kubectl exec <pod name> -c <container name> -- <command>
```

## Kubectl Tips for CKA exam
### Imperative or declarative command
When we deploy objects like pod we use:
- imperative commands: define objects using yaml or json file after that we use `kubectl apply -f ...`
- declarative command: define object using kubectl command
    ```sh
    kubectl create deployment my-deployment --image=nginx
    ```
### Quick sample yaml
We can run an imperative command without create object with `--dry-run` option
    ```sh
    kubectl create deployment my-deployment --image=nginx --dry-run -o yaml
    ```

### Record a command
We can record a command that was made to make a change for a later review.
```sh
kubectl scale deployment my-deployment --replicas=5 --record
```
### Use Documentations

## Managing K8s Role-Based Access Control (RBAC)
RBAC allows you tp control what users are allowed to do or access within your cluster

we have two types of RBAC Objects:
- Role: define permissions within a specific single namespace
- ClusterRole: define permissions not specificly for a single namespace

- RoleBinding and ClusterRoleBinding are objects that connect users to `Role` or `ClusterRole`

### Create RBAC file from kubectl
- Create Role
```sh
kubectl create role pod-reader --verb=get,list,watch --resource=pods,pods/logs --dry-run=client -o yaml > pod-reader.yml
```
- Create RoleBinding    
```sh
 kubectl create rolebinding pod-reader --role=pod-reader --user=dev --dry-run=client -o yaml > pod-readerBinding.yml
```
## Creating ServiceAccounts
A Service Accounts is a account used by containers processes within pods to authenticate with k8s API


You can manage access control for service accounts, like others users, using RBAC
### TIPS
Create a service account file

```sh
kubectl create serviceaccount my-serviceaccount --dry-run=client -o yaml > my-serviceaccount.yml
```

Create Role Binding for Service account
```sh
kubectl create rolebinding sa-pod-reader --role=pod-reader --serviceaccount=default:my-serviceaccount --dry-run=client -o yaml > sa-pod-reader.yml
```

## Inspecting Pod Resource Usage
```sh
kubectl top pod
```
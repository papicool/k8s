# Pods and Containers
## Managing Application Configuration

Application Configuration is a way to pass dynamic values to your application at runtimes to control how they behave
They are 2 primary way to do it:
- ConfigMap: Store data in the form of key-value 

    - template config maps
    ```sh
    kubectl create configmap my-configmap --from-literal=key1=value1 --dry-run=client -o yaml > my-configmap.yml
    ```
- Secrets: similar to configmap but store sensitive data

### environment variables
You can pass configmap and secrets data to your container as environment variables.

these variables will be visible to your container process at runtime.

### Configuration volumes
ConfigMap and secrets can be pass to containers in the form of  `mounted volumes`.

In that case, Configuration data appear in files available to the container file system

### Demo:
#### Using ConfigMap and Secrets as Env Variables
- [configmap file](demo/appconfiguration/my-configmap.yml)
- [secret file](demo/appconfiguration/my-secret.yml)
- [pod file](demo/appconfiguration/my-pod.yml)

Check Pod's logs:
```sh
kubectl logs env-prod
```

####  Using ConfigMap and Secrets as Configuration volumes

- [configmap file](demo/appconfiguration/my-configmap.yml)
- [secret file](demo/appconfiguration/my-secret.yml)
- [pod file](demo/appconfiguration/my-volumes.yml)

Check Pod's logs:
```sh
kubectl exec volume-pod -- ls /etc/config/configmap
kubectl exec volume-pod -- cat /etc/config/configmap/key1
```

## Managing Container Resources
### Resource Requests

Resource Requests define the amount of resources (CPU, memory)
#### demo

- [pod file](demo/resources_requests/big-request-pod.yml)
### Resource Limits
Resource Limits provide a way to limit the amount of resources your container can use
- [pod file](demo/resources_requests/resource-pod.yml)

## Monitoring Container Health with Probes

K8s provide numerious features that help check container healthy
### Liveness Probe
 Liveness Probe determine if a container is or not in healthy
### Startup Probe
 Startup Prode are similar to Liveness Probe. However while Liveness Probe run on a schedule,  Startup Probe run at container startup and stop. Help determine if application has successfully started up or stopped
### Readiness Probe
 Readiness Probe determine if a container are ready to accept requests
### Demo

- [liveness pod file](demo/Monitoring_container_healthy/liveness-pod.yml)
- [liveness http pod file](demo/Monitoring_container_healthy/liveness-pod-http.yml)
- [startup pod file](demo/Monitoring_container_healthy/startup-pod.yml)
- [readiness pod file](demo/Monitoring_container_healthy/readiness-pod.yml)
## Building Self-Healing Pods with Restart Policies

k8s can automatically restart containers when they fail. `Restart Policies` allow customize this behavior by defining when we want container to restart

They are 3 possible values for a Pod Restart Policies:
- Always
- OnFailure
- Never

### Always
Default restart policie in k8s. Containers will always restart when they fail
### OnFailure
Will restart container only if container process failed or container unhealthy by liveness probe
### Never
Container will never be restarted

### Demo

```sh
restartPolicy: [Always | OnFailure | never]
```
- [always pod file](demo/restart_policies/always-pod.yml)

## Creating Multi-Container Pods
`BEST PRACTICE`: KEEP CONTAINERS IN SEPARATE PODS UNLESS THEY NEED TO SHARE RESOURCES

- [multi-container pod file](demo/multi-container/multi-container-pod.yml)
- [sidecar pod file](demo/multi-container/sidecar-pod.yml)


## Introducing Init Containers
Init Containers are container that run once during the startup process of a pod. A pod can run one or more init container in a certain order
### Use Cases
- Cause a pod to wait for a k8s resources to be created before finishing startup
- populate data into a shared volume at startup before running the pod
- Communicate with another service at startup
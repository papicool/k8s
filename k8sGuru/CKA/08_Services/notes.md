# Services
a Service is like a Load Balancer or Proxy, it route traffic to Pods
endpoints: are the backend entities to which services route traffic 

## Using k8s services
### Service Types
The service Types determine how and where the service will expose your application:
- ClusterIp: each Pod have uniq @ip and can talk each other inside the cluster
- NodePort: expose Pods outside the cluster
- LoadBalancer: expose Pods outside the cluster but use external cloud LB to do so
- ExternalNmae
### Demo 
#### ClusterIP
- [deployment_svc_example](demo/01_using_k8s_services/deployment_svc_example.yml)
- [clusterip](demo/01_using_k8s_services/svc-clusterip.yml)
- [pod-svc-test](demo/01_using_k8s_services/pod-svc-test.yml)

To test:

```sh
kubectl exec pod-svc-test -- curl svc-clusterip:80
```
#### nodePort

- [nodeport](demo/01_using_k8s_services/svc-nodeport.yml)

## Discovering K8s Services with DNS
K8s DNS assigns DNS names to services allowing applications whitin the cluster to easily locate them.

A service fully qualified domain name has the following format: `service-name.nsamespace-name.svc.cluster-domain.example`

the default cluster domain is `cluster.local`

## Managing Access from Outside with K8s Ingress
An Ingress is a k8s object that manages external access to service in the cluster.

In order for Ingress to do anything, we nust install one or more `Ingress Controller`

Ingress works hand-by-hand with service. Ingress define a set of routing rules. a routing rules has properties determine to which requests it applies. Each rule has a set of `paths`, each with a `backend`
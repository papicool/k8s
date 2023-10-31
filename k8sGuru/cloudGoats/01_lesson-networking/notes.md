# Networking
## Common Networking requirements
- Monolithic App

![Monolithic app](../images/Screenshot%202023-10-29%20at%2020.40.51.png)

- Microservices app

![microservices app](../images/Screenshot%202023-10-29%20at%2020.41.32.png)

Each microservice have dynamically his own IP.

Caracterisque :

- Highly dynamic and scale networks are the new normal when adding or removing resources (scaling, deployment)

![microservices app](../images/Screenshot%202023-10-29%20at%2020.59.05.png)

## Sample App requirements
![microservices app](../images/Screenshot%202023-10-29%20at%2021.01.20.png)


## Kubernetes Networking Basics

### House Rules
- All Nodes can talk
- All Pods can talk without NAT
- Each Pod have specific @ip


![microservices app](../images/Screenshot%202023-10-30%20at%2009.41.28.png)


![microservices app](../images/Screenshot%202023-10-30%20at%2009.47.46.png)

first, we have 2 networks:
- Pod network (provide by 3-part like calico ...)
- Node network (443 HTTPS, Pod Network)

Kubernetes have a pod named `Pod network` which have `CNI Plugin` (CNI : container network interface)


## Kubernetes Service Basics
A Service is like a Load Balancer or proxy. When it's create at the first time, it have a fixed @IP, its name are saved in your local k8s DNS.

In the service, we've `label selector` which balance traffic in the defined pods


![microservices app](../images/Screenshot%202023-10-30%20at%2010.10.00.png)

What if ours pods scale up and down? how kubernetes are managing it?


![microservices app](../images/Screenshot%202023-10-30%20at%2011.25.20.png)

We have another object in the cluster named `Endpoint Object`. This Object is a list of  all @ip and ports that match with label selector



![microservices app](../images/Screenshot%202023-10-30%20at%2011.26.24.png)


## Kubernetes Service Types
- LoadBalancer: integrate with Public cloud platform, create a LB in your cloud
- NodePort: the service also get a `ClusterIP` and they enable access outside the cluster by way of a cluster-wide ports (see screenshot). the nodePort generate a random port service then we pend it to a specific node @ in the cluster, we will reach the service.

The NodePort range is [30000 ... 32767]

![microservices app](../images/Screenshot%202023-10-30%20at%2011.39.04.png)


- ClusterIP : Give the service his own IP that is only accessible within the cluster

By default the service type is `ClusterIP`



##  The Service Network


![microservices app](../images/Screenshot%202023-10-30%20at%2022.05.47.png)


# Networking
## K8s Networking Architectural Overview
K8s Network model is a set of standards that define how networking between Pods
## Understanding K8s DNS
k8s use coreDNS
## Using NetworkPolicies
a k8s `NetworkPolicy` is an object that allows you to control the flow of thr network communication to and from Pods

### podSelector
a `podSelector` determines to which pods in the namespace the NetworkPolicy applies

The `podSelector` can select Pods using Pod labels:
```yaml
apiVersion: v1
...
...
spec:
  podSelector:
    matchLabels:
      role: db
```
### Ingress and Egress
A `NetworkPolicy` can apply to Ingress, Egress or both

`ingress` refers to incomig network traffic coming to the Pod from another source
`Egress` refers to outgoing network traffic leaving the Pod for another destination
#### from and to Selectors
`from selector`: selects ingress traffic that will be allowed
`to selector` selects egress traffic that will be allowed

```yaml
...
...
spec:
  ingress:
    - from:
      ...
      ...
  egress:
    - to:
      ...
      ...
```
![from and to Selectors](../images/Screenshot%202023-11-15%20at%2011.01.57.png)

#### Ports
`port`: Specifies one or more ports that will allow traffic
```yaml
...
...
spec:
  ingress:
    - from:
      ports:
        - protocol: TCP
          port: 80
  egress:
    - to:
      ...
      ...
```
### Demo time

:
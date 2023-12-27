# Networking

Deploying addons on the k8s cluster

# How to Implement Ingress Controller?

![arch](images/Screenshot%202023-12-17%20at%2018.25.59.png)

by default we don't have an ingress controller on k8s.

Deployment steps:
- Create a deployment file
- nginx have his proper set of requirements with vault like: 
  - the path to stock the logs: err-log-path
  - keep-alive treshold
  - ssl-protocols 
  for this params we need to create a configmap in pass these parameter to the nginx ingress controller

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-ingress-controller
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-ingress
  template:
    metadata:
      labels:
        app: nginx-ingress
    spec:
      containers:
      - image: quai.io/kubernetes-ingress-controller/nginx-ingress-controller:0.21.0
        name: nginx-ingress-controller
        args:
          - /nginx-ingress-controller
          - --configmap=$(POD_NAMESPACE)/nginx-configuration 
        env:
        - name: POD_NAME
          valueFrom:
            fieldRef:
              fieldPath: metadata.name
        - name: POD_NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        ports: # port expose by the nginx
        - name: http
          port: 80  
        - name: https
          port: 443 
          # We then need a service to expose the nginx to the world     
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-configuration
data:
  err-log-path: ..
  keepalive: ...
  ssl-protocol: ...
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name:  nginx-ingress
spec:
  selector:
    app:  nginx-ingress
  type:  NodePort
  ports:
  - name: http
    protocol:  TCP
    port:  80
    targetPort:  80
  - name: https
    protocol:  TCP
    port:  443
    targetPort:  443
```

We need a serviceaccount for the right set of permissions

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: nginx-ingress-serviceaccount
```

We need to create approprietes `roles` , `clusterrole` and `bindings`

after configurate nginx, We need to routes traffic on specific pods


example 1 pod:
![arch](images/Screenshot%202023-12-17%20at%2019.04.01.png)

We use `rules` if we want to route traffics base on conditions

Single domain name:
![arch](images/Screenshot%202023-12-17%20at%2019.11.10.png)
![arch](images/Screenshot%202023-12-17%20at%2019.12.09.png)


two domain name:
![arch](images/Screenshot%202023-12-17%20at%2019.45.13.png)


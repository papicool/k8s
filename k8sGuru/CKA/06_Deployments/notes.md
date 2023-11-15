# Deployments
## K8s Deployments Overview
`Deployment` is a k8s object that define a desired state for a replicaSet.

The deployment controller seeks to maintain the desired state by creating, deleting, replacing Pods with new configurations.

A deployment's desired state includes:
- replicas: the number of replica pod the deployment will seek to maintain
- selector: A label selector used to identify the replica Pods managed by the deployment
- template: A template pod definition used to create replica Pods

## Scaling Applications with Deployments
Scaling is a way to add or remove pod on a specific deployment

- deployment file: increasing/decreasing the number of replicas
    ```sh
    apiVersiom: v1
    ...
    ...
    spec:
      repilcas: 3
      containers:
      ...
      ...
    ```
- kubectl
    ```sh
    kubectl scale deployment.v1.apps/my-deployment --replicas=5
     ```

## Managing Rolling Updates with Deployments
`Rolling Updates` allow you to make changes to a deployment's pod.
`Rollback`: Go to a previous version of a deployment
Namespace:
 - resourceQuota

 # Taints & Tolerations
 To Taint the node :

 ```sh
 kubectl taint nodes <node name> key=value:<taint-effect>

 #Example 

  kubectl taint nodes node01 app=blue:NoSchedule

 ```
<taint-effect> : define what will happens to Pods that don't TOLERATE this taint.

We have 3 values possibles:
- NoSchedule : Pod will not be schedule on the tainted node
- PreferNoSchedule: The schedule will try to not schedule intolerant pods on this nodes but nothing is garanted
- NoExecute : Pods that do not tolerate the taint are evicted immediately


to tolerate a Pod:

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "blue"
    effect: "NoSchedule"
```

# NodeAffinity


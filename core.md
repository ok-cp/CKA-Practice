# Core Concepts 19%


## Pods
### How many PODs exist on the system? in the current(default) namespace
<p>
  
```bash
kubectl get pods
```

</p>

### Create a new pod with the NGINX image
<p>

```bash
kubectl run nginx --image=nginx --generator=run-pod/v1
```

</p>

### How many pods are created now? Note: We have created a few more pods. So please check again.
<p>

```bash
kubectl run nginx --image=nginx --generator=run-pod/v1
```

</p>

### What is the image used to create the new pods? You must look at one of the new pods in detail to figure this out
<p>

```bash
kubectl describe pod newpod-<id>
```

</p>

### Which nodes are these pods placed on? You must look at all the pods in detail to figure this out
<p>

```bash
kubectl get po -o wide
```

</p>

### Why do you think the container 'agentx' in pod 'webapp' is in error? Try to figure it out from the events section of the pod
<p>

```bash
kubectl describe pod webapp
```

</p>

### Delete the 'webapp' Pod. Once deleted, wait for the pod to fully terminate.
<p>

```bash
kubectl delete pod webapp
```

</p>

### Create a new pod with the name 'redis' and with the image 'redis123' Use a pod-definition YAML file. And yes the image name is wrong!
<p>

```bash
kubectl run redis --image=redis123 --generator=run-pod/v1
```

</p>

### Now fix the image on the pod to 'redis'. Update the pod-definition file and use 'kubectl apply' command or use 'kubectl edit pod redis' command.
<p>

```bash
kubectl edit po redis
```

</p>

## Replicaset

### How many ReplicaSets exist on the system? in the current(default) namespace
<p>
How many PODs are DESIRED in the new-replica-set?
How many PODs are READY in the new-replica-set?
Why do you think the PODs are not ready?

```bash
kubectl get replicaset
```

</p>

### What is the image used to create the pods in the new-replica-set?
<p>

```bash
kubectl describe replicaset
```

</p>

### Delete the two newly created ReplicaSets - replicaset-1 and replicaset-2
<p>

```bash
kubectl delete replicaset replicaset-1
kubectl delete replicaset replicaset-2
```

</p>

### Fix the original replica set 'new-replica-set' to use the correct 'busybox' image. Either delete and re-create the ReplicaSet or Update the existing ReplicSet and then delete all PODs, so new ones with the correct image will be created.

<p>

```bash

# Scale the ReplicaSet to 5 PODs. Use 'kubectl scale' command or edit the replicaset using 'kubectl edit replicaset'
# Now scale the ReplicaSet down to 2 PODs. Use 'kubectl scale' command or edit the replicaset using 'kubectl edit replicaset'


kubectl edit replicaset new-replica-set
kubectl scale replicaset new-replica-set --replicas=2

```

</p>

## Deployments

### How many Deployments exist on the system? in the current(default) namespace
<p>

```bash
Out of all the existing PODs, how many are ready?
kubectl get deploy
```

</p>

### What is the image used to create the pods in the new deployment?
<p>

```bash
kubectl describe deploy frontend-deployment
```

</p>


### Create a new Deployment using the 'deployment-definition-1.yaml' file located at /root/ .There is an issue with the file, so try to fix it.
<p>

```bash
kubectl apply -f /root/deployment-definition-1.yaml
```

</p>

### Create a new Deployment with the below attributes using your own deployment definition file
#### Name: httpd-frontend; Replicas: 3; Image: httpd:2.4-alpine
<p>

```bash
kubectl run httpd-frontend --image=httpd:2.4-alpine --replicas=3
```

</p>


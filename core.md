# Core Concepts 19%

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

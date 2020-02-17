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

### Deploy a redis pod using the redis:alpine image with the labels set to tier=db.

kubectl run redis  --image=redis:alpine --generator=run-pod/v1 -l tier=db


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

### Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas. Try to use imperative commands only. Do not create definition files.
<p>

```bash
kubectl run webapp --image=kodekloud/webapp-color --replicas=3
```

</p>

## Namespaces

### How many Namespaces exist on the system?

<p>

```bash
kubect get ns
```

</p>


### How many pods exist in the 'research' namespace?
<p>

```bash
kubectl get po -n research
```

</p>

### Create a POD in the 'finance' namespace.
Name: redis
Image Name: redis

<p>

```bash
kubectl run redis --image=redis --generator=run-pod/v1 -n finance
```

</p>


### Which namespace has the 'blue' pod in it?

<p>

```bash
kubectl get pods --all-namespaces
```

</p>


### What DNS name should the Blue application use to access the database 'db-service' in its own namespace - 'marketing'.

<p>

```bash
db-service
```

</p>

### What DNS name should the Blue application use to access the database 'db-service' in the 'dev' namespace

<p>

```bash
db-service.dev.svc.cluster.local
```

</p>


## Service

### How many Services exist on the system? in the current(default) namespace
What is the type of the default 'kubernetes' service?

<p>

```bash
kubectl get svc
```

</p>

### What is the 'targetPort' configured on the 'kubernetes' service?
How many Endpoints are attached on the 'kubernetes' service?

<p>

```bash
kubectl describe svc kubernetes
```

</p>

### How many labels are configured on the 'kubernetes' service?

<p>

```bash
kubectl get svc kubernetes --show-labels
```

</p>




### Create a new service to access the web application using the service-definition-1.yaml file
Name: webapp-service; Type: NodePort; targetPort: 8080; port: 8080; nodePort: 30080; selector: simple-webapp

### Create a service redis-service to expose the redis application within the cluster on port 6379.

<p>

```bash
kubectl expose pod redis  --name=redis-service --port=6379
```

</p>

### Expose the webapp as service webapp-service application on port 30082 on the nodes on the cluster. The web application listens on port 8080
Name: webapp-service
Type: NodePort
Endpoints: 3
Port: 8080
NodePort: 30082

<p>

```bash
kubectl expose deploy webapp --name=webapp-service --type=NodePort --port=8080
kubectl edit svc webapp-service
```

</p>

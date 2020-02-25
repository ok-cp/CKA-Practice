# Core Concepts 19%

Pod 생성/관리, Deployments 생성/관리, Scale 방법, Service 생성/관리 이해가 필요하다.


## Pods
### How many PODs exist on the system? in the current(default) namespace
<p>
  
```bash
$ kubectl get pods
```

</p>
</br>

### Create a new pod with the NGINX image
<p>

```bash
$ kubectl run nginx --image=nginx --generator=run-pod/v1
```

</p>
</br>

### How many pods are created now? Note: We have created a few more pods. So please check again.
<p>

```bash
$ kubectl run nginx --image=nginx --generator=run-pod/v1
```

</p>
</br>

### What is the image used to create the new pods? You must look at one of the new pods in detail to figure this out
<p>

```bash
$ kubectl describe pod newpod-<id>
```

</p>
</br>

### Which nodes are these pods placed on? You must look at all the pods in detail to figure this out
<p>

```bash
$ kubectl get po -o wide
```

</p>
</br>

### Why do you think the container 'agentx' in pod 'webapp' is in error? Try to figure it out from the events section of the pod
<p>

```bash
$ kubectl describe pod webapp
```

</p>
</br>

### Delete the 'webapp' Pod. Once deleted, wait for the pod to fully terminate.
<p>

```bash
$ kubectl delete pod webapp
```

</p>
</br>

### Create a new pod with the name 'redis' and with the image 'redis123' Use a pod-definition YAML file. And yes the image name is wrong!
<p>

```bash
$ kubectl run redis --image=redis123 --generator=run-pod/v1
```

</p>
</br>

### Now fix the image on the pod to 'redis'. Update the pod-definition file and use 'kubectl apply' command or use 'kubectl edit pod redis' command.
<p>

```bash
$ kubectl edit po redis
```

</p>
</br>

### Deploy a redis pod using the redis:alpine image with the labels set to tier=db.

    $ kubectl run redis  --image=redis:alpine --generator=run-pod/v1 -l tier=db

</br>
</br>
</br>


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
</br>

### What is the image used to create the pods in the new-replica-set?
<p>

```bash
$ kubectl describe replicaset
```

</p>
</br>

### Delete the two newly created ReplicaSets - replicaset-1 and replicaset-2
<p>

```bash
$ kubectl delete replicaset replicaset-1
$ kubectl delete replicaset replicaset-2
```

</p>
</br>

### Fix the original replica set 'new-replica-set' to use the correct 'busybox' image. Either delete and re-create the ReplicaSet or Update the existing ReplicSet and then delete all PODs, so new ones with the correct image will be created.

<p>

```bash
$ kubectl edit replicaset new-replica-set
$ kubectl scale replicaset new-replica-set --replicas=2
```

</p>
</br>
</br>
</br>

## Deployments

### How many Deployments exist on the system? in the current(default) namespace
<p>

```bash
$ kubectl get deploy
```

</p>
</br>

### What is the image used to create the pods in the new deployment?
<p>

```bash
$ kubectl describe deploy frontend-deployment
```

</p>
</br>


### Create a new Deployment using the 'deployment-definition-1.yaml' file located at /root/ .There is an issue with the file, so try to fix it.
<p>

```bash
$ kubectl apply -f /root/deployment-definition-1.yaml
```

</p>
</br>

### Create a new Deployment with the below attributes using your own deployment definition file
#### Name: httpd-frontend; Replicas: 3; Image: httpd:2.4-alpine
<p>

```bash
$ kubectl run httpd-frontend --image=httpd:2.4-alpine --replicas=3
```

</p>
</br>

### Create a deployment named webapp using the image kodekloud/webapp-color with 3 replicas. Try to use imperative commands only. Do not create definition files.
<p>

```bash
$ kubectl run webapp --image=kodekloud/webapp-color --replicas=3
```

</p>
</br>
</br>
</br>

## Namespaces

### How many Namespaces exist on the system?

<p>

```bash
$ kubect get ns
```

</p>
</br>


### How many pods exist in the 'research' namespace?
<p>

```bash
$ kubectl get po -n research
```

</p>
</br>

### Create a POD in the 'finance' namespace.
* Name: redis
* Image Name: redis

<p>

```bash
$ kubectl run redis --image=redis --generator=run-pod/v1 -n finance
```

</p>
</br>


### Which namespace has the 'blue' pod in it?

<p>

```bash
$ kubectl get pods --all-namespaces
```

</p>
</br>


### What DNS name should the Blue application use to access the database 'db-service' in its own namespace - 'marketing'.

<p>

```bash
$ db-service
```

</p>

### What DNS name should the Blue application use to access the database 'db-service' in the 'dev' namespace

<p>

```bash
$ db-service.dev.svc.cluster.local
```

</p>
</br>
</br>
</br>


## Service

### How many Services exist on the system? in the current(default) namespace
* What is the type of the default 'kubernetes' service?

<p>

```bash
$ kubectl get svc
```

</p>
</br>

### What is the 'targetPort' configured on the 'kubernetes' service?
* How many Endpoints are attached on the 'kubernetes' service?

<p>

```bash
$ kubectl describe svc kubernetes
```

</p>
</br>

### How many labels are configured on the 'kubernetes' service?

<p>

```bash
$ kubectl get svc kubernetes --show-labels
```

</p>
</br>


### Create a service redis-service to expose the redis application within the cluster on port 6379.

<p>

```bash
$ kubectl expose pod redis  --name=redis-service --port=6379
```

</p>
</br>

### Expose the webapp as service webapp-service application on port 30082 on the nodes on the cluster. The web application listens on port 8080
* Name: webapp-service
* Type: NodePort
* Endpoints: 3
* Port: 8080
* NodePort: 30082

<p>

```bash
$ kubectl expose deploy webapp --name=webapp-service --type=NodePort --port=8080
$ kubectl edit svc webapp-service
```

</p>
</br>

# Scheduling 5%

## Manually schedule
### Manually schedule the pod on node01. Delete and re-create the POD if necessary
<p>
  
```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  nodeName: node-01
```

</p>

## Labels and Selectors
### We have deployed a number of PODs. They are labelled with 'tier', 'env' and 'bu'. How many PODs exist in the 'dev' environment?
<p>
  
```bash
 kubectl get po -l env=dev
 ```

</p>

### How many PODs are in the 'finance' business unit ('bu')?
<p>
  
```bash
kubectl get po -l bu=finance
```

</p>

### How many objects are in the 'prod' environment including PODs, ReplicaSets and any other objects?
<p>
  
```bash
kubectl get all -l env=prod
kubectl get all --selector env=prod
```

</p>

### Identify the POD which is 'prod', part of 'finance' BU and is a 'frontend' tier?
<p>
  
```bash
kubectl get po -l env=prod,bu=finance,tier=frontend
```

</p>

## Taints and Tolerations

### How many Nodes exist on the system? including the master node
<p>
  
```bash
k get nodes
```

</p>

### Create a taint on node01 with key of 'spray', value of 'mortein' and effect of 'NoSchedule'
<p>
  
```bash
kubectl taint nodes node01 spray=mortein:NoSchedule
```

</p>

### Create another pod named 'bee' with the NGINX image, which has a toleration set to the taint Mortein

Image name: nginx
Key: spray
Value: mortein
Effect: NoSchedule
Status: Running
<p>
  
```bash
k run bee --image=nginx --generator=run-pod/v1 --dry-run -o yaml > bee.yaml
```

</p>

<p>
  
```bash
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: bee
  name: bee
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: bee
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  tolerations:
  - key: "spray"
    value: "mortein"
    effect: "NoSchedule"
```

</p>
    
    
    
### Remove the taint on master, which currently has the taint effect of NoSchedule
    
    kubectl taint node master node-role.kubernetes.io/master:NoSchedule-
    
    
    
## Node Affinity
    
### How many Labels exist on node node01?
<p>
  
```bash
     k get nodes --show-labels
```

</p>
     
### Apply a label color=blue to node node01
<p>
  
```bash    
     kubectl label node node01 color=blue
```

</p>
     
### Set Node Affinity to the deployment to place the PODs on node01 only
Name: blue
Replicas: 6
Image: nginx
NodeAffinity: requiredDuringSchedulingIgnoredDuringExecution
Key: color
values: blue
<p>
  
```bash
k edit deploy blue
```

</p>

<p>
  
```bash
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: blue
spec:
  replicas: 6
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: color
                operator: In
                values:
                - blue
```

</p>
                
### Create a new deployment named 'red' with the NGINX image and 3 replicas, and ensure it gets placed on the master node only. Use the label - node-role.kubernetes.io/master - set on the master node.
                
Name: red
Replicas: 3
Image: nginx
NodeAffinity: requiredDuringSchedulingIgnoredDuringExecution
Key: node-role.kubernetes.io/master
Use the right operator


<p>
  
```bash
k run red --image=nginx --replicas=3 --dry-run -o yaml > red.yaml
vi red.yaml
```

</p>

<p>
  
```bash
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    run: red
  name: red
spec:
  replicas: 3
  selector:
    matchLabels:
      run: red
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: red
    spec:
      containers:
      - image: nginx
        name: red
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: node-role.kubernetes.io/master
                operator: Exists
```

</p>

<p>
  
```bash
k apply -f red.yaml
```

</p>



## Resource Limits

### A pod named 'rabbit' is deployed. Identify the CPU requirements set on the Pod in the current(default) namespace

<p>
  
```bash
kubectl describe pod rabbit
```

</p>

### The elephant runs a process that consume 15Mi of memory. Increase the limit of the elephant pod to 20Mi. Delete and recreate the pod if required. Do not modify anything other than the required fields.
Pod Name: elephant
Image Name: polinux/stress
Memory Limit: 20Mi


<p>
  
```bash
k get po elephant -o yaml > e.yaml
```

</p>

<p>
  
```bash
spec:
  containers:
  - args:
    - --vm
    - "1"
    - --vm-bytes
    - 15M
    - --vm-hang
    - "1"
    command:
    - stress
    image: polinux/stress
    imagePullPolicy: Always
    name: mem-stress
    resources:
      limits:
        memory: 20Mi
      requests:
        memory: 5Mi
```

</p>

<p>
  
```bash
k delete po elephant
```

</p>

<p>
  
```bash
k apply -f e.yaml        
```

</p>


## DaemonSets

### How many DaemonSets are created in the cluster in all namespaces? Check all namespaces

<p>
  
```bash
kubectl get daemonsets --all-namespaces
```

</p>

### What is the image used by the POD deployed by the weave-net DaemonSet?

<p>
  
```bash
kubectl describe daemonset weave-net --namespace=kube-system
```

</p>

### Deploy a DaemonSet for FluentD Logging. Use the given specifications.
Name: elasticsearch
Namespace: kube-system
Image: k8s.gcr.io/fluentd-elasticsearch:1.20

<p>
  
```bash
vi daemon.yaml
```

</p>

<p>
  
```bash
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: elasticsearch
  namespace: kube-system
spec:
  selector:
    matchLabels:
      name: elasticsearch
  template:
    metadata:
      labels:
        name: elasticsearch
    spec:
      containers:
      - name: elasticsearch
        image: k8s.gcr.io/fluentd-elasticsearch:1.20
```

</p>

<p>
  
```bash
k apply -f daemon.yaml
```

</p>


## Static Pods

### What is the path of the directory holding the static pod definition files?

<p>
  
```bash
cat /var/lib/kubelet/config.yaml | grep staticPodPath:
```

</p>


### Create a static pod named static-busybox that uses the busybox image and the command sleep 1000

Name: static-busybox
Image: busybox

<p>
  
```bash
k run static-busybox --image=busybox --generator=run-pod/v1 --dry-run -o yaml --command -- sleep 1000 > static.yaml
mv static.yaml /etc/kubernetes/manifests/

k get po
NAME                    READY   STATUS    RESTARTS   AGE
static-busybox-master   1/1     Running   0          18s
```

</p>


### We just created a new static pod named static-greenbox. Find it and delete it.

<p>
  
```bash
master $ k get po  -o wide
NAME                     READY   STATUS    RESTARTS   AGE   IP          NODE     NOMINATED NODE   READINESS GATES
static-greenbox-node01   1/1     Running   0          65s   10.44.0.1   node01   <none>           <none>

master $ k get nodes -o wide
NAME     STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
master   Ready    master   10m   v1.16.0   172.17.0.55   <none>        Ubuntu 16.04.6 LTS   4.4.0-166-generic   docker://18.9.7
node01   Ready    <none>   10m   v1.16.0   172.17.0.58   <none>        Ubuntu 16.04.6 LTS   4.4.0-166-generic   docker://18.9.7

master $ ssh 172.17.0.58

node01 $ cat /var/lib/kubelet/config.yaml  | grep static
staticPodPath: /etc/just-to-mess-with-you
node01 $ rm /etc/just-to-mess-with-you/greenbox.yaml
Connection to 172.17.0.58 closed.

master $ k get po
No resources found in default namespace.
```

</p>


## Multiple Schedulers

### Deploy an additional scheduler to the cluster following the given specification. Use the manifest file used by kubeadm tool. Use a different port than the one used by the current one.

Namespace: kube-system
Name: my-scheduler
Status: Running
Custom Scheduler Name

cp /etc/kubernetes/manifests/kube-scheduler.yaml /etc/kubernetes/manifests/my-scheduler.yaml
vi /etc/kubernetes/manifests/my-scheduler.yaml

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    component: kube-scheduler
    tier: control-plane
  name: my-scheduler # Change
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-scheduler
    - --bind-address=127.0.0.1
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    - --leader-elect=true
    - --scheduler-name=my-scheduler # Add
    - --port=10282    # Add 
    - --secure-port=0  # Add
    image: k8s.gcr.io/kube-scheduler:v1.16.0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 127.0.0.1
        path: /healthz
        port: 10282  # change
        scheme: HTTP



### A POD definition file is given. Use it to create a POD with the new custom scheduler. File is located at /root/nginx-pod.yaml

Name: nginx
Uses custom scheduler
Status: Running

apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  schedulerName: my-scheduler # Add
  containers:
  -  image: nginx
     name: nginx
     
k get po 

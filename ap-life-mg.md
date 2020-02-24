# Application Lifecycle Management 8%

### Inspect the deployment and identify the current strategy

<p>
  
```bash
k describe deploy frontend
```

</p>

### Let us try that. Upgrade the application by setting the image on the deployment to 'kodekloud/webapp-color:v2'. Do not delete and re-create the deployment. Only set the new image name for the existing deployment.
<p>

```bash
k edit deploy frontend
```

</p>

### Up to how many PODs can be down for upgrade at a time. Consider the current strategy settings and number of PODs - 4
<p>

```bash
k describe deploy frontend | grep RollingUpdateStrategy
RollingUpdateStrategy:  25% max unavailable, 25% max surge

1
```

</p>

### Change the deployment strategy to 'Recreate'. Do not delete and re-create the deployment. Only update the strategy type for the existing deployment.
Deployment Name: frontend
Deployment Image: kodekloud/webapp-color:v2
Strategy: Recreate

<p>

```bash
k edit deploy frontend

  selector:
    matchLabels:
      name: webapp
  strategy:
    type: Recreate
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: webapp
```

</p>

### What is the command used to run the pod 'ubuntu-sleeper'?
<p>

```bash
k describe po ubuntu-sleeper
Name:         ubuntu-sleeper
Namespace:    default
Priority:     0
Node:         node01/172.17.0.71
Start Time:   Tue, 18 Feb 2020 02:34:09 +0000
Labels:       <none>
Annotations:  <none>
Status:       Pending
IP:
IPs:          <none>
Containers:
  ubuntu:
    Container ID:
    Image:         ubuntu
    Image ID:
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      4800
      
      
```

</p>


### Create a pod with the ubuntu image to run a container to sleep for 5000 seconds. Modify the file ubuntu-sleeper-2.yaml. Note: Only make the necessary changes. Do not modify the name.
<p>

```bash
Pod Name: ubuntu-sleeper-2
Command: sleep 5000

apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-2
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command:
      - "sleep"
      - "5000"
      
      
```

</p>


### Create a pod with the given specifications. By default it displays a 'blue' background. Set the given command line arguments to change it to 'green'
<p>

```bash
Pod Name: webapp-green
Image: kodekloud/webapp-color
Command line arguments: --color=green

apiVersion: v1
kind: Pod
metadata:
  name: webapp-green
  labels:
      name: webapp-green
spec:
  containers:
  - name: simple-webapp
    image: kodekloud/webapp-color
    args: ["--color", "green"]
      
```

</p>
   
    
### How many ConfigMaps exist in the environment?
<p>

```bash
kubectl get configmaps
      
```

</p>


### Identify the database host from the config map 'db-config'
<p>

```bash
Run the command 'kubectl describe configmaps' and look for DB_HOST option
      
```

</p>


### create configmap
<p>

```bash
configmap name: webapp-config-map
data : APP_COLOR=darkblue

apiVersion: v1
kind: ConfigMap
metadata:
  name: webapp-config-map
  namespace: default
data:
  APP_COLOR: darkblue
      
```

</p>


### Update the environment variable on the POD use the newly created ConfigMap. Note: Delete and recreate the POD. Only make the necessary changes. Do not modify the name of the Pod.
<p>

```bash
Pod Name: webapp-color
EnvFrom: webapp-config-map

spec:
  containers:
  - name: webapp-color
    env:
    - name: APP_COLOR
      valueFrom:
        configMapKeyRef:
           name: webapp-config-map
           key: APP_COLOR
    image: kodekloud/webapp-color
    imagePullPolicy: Always
       
```

</p>
   

## Secrets

### How many Secrets exist on the system? in the current(default) namespace
<p>

```bash
k get secret
      
```

</p>

### How many secrets are defined in the 'default-token' secret?
<p>

```bash
Run the command 'kubectl get secrets' and look at the DATA field
      
```

</p>

### What is the type of the 'default-token' secret?
<p>

```bash
kubectl describe secret
      
```

</p>


### The reason the application is failed is because we have not created the secrets yet. Create a new Secret named 'db-secret' with the data given(on the right). You may follow any one of the methods discussed in lecture to create the secret.
<p>

```bash
Secret Name: db-secret
Secret 1: DB_Host=sql01
Secret 2: DB_User=root
Secret 3: DB_Password=password123

kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=password123
      
```

</p>


### Configure webapp-pod to load environment variables from the newly created secret. Delete and recreate the pod if required.
<p>

```bash
Pod name: webapp-pod
Image name: kodekloud/simple-webapp-mysql
Env From: Secret=db-secret

master $ k get po webapp-pod -o yaml > webapp-pod.yaml
master $ k delete -f webapp-pod.yaml
pod "webapp-pod" deleted
master $ vi webapp-pod.yaml

spec:
  containers:
  - image: kodekloud/simple-webapp-mysql
    imagePullPolicy: Always
    name: webapp
    env:
      - name: DB_Host
        valueFrom:
          secretKeyRef:
            name: db-secret
            key: DB_Host
      - name: DB_User
        valueFrom:
          secretKeyRef:
            name: db-secret
            key: DB_User
      - name: DB_Password
        valueFrom:
          secretKeyRef:
            name: db-secret
            key: DB_Password
      
```

</p>

## Multi Container

###  Create a multi-container pod with 2 containers. Use the spec given on the right.
<p>

```bash
Name: yellow
Container 1 Name: lemon
Container 1 Image: busybox
Container 2 Name: gold
Container 2 Image: redis

k run yellow --image=busybox --generator=run-pod/v1 --dry-run -o yaml > multi.yaml
master $ vi multi.yaml
master $ k apply -f multi.yaml

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: yellow
  name: yellow
spec:
  containers:
  - image: busybox
    name: lemon
  - image: redis
    name: gold
      
```

</p>

### The 'app'lication outputs logs to the file /log/app.log. View the logs and try to identify the user having issues with Login. Inspect the log file inside the pod
<p>

```bash
k exec app -it -n elastic-stack cat /log/app.log
      
```

</p>

### Edit the pod to add a sidecar container to send logs to ElasticSearch. Mount the log volume to the sidecar container. Only add a new container. Do not modify anything else. Use the spec on the right.
<p>

```bash
Name: app
Container Name: sidecar
Container Image: kodekloud/filebeat-configured
Volume Mount: log-volume
Mount Path: /var/log/event-simulator/
Existing Container Name: app
Existing Container Image: kodekloud/event-simulator


master $ k get po -n elastic-stack  -o yaml  > app.yaml
master $ vi app.yaml

  spec:
    containers:
    - image: kodekloud/event-simulator
      imagePullPolicy: Always
      name: app
      resources: {}
      terminationMessagePath: /dev/termination-log
      terminationMessagePolicy: File
      volumeMounts:
      - mountPath: /log
        name: log-volume
      - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
        name: default-token-fx8mv
        readOnly: true
    - image: kodekloud/filebeat-configured
      imagePullPolicy: Always
      name: sidecar
      volumeMounts:
      - mountPath: /var/log/event-simulator/
        name: log-volume

master $ k delete po app -n elastic-stack
pod "app" deleted
master $ k apply -f app.yaml
      
```

</p>

## Init Container

### Update the pod red to use an initContainer that uses the busybox image and sleeps for 20 seconds. Delete and re-create the pod if necessary. But make sure no other configurations change.

<p>

```bash
Pod: red
initContainer Configured Correctly

k get po red -o yaml > red.yaml
vi red.yaml

spec:
  initContainers:
  - name: initc
    image: busybox
    command: ['sleep','20']
  containers:
  - command:
    - sh
    - -c
    - echo The app is running! && sleep 3600
    image: busybox:1.28
    imagePullPolicy: IfNotPresent
    name: red-container

k delete -f red.yaml
pod "red" deleted
k apply -f red.yaml
      
```

</p>

### A new application orange is deployed. There is something wrong with it. Identify and fix the issue. Once fixed, wait for the application to run before checking solution.

<p>

```bash
kubectl describe po orange

kubectl get po orange -o yaml > orange.yaml

vi orange.yaml
# fixed issue

kubectl delete -f orange.yaml
kubectl apply -f orange.yaml
```

</p>


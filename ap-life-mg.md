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

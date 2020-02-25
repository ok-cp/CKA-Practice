# Storage 7%

Storage는 조회하거나 주어진 조건대로 오브젝트를 생성할 수 있는지 확인한다.

### Configure a volume to store these logs at /var/log/webapp on the host. Use the spec given on the right.
* Name: webapp
* Image Name: kodekloud/event-simulator
* Volume HostPath: /var/log/webapp
* Volume Mount: /log

<p>

```bash
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - name: event-simulator
    image: kodekloud/event-simulator
    env:
    - name: LOG_HANDLERS
      value: file
    volumeMounts:
    - mountPath: /log
      name: log-volume
  volumes:
  - name: log-volume
    hostPath:
      path: /var/log/webapp
      type: Directory
```

</p>      

</br>

### Create a 'Persistent Volume' with the given specification.
* Volume Name: pv-log   
* Storage: 100Mi   
* Access modes: ReadWriteMany   
* Host Path: /pv/log   

<p>

```bash
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /pv/log
```

</p>      
 
</br>
 
### Let us claim some of that storage for our application. Create a 'Persistent Volume Claim' with the given specification.

<p>

```bash 
Volume Name: claim-log-1
Storage Request: 50Mi
Access modes: ReadWriteMany

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 50Mi
```

</p>      

</br>

### Update the webapp pod to use the persistent volume claim as its storage. Replace hostPath configured earlier with the newly created PersistentVolumeClaim

* Name: webapp   
* Image Name: kodekloud/event-simulator   
* Volume: PersistentVolumeClaim=claim-log-1   
* Volume Mount: /log   

<p>

```bash
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - name: event-simulator
    image: kodekloud/event-simulator
    env:
    - name: LOG_HANDLERS
      value: file
    volumeMounts:
    - mountPath: /log
      name: log-volume

  volumes:
  - name: log-volume
    persistentVolumeClaim:
      claimName: claim-log-1
```

</p>      

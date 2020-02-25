# Troubleshooting 10%


## Application Failure

### [Service-Name]Troubleshooting Test 1: A simple 2 tier application is deployed in the alpha namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

<p>

```bash
master $ k get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   116s
master $ k get all -n alpha
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          64s
pod/webapp-mysql-6ff75cc4c8-2qdjh   1/1     Running   0          63s

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql         ClusterIP   10.101.216.24   <none>        3306/TCP         64s
service/web-service   NodePort    10.96.255.89    <none>        8080:30081/TCP   63s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           63s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ff75cc4c8   1         1         1       63s


master $ k get svc mysql -n alpha -o yaml > mysql-svc.yaml
master $ k delete -f mysql-svc.yaml
service "mysql" deleted
master $ vi mysql-svc.yaml
master $ k apply -f mysql-svc.yaml
service/mysql-service created
```

</p>

### [Serivce TargetPort] Troubleshooting Test 2: The same 2 tier application is deployed in the beta namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

<p>


```bash
master $ k get all -n beta
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          118s
pod/webapp-mysql-6ff75cc4c8-7x75g   1/1     Running   0          118s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.98.31.97     <none>        3306/TCP         118s
service/web-service     NodePort    10.99.100.185   <none>        8080:30081/TCP   117s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           118s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-6ff75cc4c8   1         1         1       118s


master $ k describe svc mysql-service   -n beta
Name:              mysql-service
Namespace:         beta
Labels:            <none>
Annotations:       <none>
Selector:          name=mysql
Type:              ClusterIP
IP:                10.98.31.97
Port:              <unset>  3306/TCP
TargetPort:        8080/TCP  # port
Endpoints:         10.44.0.1:8080
Session Affinity:  None
Events:            <none>
```

</p>

### [Labels] Troubleshooting Test 3: The same 2 tier application is deployed in the gamma namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

master $ k get all -n gamma --show-labels
NAME                                READY   STATUS    RESTARTS   AGE     LABELS
pod/mysql                           1/1     Running   0          8m34s   name=mysql
pod/webapp-mysql-6ff75cc4c8-76ndn   1/1     Running   0          8m34s   name=webapp-mysql,pod-template-hash=6ff75cc4c8

NAME                    TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE     LABELS
service/mysql-service   ClusterIP   10.104.197.135   <none>        3306/TCP         8m34s   <none>
service/web-service     NodePort    10.106.95.25     <none>        8080:30081/TCP   8m34s   <none>

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE     LABELS
deployment.apps/webapp-mysql   1/1     1            1           8m34s   name=webapp-mysql

NAME                                      DESIRED   CURRENT   READY   AGE     LABELS
replicaset.apps/webapp-mysql-6ff75cc4c8   1         1         1       8m34s   name=webapp-mysql,pod-template-hash=6ff75cc4c8
master $ k describe service/mysql-service  -n gamma
Name:              mysql-service
Namespace:         gamma
Labels:            <none>
Annotations:       <none>
Selector:          name=sql00001  <<<<<<<<<<
Type:              ClusterIP
IP:                10.104.197.135
Port:              <unset>  3306/TCP
TargetPort:        3306/TCP
Endpoints:         <none>
Session Affinity:  None
Events:            <none>
```

</p>


### [Env] Troubleshooting Test 4: The same 2 tier application is deployed in the delta namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

<p>


```bash
master $ k describe deployment.apps/webapp-mysql  -n delta
Name:                   webapp-mysql
Namespace:              delta
CreationTimestamp:      Tue, 18 Feb 2020 07:11:19 +0000
Labels:                 name=webapp-mysql
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               name=webapp-mysql
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  name=webapp-mysql
  Containers:
   webapp-mysql:
    Image:      mmumshad/simple-webapp-mysql
    Port:       8080/TCP
    Host Port:  0/TCP
    Environment:
      DB_Host:      mysql-service
      DB_User:      sql-user
      DB_Password:  paswrd
    Mounts:         <none>
  Volumes:          <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   webapp-mysql-76686f9686 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  97s   deployment-controller  Scaled up replica set webapp-mysql-76686f9686 to 1
```

</p>


### Troubleshooting Test 5: The same 2 tier application is deployed in the epsilon namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

<p>

```bash
master $ k describe pod/mysql    -n epsilon
Name:         mysql
Namespace:    epsilon
Priority:     0
Node:         node01/172.17.0.57
Start Time:   Tue, 18 Feb 2020 07:14:33 +0000
Labels:       name=mysql
Annotations:  <none>
Status:       Running
IP:           10.44.0.1
IPs:
  IP:  10.44.0.1
Containers:
  mysql:
    Container ID:   docker://6d1fe0ea4ffbbe9ed0b27c2aae50d77ff93c5047abcf1233abf8944020e2ce63
    Image:          mysql
    Image ID:       docker-pullable://mysql@sha256:6d0741319b6a2ae22c384a97f4bbee411b01e75f6284af0cce339fee83d7e314
    Port:           3306/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 18 Feb 2020 07:14:37 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      MYSQL_ROOT_PASSWORD:  passwooooorrddd  <<<<<<<<<<<<<<<<<<<<
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-vl7bn (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-vl7bn:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-vl7bn
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age        From               Message
  ----    ------     ----       ----               -------
  Normal  Scheduled  <unknown>  default-scheduler  Successfully assigned epsilon/mysql to node01
  Normal  Pulling    2m50s      kubelet, node01    Pulling image "mysql"
  Normal  Pulled     2m49s      kubelet, node01    Successfully pulled image "mysql"
  Normal  Created    2m49s      kubelet, node01    Created container mysql
  Normal  Started    2m48s      kubelet, node01    Started container mysql
```

</p>

### Troubleshooting Test 6: The same 2 tier application is deployed in the zeta namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

<p>

```bash
master $ k get all -n zeta
NAME                                READY   STATUS    RESTARTS   AGE
pod/mysql                           1/1     Running   0          71s
pod/webapp-mysql-76686f9686-zmpn5   1/1     Running   0          70s

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/mysql-service   ClusterIP   10.110.163.61   <none>        3306/TCP         71s
service/web-service     NodePort    10.96.126.192   <none>        8080:30088/TCP   70s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp-mysql   1/1     1            1           70s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-mysql-76686f9686   1         1         1       70s

master $ k edit service/web-service -n zeta
service/web-service edited
```


</p>



## Control Plane Failure

### The cluster is broken again. We tried deploying an application but it's not working. Troubleshoot and fix the issue.

<p>


```bash
master $ k get po -n kube-system
NAME                             READY   STATUS             RESTARTS   AGE
coredns-5644d7b6d9-v5td4         1/1     Running            0          6m8s
coredns-5644d7b6d9-zq7bn         1/1     Running            0          6m8s
etcd-master                      1/1     Running            0          5m52s
kube-apiserver-master            1/1     Running            0          5m51s
kube-controller-manager-master   1/1     Running            0          6m2s
kube-proxy-gqncs                 1/1     Running            0          5m47s
kube-proxy-rz4r2                 1/1     Running            0          6m8s
kube-scheduler-master            0/1     CrashLoopBackOff   4          3m2s
weave-net-k9k8j                  2/2     Running            1          6m8s
weave-net-m2xml                  2/2     Running            0          5m47s

master $ k describe po kube-scheduler-master -n kube-system

Events:
  Type     Reason   Age                   From             Message
  ----     ------   ----                  ----             -------
  Normal   Pulled   99s (x5 over 3m18s)   kubelet, master  Container image "k8s.gcr.io/kube-scheduler:v1.16.0" already present on machine
  Normal   Created  99s (x5 over 3m18s)   kubelet, master  Created container kube-scheduler
  Warning  Failed   99s (x5 over 3m17s)   kubelet, master  Error: failed to start container "kube-scheduler": Errorresponse from daemon: OCI runtime create failed: container_linux.go:345: starting container process caused "exec: \"kube-schedulerrrr\": executable file not found in $PATH": unknown
  Warning  BackOff  92s (x10 over 3m16s)  kubelet, master  Back-off restarting failed container

master $ vi /etc/kubernetes/manifests/kube-scheduler.yaml

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    component: kube-scheduler
    tier: control-plane
  name: kube-scheduler
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-schedulerrrr
```


</p>


### Even though the depoyment was scaled to 2, the number of PODs does not seem to increase. Investigate and fix the issue.
Inspect the component responsible for managing deployments and replicasets.

<p>

```bash
master $ k get po -n kube-system
NAME                             READY   STATUS             RESTARTS   AGE
coredns-5644d7b6d9-v5td4         1/1     Running            0          8m50s
coredns-5644d7b6d9-zq7bn         1/1     Running            0          8m50s
etcd-master                      1/1     Running            0          8m34s
kube-apiserver-master            1/1     Running            0          8m33s
kube-controller-manager-master   0/1     CrashLoopBackOff   3          62s
kube-proxy-gqncs                 1/1     Running            0          8m29s
kube-proxy-rz4r2                 1/1     Running            0          8m50s
kube-scheduler-master            1/1     Running            1          84s
weave-net-k9k8j                  2/2     Running            1          8m50s
weave-net-m2xml                  2/2     Running            0          8m29s

master $ k logs kube-controller-manager-master  -n kube-system
I0219 15:19:15.112199       1 serving.go:319] Generated self-signed cert in-memory
stat /etc/kubernetes/controller-manager-XXXX.conf: no such file or directory

master $ vi /etc/kubernetes/manifests/kube-controller-manager.yaml

spec:
  containers:
  - command:
    - kube-controller-manager
    - --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf
    - --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf
    - --bind-address=127.0.0.1
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
    - --cluster-signing-key-file=/etc/kubernetes/pki/ca.key
    - --controllers=*,bootstrapsigner,tokencleaner
    - --kubeconfig=/etc/kubernetes/controller-manager-XXXX.conf
```


</p>

### Something is wrong with scaling again. We just tried scaling the deployment to 3 replicas. But it's not happening. Investigate and fix the issue
Fix Issue
Wait for deployment to actually scale

<p>

```bash
master $ k get po
NAME                  READY   STATUS    RESTARTS   AGE
app-f54ccc97b-5clrr   1/1     Running   0          14m
app-f54ccc97b-rtb7v   1/1     Running   0          3m26s
master $ k get deploy
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
app    2/3     2            2           14m


master $ k get po -n kube-system
NAME                             READY   STATUS             RESTARTS   AGE
coredns-5644d7b6d9-v5td4         1/1     Running            0          17m
coredns-5644d7b6d9-zq7bn         1/1     Running            0          17m
etcd-master                      1/1     Running            0          17m
kube-apiserver-master            1/1     Running            0          17m
kube-controller-manager-master   0/1     CrashLoopBackOff   5          3m53s
kube-proxy-gqncs                 1/1     Running            0          17m
kube-proxy-rz4r2                 1/1     Running            0          17m
kube-scheduler-master            1/1     Running            1          10m
weave-net-k9k8j                  2/2     Running            1          17m
weave-net-m2xml                  2/2     Running            0          17m

master $ k logs kube-controller-manager-master   -n kube-system
I0219 15:25:37.000364       1 serving.go:319] Generated self-signed cert in-memory
unable to load client CA file: unable to load client CA file: open /etc/kubernetes/pki/ca.crt: no such file or directory


  - hostPath:
      path: /etc/pki
      type: DirectoryOrCreate
    name: etc-pki
  - hostPath:
      path: /usr/libexec/kubernetes/kubelet-plugins/volume/exec
      type: DirectoryOrCreate
    name: flexvolume-dir
  - hostPath:
      path: /etc/kubernetes/WRONG-PKI-DIRECTORY <<<<<<
      type: DirectoryOrCreate
    name: k8s-certs
  - hostPath:
      path: /etc/kubernetes/controller-manager.conf
      type: FileOrCreate
    name: kubeconfig
  - hostPath:
      path: /usr/local/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-local-share-ca-certificates
  - hostPath:
      path: /usr/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-share-ca-certificates
```

</p>

## Worker Node Failure

### Fix the broken cluster
Fix node01

<p>

```bash
master $ k get nodes
NAME     STATUS     ROLES    AGE    VERSION
master   Ready      master   115s   v1.16.0
node01   NotReady   <none>   83s    v1.16.0
master $ ssh node01
node01 $ service kubelet status
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: inactive (dead) since Wed 2020-02-19 15:33:35 UTC; 1min 41s ago
     Docs: https://kubernetes.io/docs/home/
  Process: 3537 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $K
 Main PID: 3537 (code=exited, status=0/SUCCESS)

Feb 19 15:33:35 node01 systemd[1]: Stopping kubelet: The Kubernetes Node Agent...
Feb 19 15:33:35 node01 systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Warning: Journal has been rotated since unit was started. Log output is incomplete or unava

node01 $ service kubelet start

master $ k get nodes
NAME     STATUS   ROLES    AGE     VERSION
master   Ready    master   2m57s   v1.16.0
node01   Ready    <none>   2m25s   v1.16.0
```

</p>

### The cluster is broken again. Investigate and fix the issue.

<p>


```bash
master $ k get nodes
NAME     STATUS     ROLES    AGE     VERSION
master   Ready      master   4m11s   v1.16.0
node01   NotReady   <none>   3m39s   v1.16.0
master $ !ssh
ssh node01

node01 $ journalctl -u kubelet -f
-- Logs begin at Wed 2020-02-19 15:33:35 UTC. --
Feb 19 15:37:32 node01 systemd[1]: kubelet.service: Failed with result 'exit-code'.
Feb 19 15:37:42 node01 systemd[1]: kubelet.service: Service hold-off time over, scheduling restart.
Feb 19 15:37:42 node01 systemd[1]: Stopped kubelet: The Kubernetes Node Agent.
Feb 19 15:37:42 node01 systemd[1]: Started kubelet: The Kubernetes Node Agent.
Feb 19 15:37:42 node01 kubelet[7992]: Flag --cgroup-driver has been deprecated, This parameter should be set via the config file specified by the Kubelet's --config flag. See https://kubernetes.io/docs/tasks/administer-cluster/kubelet-config-file/ for more information.
Feb 19 15:37:42 node01 kubelet[7992]: Flag --cgroup-driver has been deprecated, This parameter should be set via the config file specified by the Kubelet's --config flag. See https://kubernetes.io/docs/tasks/administer-cluster/kubelet-config-file/ for more information.
Feb 19 15:37:42 node01 kubelet[7992]: F0219 15:37:42.650294    7992 server.go:251] unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory


node01 $ cat /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
[Service]
Environment="KUBELET_KUBECONFIG_ARGS=--bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf"
Environment="KUBELET_CONFIG_ARGS=--config=/var/lib/kubelet/config.yaml"
EnvironmentFile=-/var/lib/kubelet/kubeadm-flags.env
EnvironmentFile=-/etc/default/kubelet
ExecStart=
ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS



node01 $ vi /var/lib/kubelet/config.yaml
address: 0.0.0.0
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 2m0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/WRONG-CA-FILE.crt  <<<<<<<<<<<<<<<<
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 5m0s
    cacheUnauthorizedTTL: 30s
cgroupDriver: cgroupfs
cgroupsPerQOS: true
```

</p>


### The cluster is broken again. Investigate and fix the issue.
Fix Cluster

<p>


```bash
master $ k get nodes
NAME     STATUS     ROLES    AGE   VERSION
master   Ready      master   29m   v1.16.0
node01   NotReady   <none>   28m   v1.16.0
  
master $ ssh node01
node01 $ journalctl -u kubelet -f
-- Logs begin at Wed 2020-02-19 15:33:35 UTC. --
Feb 19 16:02:01 node01 kubelet[19163]: E0219 16:02:01.694662   19163 kubelet.go:2267] node "node01" not found
Feb 19 16:02:01 node01 kubelet[19163]: E0219 16:02:01.795278   19163 kubelet.go:2267] node "node01" not found
Feb 19 16:02:01 node01 kubelet[19163]: E0219 16:02:01.819249   19163 reflector.go:123] k8s.io/kubernetes/pkg/kubelet/config/apiserver.go:46: Failed to list *v1.Pod: Get https://172.17.0.25:6553/api/v1/pods?fieldSelector=spec.nodeName%3Dnode01&limit=500&resourceVersion=0: dial tcp 172.17.0.25:6553: connect: connection refused


node01 $ cat /etc/kubernetes/kubelet.conf
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ESXhPVEUxTXpJeU1Wb1hEVE13TURJeE5qRTFNekl5TVZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTjBOCkU5QThWMXpNcHZ1Z3NCNktSY0tLNldSTlR0WkIrRVhoZ09IR3RZQTYzaElUZVF0RkRTRnIxcUlOYmpEaTRwblEKSk96VDZuZ1NwTHBrRng0dy9WSndISXBGandrdEI3YWw5YmRWMzNhamwxejhlcFFmSmRpaW91cTc1OHRTbnNLOApYbHVnK1NmN2s1RkhwU3poRXF0eUdnMElVVUpINTZiczA1aFZZSjg2YTlPenV2dmRRV1Ftak9YYWlEcC9nclpMCk9sZ2hSWjExUldkRDNaRHZDVGgzWkR1RWhIVEdzdnRJeUJuK0VRK2ZmTGprVW1vdWNxTkNMMnVlbmplOEZjU3UKYXpZTXVBYklWN3FzQVBZejJseTRoWlR6S09TU29hYk16Wm5MZStHWjFPL3VmVjVESmI1b3dRTEUxR3JDVFlGLwp6ZUhySFdoVkk2bzRPMEcyTzVrQ0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFBZjdEMHFkR1FGb05MSW8rMGdWM0dQQkp4NW8Kc2xkN0hHZ3h4SjlONGV5NlNSbmFVVUhTNC9WWElhV3Zydmc3enZHbW5ZQ2hPK0t6M0h1NFJMSXJJRXdvMW41Qwo4dFdidkFrc2ZwTStnY0YwZE1TbWJBZHRiNmpWK2NnWWVnNlhrS2VOYkhETU9VMHhyMGM0ajlxL0VtOGhQWmRjCnUyZ2c4bzlKNjBoVTJXd00rbWMwVU5rWU5yNEJVVXdJZk02VGU1NXdPbUNhT2tucWdEYmVBSHRXTkczdkNveSsKV3lrVUxIOGtHYmJ0ejh3T3lyWjY2ZjU5cXFWVmFyMlRJeDNjT2Z4cW83SCtwK3FjRTg5QXptZXY5c29ZY0pwZwp1OVBTd01zdUIrMlZRdHJZeWVhd0dwQktiQ0Zka3Y0bjQvSVhyZ01wWjRiQzN6U0JNd1d2aGh0eTcrUT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://172.17.0.25:6553  <<<<< 172.17.0.25:6443
  name: default-cluster
contexts:

master $ netstat -nap | grep api
tcp6       0      0 :::6443                 :::*                    LISTEN      4224/kube-apiserver
tcp6       0      0 172.17.0.25:6443        172.17.0.25:53434       ESTABLISHED 4224/kube-apiserver
tcp6       0      0 172.17.0.25:6443        172.17.0.26:45916       ESTABLISHED 4224/kube-apiserver

master $ k get nodes
NAME     STATUS   ROLES    AGE   VERSION
master   Ready    master   34m   v1.16.0
node01   Ready    <none>   33m   v1.16.0
```

</p>
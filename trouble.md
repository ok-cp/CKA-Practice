# Troubleshooting 10%


## Application Failure

### [Service-Name]Troubleshooting Test 1: A simple 2 tier application is deployed in the alpha namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

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


### [Serivce TargetPort] Troubleshooting Test 2: The same 2 tier application is deployed in the beta namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

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

### [Env] Troubleshooting Test 4: The same 2 tier application is deployed in the delta namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

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
  
### Troubleshooting Test 5: The same 2 tier application is deployed in the epsilon namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.
  
 aster $ k describe pod/mysql    -n epsilon
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


### Troubleshooting Test 6: The same 2 tier application is deployed in the zeta namespace. It must display a green web page on success. Click on the app tab at the top of your terminal to view your application. It is currently failed. Troubleshoot and fix the issue.

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




## Control Plane Failure


## Worker Node Failure

# Networking 11%

### What is the IP Range configured for the services within the cluster?
<p>

```bash
ps -aux | grep kube-api
-service-cluster-ip-range=10.96.0.0/12 
```

</p>

### How many kube-proxy pods are deployed in this cluster
<p>

```bash
 kubectl get pods -n kube-system
 ```

</p>
### What type of proxy is the kube-proxy configured to use?
<p>

```bash
k logs kube-proxy-jj2xn   -n kube-system
W0218 16:51:34.951442       1 server_others.go:287] Flag proxy-mode="" unknown, assuming iptables proxy
```

</p>

### How does this Kubernetes cluster ensure that a kube-proxy pod runs on all nodes in the cluster?
<p>

```bash
using daemonsets
```

</p>

## CoreDNS

### What is the name of the service created for accessing CoreDNS?
<p>

```bash
 kubectl get service -n kube-system
 ```

</p>
 
### Where is the configuration file located for configuring the CoreDNS service?
 <p>

```bash
master $  kubectl exec coredns-78fcdf6894-6z6x7  -n kube-system ps
PID   USER     TIME   COMMAND
    1 root       0:01 /coredns -conf /etc/coredns/Corefile
   20 root       0:00 ps
```

</p>

### What is the name of the ConfigMap object created for Corefile?
<p>

```bash
kubectl get configmap -n kube-system
```

</p>

### What is the root domain/zone configured for this kubernetes cluster?
<p>

```bash
master $  kubectl describe configmap coredns -n kube-system
Name:         coredns
Namespace:    kube-system
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"v1","data":{"Corefile":".:53 {\n    errors\n    health\n    kubernetes nextgen.cloud in-addr.arpa ip6.arpa {\n       pods insecure\n    ...

Data
====
Corefile:
----
.:53 {
    errors
    health
    kubernetes nextgen.cloud in-addr.arpa ip6.arpa {
       pods insecure
       upstream
       fallthrough in-addr.arpa ip6.arpa
    }
    prometheus :9153
    proxy . /etc/resolv.conf
    cache 30
    reload
}

Events:  <none>
```

</p>

### What name can be used to access the hr web server from the test Application?
You can execute a curl command on the test pod to test. Alternatively, the test Application also has a UI. Access it using the tab at the top of your terminal named"test-app"
<p>

```bash
master $ k get po,svc
NAME                    READY     STATUS    RESTARTS   AGE
pod/hr                  1/1       Running   0          5m
pod/simple-webapp-1     1/1       Running   0          5m
pod/simple-webapp-122   1/1       Running   0          5m
pod/test                1/1       Running   0          5m

NAME                   TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes     ClusterIP   10.96.0.1      <none>        443/TCP        20m
service/test-service   NodePort    10.96.83.156   <none>        80:30080/TCP   5m
service/web-service    ClusterIP   10.97.236.27   <none>        80/TCP         5m
```

</p>

### We just deployed a web server - webapp - that accesses a database mysql - server. However the web server is failing to connect to the database server. Troubleshoot and fix the issue.
<p>

```bash
Web Server: webapp
Uses the right DB_Host name

master $ k get po,svc --all-namespaces
NAMESPACE     NAME                                 READY     STATUS    RESTARTS   AGE
default       pod/hr                               1/1       Running   0          9m
default       pod/simple-webapp-1                  1/1       Running   0          8m
default       pod/simple-webapp-122                1/1       Running   0          8m
default       pod/test                             1/1       Running   0          9m
default       pod/webapp-947cfdd7c-bq6xn           1/1       Running   0          1m
kube-system   pod/coredns-78fcdf6894-6z6x7         1/1       Running   0          23m
kube-system   pod/coredns-78fcdf6894-xl755         1/1       Running   0          23m
kube-system   pod/etcd-master                      1/1       Running   0          22m
kube-system   pod/kube-apiserver-master            1/1       Running   0          22m
kube-system   pod/kube-controller-manager-master   1/1       Running   0          22m
kube-system   pod/kube-proxy-hlrrs                 1/1       Running   0          23m
kube-system   pod/kube-proxy-xfswv                 1/1       Running   0          23m
kube-system   pod/kube-scheduler-master            1/1       Running   0          22m
kube-system   pod/weave-net-468db                  2/2       Running   1          23m
kube-system   pod/weave-net-tthn4                  2/2       Running   1          23m
payroll       pod/mysql                            1/1       Running   0          1m
payroll       pod/web                              1/1       Running   0          9m

NAMESPACE     NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
default       service/kubernetes       ClusterIP   10.96.0.1       <none>        443/TCP        23m
default       service/test-service     NodePort    10.96.83.156    <none>        80:30080/TCP     9m
default       service/web-service      ClusterIP   10.97.236.27    <none>        80/TCP        9m
default       service/webapp-service   NodePort    10.105.95.165   <none>        8080:30082/TCP   1m
kube-system   service/kube-dns         ClusterIP   10.96.0.10      <none>        53/UDP,53/TCP    23m
payroll       service/mysql            ClusterIP   10.98.50.60     <none>        3306/TCP        1m
payroll       service/web-service      ClusterIP   10.100.58.167   <none>        80/TCP        9m
  
  
    spec:
      containers:
      - env:
        - name: DB_Host
          value: mysql.payroll
        - name: DB_User
          value: root
        - name: DB_Password
          value: paswrd
        image: mmumshad/simple-webapp-mysql
```

</p>

### From the hr pod nslookup the mysql service and redirect the output to a file /root/nslookup.out
<p>

```bash
kubectl exec hr -it -- nslookup mysql.payroll

master $ kubectl exec hr -it -- nslookup mysql.payroll > /root/nslookup.out

Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   mysql.payroll.svc.nextgen.cloud
Address: 10.98.50.60
```

</p>

## Ingress

### What is the Host configured on the ingress-resource?
<p>

```bash
$ kubectl describe ingress --namespace app-space
Name:             ingress-wear-watch
Namespace:        app-space
Address:
Default backend:  default-http-backend:80 (<none>)
Rules:
  Host  Path  Backends  ----  ----  --------
  *
        /wear    wear-service:8080 (10.32.0.5:8080)
        /watch   video-service:8080 (10.32.0.2:8080)
Annotations:
  nginx.ingress.kubernetes.io/rewrite-target:  /
  nginx.ingress.kubernetes.io/ssl-redirect:    false
Events:
  Type    Reason  Age   From                      Message
  ----    ------  ----  ----                      -------
  Normal  CREATE  79s   nginx-ingress-controller  Ingress app-space/ingress-wear-watch
  Normal  UPDATE  79s   nginx-ingress-controller  Ingress app-space/ingress-wear-watch
```

</p>

### You are requested to change the URLs at which the applications are made available.
<p>

```bash
Ingress: ingress-wear-watch
Path: /stream
Backend Service: video-service
Backend Service Port: 8080

apiVersion: v1
items:
- apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    annotations:
      nginx.ingress.kubernetes.io/rewrite-target: /
      nginx.ingress.kubernetes.io/ssl-redirect: "false"
    creationTimestamp: "2020-02-18T17:35:30Z"
    generation: 4
    name: ingress-wear-watch
    namespace: app-space
    resourceVersion: "1144"
    selfLink: /apis/extensions/v1beta1/namespaces/app-space/ingresses/ingress-wear-watch
    uid: 8326c94d-922b-4577-a67a-c06c039f94c7
  spec:
    rules:
    - http:
        paths:
        - backend:
            serviceName: wear-service
            servicePort: 8080
          path: /wear
        - backend:
            serviceName: video-service
            servicePort: 8080
          path: /stream
        - backend:
            serviceName: food-service
            servicePort: 8080
          path: /eat
```

</p>

### You are requested to add a new path to your ingress to make the food delivery application available to your customers.
<p>

```bash
Ingress: ingress-wear-watch
Path: /eat
Backend Service: food-service
Backend Service Port: 8080

  spec:
    rules:
    - http:
        paths:
        - backend:
            serviceName: wear-service
            servicePort: 8080
          path: /wear
        - backend:
            serviceName: video-service
            servicePort: 8080
          path: /stream
        - backend:
            serviceName: food-service
            servicePort: 8080
          path: /eat
 ```

</p>         

### You are requested to make the new application available at /pay.
Identify and implement the best approach to making this application available on the ingress controller and test to make sure its working. Look into annotations: rewrite-target as well.
<p>

```bash
Ingress Created
Path: /pay
Configure correct backend service
Configure correct backend port


master $ k get po,svc,ingress -n critical-space
NAME                             READY   STATUS    RESTARTS   AGE
pod/webapp-pay-5d8c86d5c-sshzh   1/1     Running   0          2m49s

NAME                  TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/pay-service   ClusterIP   10.110.4.1   <none>        8282/TCP   2m49s

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress
  namespace: critical-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /pay
        backend:
          serviceName: pay-service
          servicePort: 8282
```

</p>

### Let us now create a service to make Ingress available to external users.
<p>

```bash
Name: ingress
Type: NodePort
Port: 80
TargetPort: 80
NodePort: 30080
Use the right selector

kubectl expose deployment -n ingress-space ingress-controller --type=NodePort --port=80 --name=ingress -n ingress-space --dry-run -o yaml >ingress.yaml
```

</p>

### Create the ingress resource to make the applications available at /wear and /watch on the Ingress service.
Ingress Created
Path: /wear
Path: /watch
Configure correct backend service for /wear
Configure correct backend service for /watch
Configure correct backend port for /wear service
Configure correct backend port for /watch service

<p>

```bash
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-wear-watch
  namespace: app-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
spec:
  rules:
  - http:
      paths:
      - path: /wear
        backend:
          serviceName: wear-service
          servicePort: 8080
      - path: /watch
        backend:
          serviceName: video-service
          servicePort: 8080
 ```

</p>         
          
          


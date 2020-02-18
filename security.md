# Security 12%

## TLS

### What is the Common Name (CN) configured on the Kube API Server Certificate?

openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text

### What is the Common Name (CN) configured on the ETCD Server certificate?

Run the command openssl x509 -in /etc/kubernetes/pki/etcd/server.crt -text and look for Subject CN.

### How long, from the issued date, is the Root CA Certificate valid for?

openssl x509 -in /etc/kubernetes/pki/ca.crt -text

### Kubectl suddenly stops responding to your commands. Check it out! Someone recently modified the /etc/kubernetes/manifests/etcd.yaml file

Inspect the --cert-file option in the manifests file.

### The kube-api server stopped again! Check it out. Inspect the kube-api server logs and identify the root cause and fix the issue.

ETCD has its own CA. The right CA must be used for the ETCD-CA file in /etc/kubernetes/manifests/kube-apiserver.yaml. View answer at /var/answers/kube-apiserver.yaml

### The kube-api server stops responding one day when it tries to connect to ETCD server.

openssl x509 -req -in /etc/kubernetes/pki/apiserver-etcd-client.csr -CA /etc/kubernetes/pki/etcd/ca.crt -CAkey /etc/kubernetes/pki/etcd/ca.key -CAcreateserial -out /etc/kubernetes/pki/apiserver-etcd-client.crt



### A new member akshay joined our team. He requires access to our cluster. The Certificate Signing Request is at the /root location. Create a CertificateSigningRequest object with the name akshay with the contents of the akshay.csr file
CSR akshay created
Right CSR is used

master $ ls
akshay.csr  akshay.key 

apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: akshay
spec:
  request: $(cat akshay.csr | base64 | tr -d '\n')
  usages:
  - digital signature
  - key encipherment
  - server auth
  
  
### What is the Condition of the newly created Certificate Signing Request object?
 
 kubectl get csr

NAME                                                   AGE       REQUESTOR                 CONDITION
akshay                                                 2m        kubernetes-admin          Pending
csr-g5q68                                              11m       system:node:master        Approved,Issued
node-csr-ixMy6kbEKBIf-40OuBJh0xCDgn8VVFkpWSJig-TO1OQ   11m       system:bootstrap:96771a   Approved,Issued

### Who requested the csr-* request?

master node

 
### Approve the CSR Request
  CSR Approved

master $  kubectl certificate approve akshay
certificatesigningrequest.certificates.k8s.io/akshay approved
  


### You are not aware of a request coming in. What groups is this CSR requesting access to?

$ kubectl get csr agent-smith -o yaml


apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  creationTimestamp: 2020-02-18T14:31:36Z
  name: agent-smith
  resourceVersion: "1605"
  selfLink: /apis/certificates.k8s.io/v1beta1/certificatesigningrequests/agent-smith
  uid: 5e0db8e0-525b-11ea-84f1-0242ac110037
spec:
  groups:
  - system:masters
  - system:authenticated
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1dEQ0NBVUFDQVFBd0V6RVJNQThHQTFVRUF3d0libVYzTFhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQgpBUVVBQTRJQkR3QXdnZ0VLQW9JQkFRRE8wV0pXK0RYc0FKU0lyanBObzV2UklCcGxuemcrNnhjOStVVndrS2kwCkxmQzI3dCsxZUVuT041TXVxOTlOZXZtTUVPbnJEVU8vdGh5VnFQMncyWE5JRFJYall5RjQwRmJtRCs1eld5Q0sKeTNCaWhoQjkzTUo3T3FsM1VUdlo4VEVMcXlhRGtuUmwvanYvU3hnWGtvazBBQlVUcFdNeDRCcFNpS2IwVSt0RQpJRjVueEF0dE1Wa0RQUTdOYmVaUkc0M2IrUVdsVkdSL3o2RFdPZkpuYmZlek90YUF5ZEdMVFpGQy93VHB6NTJrCkVjQ1hBd3FDaGpCTGt6MkJIUFI0Sjg5RDZYYjhrMzlwdTZqcHluZ1Y2dVAwdEliT3pwcU52MFkwcWRFWnB3bXcKajJxRUwraFpFV2trRno4MGxOTnR5VDVMeE1xRU5EQ25JZ3dDNEdaaVJHYnJBZ01CQUFHZ0FEQU5CZ2txaGtpRwo5dzBCQVFzRkFBT0NBUUVBUzlpUzZDMXV4VHVmNUJCWVNVN1FGUUhVemFsTnhBZFlzYU9SUlFOd0had0hxR2k0CmhPSzRhMnp5TnlpNDRPT2lqeWFENnRVVzhEU3hrcjhCTEs4S2czc3JSRXRKcWw1ckxaeTlMUlZyc0pnaEQ0Z1kKUDlOTCthRFJTeFJPVlNxQmFCMm5XZVlwTTVjSjVURjUzbGVzTlNOTUxRMisrUk1uakRRSjdqdVBFaWM4L2RoawpXcjJFVU02VWF3enlrcmRISW13VHYybWxNWTBSK0ROdFYxWWllKzBIOS9ZRWx0K0ZTR2poNUw1WVV2STFEcWl5CjRsM0UveTNxTDcxV2ZBY3VIM09zVnBVVW5RSVNNZFFzMHFXQ3NiRTU2Q0M1RGhQR1pJcFVibktVcEF3a2ErOEUKdndRMDdqRytocGtueG11RkFlWHhnVXdvZEFMYUo3anUvVERJY3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0K
  usages:
  - digital signature
  - key encipherment
  - server auth
  username: agent-x
status: {}


### That doesn't look very right. Reject that request.
CSR agent-smith deny

$ kubectl certificate deny agent-smith

### Let's get rid of it. Delete the new CSR object
CSR agent-smith deleted

$ kubectl delete csr agent-smith

## kubeconfig

### Where is the default kubeconfig file located in the current environment?
Find the current home directory by looking at the HOME environment variable

master $ ls /root/.kube/config
/root/.kube/config

### How many Users are defined in the default kubeconfig file?

Run the command 'kubectl config view' and count the number of users

### A new kubeconfig file named 'my-kube-config' is created. It is placed in the /root directory. How many clusters are defined in the kubectl file?

Run the command 'kubectl config view --kubeconfig my-kube-config'

apiVersion: v1
clusters:
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: KUBE_ADDRESS
  name: development
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: KUBE_ADDRESS
  name: kubernetes-on-aws
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: KUBE_ADDRESS
  name: production
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: KUBE_ADDRESS
  name: test-cluster-1
contexts:
- context:
    cluster: kubernetes-on-aws
    user: aws-user
  name: aws-user@kubernetes-on-aws
- context:
    cluster: test-cluster-1
    user: dev-user
  name: research
- context:
    cluster: development
    user: test-user
  name: test-user@development
- context:
    cluster: production
    user: test-user
  name: test-user@production
current-context: test-user@development
kind: Config
preferences: {}
users:
- name: aws-user
  user:
    client-certificate: /etc/kubernetes/pki/users/aws-user/aws-user.crt
    client-key: /etc/kubernetes/pki/users/aws-user/aws-user.key
- name: dev-user
  user:
    client-certificate: /etc/kubernetes/pki/users/dev-user/developer-user.crt
    client-key: /etc/kubernetes/pki/users/dev-user/dev-user.key
- name: test-user
  user:
    client-certificate: /etc/kubernetes/pki/users/test-user/test-user.crt
    client-key: /etc/kubernetes/pki/users/test-user/test-user.key

### I would like to use the dev-user to access test-cluster-1. Set the current context to the right one so I can do that.

Run the command kubectl config --kubeconfig=/root/my-kube-config use-context research

### We don't want to have to specify the kubeconfig file option on each command. Make the my-kube-config file the default kubeconfig.

Replace the contents in the default kubeconfig file with the content from my-kube-config file.

master $ cd /root/
master $ ls
my-kube-config

master $ cd .kube/
master $ ls
cache  config  http-cache

master $ mv config config.org
master $ mv ../my-kube-config .
master $ ls -al
total 28
drwxr-xr-x 4 root root 4096 Feb 18 14:49 .
drwx------ 5 root root 4096 Feb 18 14:49 ..
drwxr-xr-x 3 root root 4096 Feb 18 14:39 cache
-rw------- 1 root root 5451 Feb 18 14:39 config.org
drwxr-xr-x 3 root root 4096 Feb 18 14:39 http-cache
-rw-r--r-- 1 root root 1380 Feb 18 14:47 my-kube-config
master $ mv my-kube-config config
master $ ls
cache  config  config.org  http-cache


### With the current-context set to research, we are trying to access the cluster. However something seems to be wrong. Identify and fix the issue. 
Try running the kubectl get pods command and look for the error. All users certificates are stored at /etc/kubernetes/pki/users

$ k get po
error: unable to read client-cert /etc/kubernetes/pki/users/dev-user/developer-user.crt for dev-user due to open /etc/kubernetes/pki/users/dev-user/developer-user.crt: no such file or directory

 $ ls /etc/kubernetes/pki/users/dev-user/
dev-user.crt  dev-user.csr  dev-user.key
$ vi config

users:
- name: aws-user
  user:
    client-certificate: /etc/kubernetes/pki/users/aws-user/aws-user.crt
    client-key: /etc/kubernetes/pki/users/aws-user/aws-user.key
 
 
 ## RBAC - Role,Rolebinding
 
 ### Inspect the environment and identify the authorization modes configured on the cluster.
 
 $ kubectl describe pod kube-apiserver-master -n kube-system
 
     Command:
      kube-apiserver
      --authorization-mode=Node,RBAC
      --advertise-address=172.17.0.114

### How many roles exist in the default namespace?

kubectl get roles


### What are the resources the weave-net role in the kube-system namespce is given access to?

$ kubectl describe role weave-net -n kube-system

Name:         weave-net
Labels:       name=weave-net
Annotations:  cloud.weave.works/launcher-info={
  "original-request": {
    "url": "/k8s/v1.8/net.yaml?k8s-version=v1.10.0",
    "date": "Wed Mar 28 2018 10:24:34 GMT+0000 (UTC)"
  },
  "email-address": "support@we...
  kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"rbac.authorization.k8s.io/v1beta1","kind":"Role","metadata":{"annotations":{"cloud.weave.works/launcher-info":"{\n  \"original-request\"...
PolicyRule:
  Resources   Non-Resource URLs  Resource Names  Verbs
  ---------   -----------------  --------------  -----
  configmaps  []                 []              [create]
  configmaps  []                 [weave-net]     [get update]
  
  
  ### Which account is the weave-net role assigned to it?
  
  $ kubectl describe rolebinding weave-net -n kube-system
  
  Name:         weave-net
Labels:       name=weave-net
Annotations:  cloud.weave.works/launcher-info={
  "original-request": {
    "url": "/k8s/v1.8/net.yaml?k8s-version=v1.10.0",
    "date": "Wed Mar 28 2018 10:24:34 GMT+0000 (UTC)"
  },
  "email-address": "support@we...
  kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"rbac.authorization.k8s.io/v1beta1","kind":"RoleBinding","metadata":{"annotations":{"cloud.weave.works/launcher-info":"{\n  \"original-re...
Role:
  Kind:  Role
  Name:  weave-net
Subjects:
  Kind            Name       Namespace
  ----            ----       ---------
  ServiceAccount  weave-net  kube-system
  
  
### A user dev-user is created. User's details have been added to the kubeconfig file. Inspect the permissions granted to the user. Check if the user can list pods in the default namespace.
Use the --as dev-user option with kubectl to run commands as the dev-user

$ kubectl get pods --as dev-user

### Create the necessary roles and role bindings required for the dev-user to create, list and delete pods in the default namespace.
Role: developer
Role Resources: pods
Role Actions: list
Role Actions: create
RoleBinding: dev-user-binding
RoleBinding: Bound to dev-user

master $ k create role developer --resource=pods --verb=list,create
role.rbac.authorization.k8s.io/developer created

master $ k create rolebinding dev-user-binding --role=developer --user=dev-user
rolebinding.rbac.authorization.k8s.io/dev-user-binding created

### The dev-user is trying to get details about the dark-blue-app pod in the blue namespace. Investigate and fix the issue.

master $ k get po dark-blue-app -n blue --as dev-user
No resources found.
Error from server (Forbidden): pods "dark-blue-app" is forbidden: User "dev-user" cannot get pods in the namespace "blue"

master $ k get po dark-blue-app -n blue --as dev-user
NAME            READY     STATUS    RESTARTS   AGE
dark-blue-app   1/1       Running   0          3m

master $ k describe role developer -n blue
Name:         developer
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources  Non-Resource URLs  Resource Names   Verbs
  ---------  -----------------  --------------   -----
  pods       []                 [blue-app]       [get watch create delete]


master $ k edit role.rbac.authorization.k8s.io/developer -n blue

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  creationTimestamp: 2020-02-18T15:25:18Z
  name: developer
  namespace: blue
  resourceVersion: "2964"
  selfLink: /apis/rbac.authorization.k8s.io/v1/namespaces/blue/roles/developer
  uid: de8fb181-5262-11ea-af4c-0242ac110007
rules:
- apiGroups:
  - ""
  resourceNames:
  - blue-app
  - dark-blue-app
  resources:
  - pods
  verbs:
  - get
  - watch
  - create
  - delete

master $ k get po dark-blue-app -n blue --as dev-user
NAME            READY     STATUS    RESTARTS   AGE
dark-blue-app   1/1       Running   0          3m


### Grant the dev-user permissions to create deployments in the blue namespace. Remember to add both groups "apps" and "extensions"

master $ k create role deploy-role --resource=deployments --verb=create -n blue
role.rbac.authorization.k8s.io/deploy-blue created
master $ k create rolebinding dev-user-deploy-binding --role=deploy-role --user=dev-user -n blue
rolebinding.rbac.authorization.k8s.io/deploy-dev-user-binding created

---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: blue
  name: deploy-role
rules:
- apiGroups: ["apps", "extensions"]
  resources: ["deployments"]
  verbs: ["create"]

---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-deploy-binding
  namespace: blue
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: deploy-role
  apiGroup: rbac.authorization.k8s.io
  
  
## RBAC - Clusterrole,Clusterrolebinding  

### How many ClusterRoles do you see defined in the cluster?

kubectl get clusterroles --no-headers | wc -l


### How many ClusterRoleBindings exist on the cluster?

kubectl get clusterrolebindings --no-headers | wc -l 


### What user/groups are the cluster-admin role bound to?

master $ kubectl describe clusterrolebinding cluster-admin
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate=true
Role:
  Kind:  ClusterRole
  Name:  cluster-admin
Subjects:
  Kind   Name            Namespace
  ----   ----            ---------
  Group  system:masters
 
 ### What level of permission does the cluster-admin role grant?
 
 master $ kubectl describe clusterrolebinding cluster-admin
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate=true
Role:
  Kind:  ClusterRole
  Name:  cluster-admin
Subjects:
  Kind   Name            Namespace
  ----   ----            ---------
  Group  system:masters
master $ ^C
master $ kubectl describe clusterrole cluster-admin
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate=true
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  *.*        []                 []              [*]
             [*]                []              [*]
             
             
 ### A new user michelle joined the team. She will be focusing on the nodes in the cluster. Create the required ClusterRoles and ClusterRoleBindings so she gets access to the nodes.
 
master $ kubectl create clusterrole node-admin --verb=get,create,delete,list,watch --resource=nodes
clusterrole.rbac.authorization.k8s.io/node-admin created
master $ kubectl create clusterrolebinding node-cluster-admin-binding --clusterrole=node-admin --user=michelle
clusterrolebinding.rbac.authorization.k8s.io/node-cluster-admin-binding created

---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: node-admin
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "watch", "list", "create", "delete"]

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: michelle-binding
subjects:
- kind: User
  name: michelle
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: node-admin
  apiGroup: rbac.authorization.k8s.io
 

### michelle's responsibilities are growing and now she will be responsible for storage as well. Create the required ClusterRoles and ClusterRoleBindings to allow her access to Storage.

ClusterRole: storage-admin
Resource: persistentvolumes
Resource: storageclasses
ClusterRoleBinding: michelle-storage-admin
ClusterRoleBinding Subject: michelle
ClusterRoleBinding Role: storage-admin


master $ kubectl create clusterrole storage-admin --verb=get,create,delete,list,watch --resource=persistentvolumes,storageclasses
clusterrole.rbac.authorization.k8s.io/storage-admin created
master $ kubectl create clusterrolebinding michelle-storage-admin --clusterrole=storage-admin --user=michelle
clusterrolebinding.rbac.authorization.k8s.io/michelle-storage-admin created


---
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: storage-admin
rules:
- apiGroups: [""]
  resources: ["persistentvolumes"]
  verbs: ["get", "watch", "list", "create", "delete"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "watch", "list", "create", "delete"]

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: michelle-storage-admin
subjects:
- kind: User
  name: michelle
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: storage-admin
  apiGroup: rbac.authorization.k8s.io


## Image Security

### We decided to use a modified version of the application from an internal private registry. Update the image of the deployment to use a new image from myprivateregistry.com:5000

Use the kubectl edit deployment command to edit the image name to myprivateregistry.com:5000/nginx:alpine


### Create a secret object with the credentials required to access the registry
Name: private-reg-cred
Username: dock_user
Password: dock_password
Server: myprivateregistry.com:5000
Email: dock_user@myprivateregistry.com
Secret: private-reg-cred
Secret Type: docker-registry
Secret Data

kubectl create secret docker-registry private-reg-cred --docker-username=dock_user --docker-password=dock_password --docker-server=myprivateregistry.com:5000 --docker-email=dock_user@myprivateregistry.com


### Configure the deployment to use credentials from the new secret to pull images from the private registry
Image Pull Secret: private-reg-cred

Edit deployment using kubectl edit deploy web command and add imagePullSecrets section. Use private-reg-cred

    spec:
      containers:
      - image: myprivateregistry.com:5000/nginx:alpine
        imagePullPolicy: IfNotPresent
        name: web
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: private-reg-cred


## Security Contexts

### What is the user used to execute the sleep process within the 'ubuntu-sleeper' pod? in the current(default) namespace

Run the command 'kubectl exec ubuntu-sleeper whoami' and count the number of pods.

### Edit the pod 'ubuntu-sleeper' to run the sleep process with user ID 1010. Note: Only make the necessary changes. Do not modify the name or image of the pod.
Pod Name: ubuntu-sleeper
Image Name: ubuntu
SecurityContext: User 1010

  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    imagePullPolicy: Always
    name: ubuntu
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: default-token-xzrbd
      readOnly: true
  securityContext:
    runAsUser: 1010

### A Pod definition file named 'multi-pod.yaml' is given. With what user are the processes in the 'web' container started?
The pod is created with multiple containers and security contexts defined at the POD and Container level

apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  securityContext:
    runAsUser: 1001
  containers:
  -  image: ubuntu
     name: web
     command: ["sleep", "5000"]
     securityContext:
      runAsUser: 1002

  -  image: ubuntu
     name: sidecar
     command: ["sleep", "5000"]


### Update pod 'ubuntu-sleeper' to run as Root user and with the 'SYS_TIME' capability. Note: Only make the necessary changes. Do not modify the name of the pod.

Pod Name: ubuntu-sleeper
Image Name: ubuntu
SecurityContext: Capability SYS_TIME

spec:
  containers:
  - command:
    - sleep
    - "4800"
    image: ubuntu
    imagePullPolicy: Always
    name: ubuntu
    securityContext:
      capabilities:
        add: [ "SYS_TIME"]

## Network Polices

### How many network policies do you see in the environment? We have deployed few web applications, services and network policies. Inspect the environment.

master $ k get netpol
NAME             POD-SELECTOR   AGE
payroll-policy   name=payroll   39s

### What type of traffic is this Network Policy configured to handle?

master $ kubectl describe networkpolicy
Name:         payroll-policy
Namespace:    default
Created on:   2020-02-18 16:26:36 +0000 UTC
Labels:       <none>
Annotations:  <none>
Spec:
  PodSelector:     name=payroll
  Allowing ingress traffic:
    To Port: 8080/TCP
    From:
      PodSelector: name=internal
  Allowing egress traffic:
    <none> (Selected pods are isolated for egress connectivity)
  Policy Types: Ingress

### Create a network policy to allow traffic from the 'Internal' application only to the 'payroll-service' and 'db-service'
Use the spec given on the right. You might want to enable ingress traffic to the pod to test your rules in the UI.

Policy Name: internal-policy
Policy Types: Egress
Egress Allow: payroll
Payroll Port: 8080
Egress Allow: mysql
MYSQL Port: 3306

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      name: internal
  policyTypes:
  - Egress
  - Ingress
  ingress:
    - {}
  egress:
  - to:
    - podSelector:
        matchLabels:
          name: mysql
    ports:
    - protocol: TCP
      port: 3306

  - to:
    - podSelector:
        matchLabels:
          name: payroll
    ports:
    - protocol: TCP
      port: 8080


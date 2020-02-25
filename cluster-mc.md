# Cluster Maintenance 11%

Cluster 관리 방법 drain/uncordon/cordon 사용 방법 이해, Etcd Backup 방법 이해가 필요하다.

### We need to take node01 out for maintenance. Empty the node of all applications and mark it unschedulable.

* Node node01 Unschedulable   
* Pods evicted from node01   

<p>

```bash
$ kubectl drain node01 --ignore-daemonsets
```

</p>
</br>


### The maintenance tasks have been completed. Configure the node to be schedulable again.
* Node01 is Schedulable   

<p>

```bash
$ kubectl uncordon node01 (--force)
```

</p>
</br>

### Node03 has our critical applications. We do not want to schedule any more apps on node03. Mark node03 as unschedulable but do not remove any apps currently running on it .
 
* Node03 Unschedulable    
* Node03 has apps   

<p>

```bash
$ kubectl cordon node03
```

</p>

</br>
</br>
</br>

## Upgrade
### What is the latest stable version available for upgrade?

<p>

```bash
$ kubeadm upgrade plan
```

</p>
</br>


### We will be upgrading the master node first. Drain the master node of workloads and mark it UnSchedulable
 
Master Node: SchedulingDisabled

<p>

```bash 
kubectl drain master --ignore-daemonsets
```

</p>
</br>
 
 ### Upgrade the master components to v1.12.0. Upgrade kubeadm tool, then master components, and finally the kubelet. Practice referring to the kubernetes documentation page. Note: While upgrading kubelet, if you hit dependency issue while running the apt-get upgrade kubelet command, use the apt install kubelet=1.12.0-00 command instead
 
* Master Upgraded to v1.12.0   
* Master Kubelet Upgraded to v1.12.0   

<p>

```bash
$ Run the command    
$ apt install kubeadm=1.12.0-00    
$ kubeadm upgrade apply v1.12.0    
$ apt install kubelet=1.12.0-00    
```

</p>
</br>


### Mark the master node as "Schedulable" again

* Master Node: Ready & Schedulable    
<p>

```bash
$ kubectl uncordon master
```

</p>
</br>

### Next is the worker node. Drain the worker node of the workloads and mark it UnSchedulable
* Worker node: Unschedulable

<p>

```bash
$ kubectl drain node01 --ignore-daemonsets
```

</p>
</br>


### Upgrade the worker node to v1.12.0

<p>

```bash
$ ssh node01
$ apt install kubeadm=1.12.0-00
$ apt install kubelet=1.12.0-00

$ kubeadm upgrade node config --kubelet-version $(kubelet --version | cut -d ' ' -f 2)
```

</p>


### Remove the restriction and mark the worker node as schedulable again.
Worker Node: Schedulable

<p>

```bash
$ kubectl uncordon node01
```

</p>
</br>   
</br>
</br>

## Backup  
### The master nodes in our cluster are planned for a regular maintenance reboot tonight. While we do not anticipate anything to go wrong, we are required to take the necessary backups. Take a snapshot of the ETCD database using the built-in snapshot functionality.
* Store the backup file at location /tmp/snapshot-pre-boot.db    
* Backup ETCD to /tmp/snapshot-pre-boot.db    
* Name: ingress-space    

<p>

```bash
$ ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key snapshot save /tmp/snapshot-pre-boot.db
Snapshot saved at /tmp/snapshot-pre-boot.db
```

</p>
</br>

### Luckily we took a backup. Restore the original state of the cluster using the backup file.
 
<p>

```bash
$ ETCDCTL_API=3 etcdctl --endpoints=https://[127.0.0.1]:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt \
     --name=master \
     --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key \
     --data-dir /var/lib/etcd-from-backup \
     --initial-cluster=master=https://127.0.0.1:2380 \
     --initial-cluster-token etcd-cluster-1 \
     --initial-advertise-peer-urls=https://127.0.0.1:2380 \
     snapshot restore /tmp/snapshot-pre-boot.db


/etc/kubernetes/manifests/etcd.yaml


Update --data-dir to use new target location

--data-dir=/var/lib/etcd-from-backup

Update new initial-cluster-token to specify new cluster

--initial-cluster-token=etcd-cluster-1


Update volumes and volume mounts to point to new path

    volumeMounts:
    - mountPath: /var/lib/etcd-from-backup
      name: etcd-data
    - mountPath: /etc/kubernetes/pki/etcd
      name: etcd-certs
  hostNetwork: true
  priorityClassName: system-cluster-critical
  volumes:
  - hostPath:
      path: /var/lib/etcd-from-backup
      type: DirectoryOrCreate
    name: etcd-data
  - hostPath:
      path: /etc/kubernetes/pki/etcd
      type: DirectoryOrCreate
    name: etcd-certs
```

</p>
# Cluster Maintenance 11%

### We need to take node01 out for maintenance. Empty the node of all applications and mark it unschedulable.

Node node01 Unschedulable
Pods evicted from node01

kubectl drain node01 --ignore-daemonsets

### The maintenance tasks have been completed. Configure the node to be schedulable again.
Node01 is Schedulable

 kubectl uncordon node01 (--force)
 
 ### Node03 has our critical applications. We do not want to schedule any more apps on node03. Mark node03 as unschedulable but do not remove any apps currently running on it .
 
 Node03 Unschedulable
Node03 has apps

kubectl cordon node03


## Upgrade

### What is the latest stable version available for upgrade?

 kubeadm upgrade plan
 
 ### We will be upgrading the master node first. Drain the master node of workloads and mark it UnSchedulable
 
 Master Node: SchedulingDisabled
 
 kubectl drain master --ignore-daemonsets
 
 
 ### Upgrade the master components to v1.12.0. Upgrade kubeadm tool, then master components, and finally the kubelet. Practice referring to the kubernetes documentation page. Note: While upgrading kubelet, if you hit dependency issue while running the apt-get upgrade kubelet command, use the apt install kubelet=1.12.0-00 command instead
 
 Master Upgraded to v1.12.0
Master Kubelet Upgraded to v1.12.0

Run the command 
apt install kubeadm=1.12.0-00
kubeadm upgrade apply v1.12.0
apt install kubelet=1.12.0-00

### Mark the master node as "Schedulable" again

Master Node: Ready & Schedulable
kubectl uncordon master


### Next is the worker node. Drain the worker node of the workloads and mark it UnSchedulable
Worker node: Unschedulable

kubectl drain node01 --ignore-daemonsets


### Upgrade the worker node to v1.12.0

ssh node01
apt install kubeadm=1.12.0-00
apt install kubelet=1.12.0-00

kubeadm upgrade node config --kubelet-version $(kubelet --version | cut -d ' ' -f 2)


### Remove the restriction and mark the worker node as schedulable again.
Worker Node: Schedulable

kubectl uncordon node01

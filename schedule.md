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
###  How many PODs are in the 'finance' business unit ('bu')?
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

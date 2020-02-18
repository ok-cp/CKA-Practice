# Logging / Monitoring 5%

### Identify the node that consumes the most CPU/Memory.

kubectl top node

### Identify the pod that consumes the most CPU/Memory.

kubectl top pod


### A user - 'USER5' - has expressed concerns accessing the application. Identify the cause of the issue. Inspect the logs of the POD

k get po
NAME       READY   STATUS    RESTARTS   AGE
webapp-1   1/1     Running   0          24s

k logs webapp-1
[2020-02-18 02:17:32,920] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.

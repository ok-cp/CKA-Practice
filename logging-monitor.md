# Logging / Monitoring 5%

Pod의 CPU/Memory 사용률 확인 방법, Pod log 확인 방법을 알고 있어야한다.

### Identify the node that consumes the most CPU/Memory.

<p>

```bash
kubectl top node
```

</p>
</br>

### Identify the pod that consumes the most CPU/Memory.

<p>

```bash
kubectl top pod
```

</p>
</br>

### A user - 'USER5' - has expressed concerns accessing the application. Identify the cause of the issue. Inspect the logs of the POD

<p>

```bash
$ kubectl get po
NAME       READY   STATUS    RESTARTS   AGE
webapp-1   1/1     Running   0          24s

$ kubectl logs webapp-1
[2020-02-18 02:17:32,920] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
```

</p>
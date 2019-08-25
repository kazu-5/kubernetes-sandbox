# kubernetes-sandbox
Docker/Kubernetes 実践コンテナ開発入門(https://gihyo.jp/book/2018/978-4-297-10033-9) の学習用

## 5章
### 5.6 Pod
```
kubectl apply -f simple-pod.yaml
```

```
kubectl exec -it simple-echo sh -c nginx
# 
```

```
kubectl logs -f simple-echo -c echo
2019/08/21 16:57:52 start server
```

```
kubectl delete pod simple-echo
pod "simple-echo" deleted
```

### 5.7 ReplicaSet
```
kubectl apply -f simple-replicaset.yaml 
replicaset.apps/echo created
```

```
kubectl delete -f simple-replicaset.yaml
replicaset.apps "echo" deleted
```

### 5.8 Deployment
```
kubectl apply -f simple-deployment.yaml --record
deployment.apps/echo created
```

```
kubectl get pods,replicaset,deployment --selector app=echo
NAME                        READY   STATUS    RESTARTS   AGE
pod/echo-58b7bc5f6c-5fstn   2/2     Running   0          112s
pod/echo-58b7bc5f6c-85wtw   2/2     Running   0          112s
pod/echo-58b7bc5f6c-fqmz5   2/2     Running   0          112s

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.extensions/echo-58b7bc5f6c   3         3         3       112s

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/echo   3/3     3            3           112s
```

```
kubectl rollout history deployment echo
deployment.extensions/echo 
REVISION  CHANGE-CAUSE
1         kubectl apply --filename=simple-deployment.yaml --record=true
```

```
simple-deployment.yamlのreplicasを「3」から「4」に変更
```

```
kubectl apply -f simple-deployment.yaml --record
deployment.apps/echo configured
```

```
kubectl get pods,replicaset,deployment --selector app=echo
NAME                        READY   STATUS    RESTARTS   AGE
pod/echo-58b7bc5f6c-5fstn   2/2     Running   0          19m
pod/echo-58b7bc5f6c-85wtw   2/2     Running   0          19m
pod/echo-58b7bc5f6c-fqmz5   2/2     Running   0          19m
pod/echo-58b7bc5f6c-z6g29   2/2     Running   0          12s

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.extensions/echo-58b7bc5f6c   4         4         4       19m

NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/echo   4/4     4            4           19m
```



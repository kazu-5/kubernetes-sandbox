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

```
simple-deployment.yamlの「nginx」containerのimageを「gihyodocker/nginx:latest」から「image: gihyodocker/nginx:patched」に変更
```

```
kubectl apply -f simple-deployment.yaml --record
deployment.apps/echo configured
```

```
kubectl get pod --selector app=echo
NAME                    READY   STATUS             RESTARTS   AGE
echo-58b7bc5f6c-5fstn   2/2     Running            0          28m
echo-58b7bc5f6c-85wtw   2/2     Running            0          28m
echo-58b7bc5f6c-fqmz5   2/2     Running            0          28m
echo-58b7bc5f6c-z6g29   0/2     Terminating        0          8m41s
echo-7ff6b5c99f-472wj   1/2     ImagePullBackOff   0          32s
echo-7ff6b5c99f-l8txf   1/2     ImagePullBackOff   0          32s
```

```
kubectl rollout history deployment echo
deployment.extensions/echo 
REVISION  CHANGE-CAUSE
1         kubectl apply --filename=simple-deployment.yaml --record=true
2         kubectl apply --filename=simple-deployment.yaml --record=true
```

```
kubectl rollout history deployment echo --revision=1
deployment.extensions/echo with revision #1
Pod Template:
  Labels:	app=echo
	pod-template-hash=58b7bc5f6c
  Annotations:	kubernetes.io/change-cause: kubectl apply --filename=simple-deployment.yaml --record=true
  Containers:
   nginx:
    Image:	gihyodocker/nginx:latest
    Port:	80/TCP
    Host Port:	0/TCP
    Environment:
      BACKEND_HOST:	localhost:8080
    Mounts:	<none>
   echo:
    Image:	gihyodocker/echo:patched
    Port:	8080/TCP
    Host Port:	0/TCP
    Environment:
      HOGE:	fuga
    Mounts:	<none>
  Volumes:	<none>
```

```
kubectl rollout undo deployment echo
deployment.extensions/echo rolled back
```

```
kubectl delete -f simple-deployment.yaml
deployment.apps "echo" deleted
```

### 5.9 Service
```
kubectl apply -f simple-replicaset-with-label.yaml 
replicaset.apps/echo-spring created
replicaset.apps/echo-summer created
```

```
kubectl get pod -l app=echo -l release=spring
NAME                READY   STATUS    RESTARTS   AGE
echo-spring-8j482   2/2     Running   0          3m49s
```

```
kubectl get pod -l app=echo -l release=summer
NAME                READY   STATUS    RESTARTS   AGE
echo-summer-66ccb   2/2     Running   0          3m58s
echo-summer-7bg4n   2/2     Running   0          3m58s
```

```
kubectl apply -f simple-service.yaml 
service/echo created
```

```
kubectl get svc echo
NAME   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
echo   ClusterIP   10.109.197.35   <none>        80/TCP    11s
```

```
kubectl run -i --rm --tty debug --image=gihyodocker/fundamental:0.1.0 --restart=Never -- bash -il
If you don't see a command prompt, try pressing enter.
debug:/# curl http://echo/
Hello Docker!!debug:/# 
```

```
kubectl get pod -l app=echo -l release=summer
NAME                READY   STATUS    RESTARTS   AGE
echo-summer-66ccb   2/2     Running   0          32m
echo-summer-7bg4n   2/2     Running   0          32m
```

```
kubectl logs -f echo-summer-66ccb -c echo
2019/08/25 05:55:32 start server
2019/08/25 06:21:15 received request
```

```
kubectl logs -f echo-summer-7bg4n -c echo
2019/08/25 05:55:34 start server
```

```
simple-service.yamlのspecに「type: NodePort」を追加、「release: summer」を削除
```

```
kubectl apply -f simple-service.yaml
```

```
kubectl get svc echo
NAME   TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
echo   NodePort   10.109.197.35   <none>        80:31346/TCP   37m
```

```
simple-service.yamlのspecの「type: NodePort」を削除
```

```
kubectl apply -f simple-deployment.yaml
deployment.apps/echo created
```

```
kubectl apply -f simple-ingress.yaml 
ingress.extensions/echo created
```

```
kubectl get ingress
NAME   HOSTS              ADDRESS   PORTS   AGE
echo   ch05.gihyo.local             80      50s
```

```
curl http://localhost -H 'Host: ch05.gihyo.local'
```

```
```

```
```

```
```

```
```

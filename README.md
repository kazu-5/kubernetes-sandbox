# kubernetes-sandbox
Docker/Kubernetes 実践コンテナ開発入門(https://gihyo.jp/book/2018/978-4-297-10033-9) の学習用

## 5章
### 5.6
kubectl apply -f simple-pod.yaml

$ kubectl exec -it simple-echo sh -c nginx
# 

$ kubectl logs -f simple-echo -c echo
2019/08/21 16:57:52 start server

$ kubectl delete pod simple-echo
pod "simple-echo" deleted


### 5.7


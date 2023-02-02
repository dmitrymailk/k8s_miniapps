## versions
```console
minikube version: v1.29.0
commit: ddac20b4b34a9c8c857fc602203b6ba2679794d3

Client Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.4", GitCommit:"e87da0bd6e03ec3fea7933c4b5263d151aafd07c", GitTreeState:"clean", BuildDate:"2021-02-18T16:12:00Z", GoVersion:"go1.15.8", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"26", GitVersion:"v1.26.1", GitCommit:"8f94681cd294aa8cfd3407b8191f6c70214973a4", GitTreeState:"clean", BuildDate:"2023-01-18T15:51:25Z", GoVersion:"go1.19.5", Compiler:"gc", Platform:"linux/amd64"}
```
## Build the container

```bash
docker build -t flask-app-testing .
```

## Run the container

```bash
docker run --name test-flask -p 5000:5000 flask-app-testing
```

## run minikube

```bash
minikube start
```

## enable minikube ingress addon
```bash
minikube addons enable ingress
```

## run image in minikube

```bash
minikube image load flask-app-testing
```

## run kubernetes deployment
```bash
kubectl apply -f kubernetes/flask_deployment.yaml 
```
## see pods
```bash
kubectl get pod
```
```console
NAME                                   READY   STATUS    RESTARTS      AGE
flask-app-7b469d8f64-hrl98             1/1     Running   0             62s
flask-app-7b469d8f64-l7fb9             1/1     Running   0             61s
flask-app-7b469d8f64-rt4ss             1/1     Running   0             62s
flask-app-7b469d8f64-txfsj             1/1     Running   0             62s
flask-app-7b469d8f64-zlmmf             1/1     Running   0             61s
```

## see pod logs
```bash
kubectl logs flask-app-7b469d8f64-hrl98
```
```console
[2023-02-02 19:55:47 +0000] [1] [INFO] Starting gunicorn 20.1.0
[2023-02-02 19:55:47 +0000] [1] [INFO] Listening at: http://0.0.0.0:5000 (1)
[2023-02-02 19:55:47 +0000] [1] [INFO] Using worker: sync
[2023-02-02 19:55:47 +0000] [7] [INFO] Booting worker with pid: 7
```

## run load balancer
```bash
kubectl apply -f kubernetes/flask_service.yaml
```

## see runnning services
```bash
kubectl get svc
```
```console
NAME                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
flask-app-service   ClusterIP   10.103.115.249   <none>        5000/TCP   45s
kubernetes          ClusterIP   10.96.0.1        <none>        443/TCP    5d3h
```
## run ingress
```bash
kubectl apply -f kubernetes/flask_ingress.yaml 
```

## see ingress
```bash
kubectl get ing
```
```console
NAME                CLASS    HOSTS   ADDRESS        PORTS   AGE
flask-app-ingress   <none>   *       192.168.49.2   80      8m22s
```

## see result
- теперь по одинаковому адресу нас должно пересылать на разные поды, которые имеют разные UUID, этим занимается ingress
```console
curl 192.168.49.2 -> Instance ID: ac1d02812db9474b90e787b03aa2cde7
curl 192.168.49.2 -> Instance ID: ac1d02812db9474b90e787b03aa2cde7
curl 192.168.49.2 -> Instance ID: 446395a2e9034a11be3a7346373f7a09
curl 192.168.49.2 -> Instance ID: 0cefba9cf7364c5cbb5179a5174bd15f
curl 192.168.49.2 -> Instance ID: ebcc0170500c4ebca5157a25a5ee5806
```

## delete service
```bash
kubectl delete svc flask-app-service
```

## delete deployment
```bash
kubectl delete deploy flask-app 
```
## create image
```bash
docker build -t k8s-fast-api .
```

## start minikube
```bash
minikube start
```

## setup env variables
- это нужно чтобы minikube смог найти локальные образы, иногда это нужно, а иногда нет
```bash
eval $(minikube docker-env)
```

## create deployment and service
- run in in kubernetes folder
```bash
kubectl apply -f ./
```

## see created pods
```bash
kubectl get pod
```
```console
NAME                                   READY   STATUS    RESTARTS       AGE
fast-api-5d48d4bbb5-4dzs4              1/1     Running   0              24s
fast-api-5d48d4bbb5-d6cqk              1/1     Running   0              33s
fast-api-5d48d4bbb5-mk4ng              1/1     Running   0              40s
```

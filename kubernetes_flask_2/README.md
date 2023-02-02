## versions
```console
minikube version: v1.29.0
commit: ddac20b4b34a9c8c857fc602203b6ba2679794d3

Client Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.4", GitCommit:"e87da0bd6e03ec3fea7933c4b5263d151aafd07c", GitTreeState:"clean", BuildDate:"2021-02-18T16:12:00Z", GoVersion:"go1.15.8", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"26", GitVersion:"v1.26.1", GitCommit:"8f94681cd294aa8cfd3407b8191f6c70214973a4", GitTreeState:"clean", BuildDate:"2023-01-18T15:51:25Z", GoVersion:"go1.19.5", Compiler:"gc", Platform:"linux/amd64"}
```

## run minikube

```bash
minikube start
```

## build docker image
```bash
docker build -t webapp:1.0 .
```

## setup env variables
- это нужно чтобы minikube смог найти локальные образы, иногда это нужно, а иногда нет
```bash
eval $(minikube docker-env)
```

## apply deployment
```bash
kubectl apply -f kubernetes/deployment.yml 
```

## apply service
```bash
kubectl apply -f kubernetes/service.yml 
```

## get pods and service
```bash
kubectl get pod,svc
```
```console
NAME                                       READY   STATUS    RESTARTS       AGE
pod/python-webapp-84bfd96d77-22wcl         1/1     Running   0              8m52s
pod/python-webapp-84bfd96d77-kn42j         1/1     Running   0              6m24s

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP        5d4h
service/web-service   NodePort    10.96.198.197   <none>        80:30639/TCP   23m
```

## create helm templates
```bash
helm create webapp
```

## see template result
```bash
helm template webapp
```
```console
---
# Source: webapp/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: release-name-webapp
  labels:
    helm.sh/chart: webapp-0.1.0
    app.kubernetes.io/name: webapp
    app.kubernetes.io/instance: release-name
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 5000
      protocol: TCP
      name: http
  selector:
    app.kubernetes.io/name: webapp
    app.kubernetes.io/instance: release-name
---
# Source: webapp/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: release-name-webapp
  labels:
    helm.sh/chart: webapp-0.1.0
    app.kubernetes.io/name: webapp
    app.kubernetes.io/instance: release-name
    app.kubernetes.io/version: "1.16.0"
    app.kubernetes.io/managed-by: Helm
spec:
  replicas: 2
  selector:
    matchLabels:
      app.kubernetes.io/name: webapp
      app.kubernetes.io/instance: release-name
  template:
    metadata:
      labels:
        app.kubernetes.io/name: webapp
        app.kubernetes.io/instance: release-name
    spec:
      containers:
        - name: webapp
          image: "webapp:1.0"
          imagePullPolicy: Never
          ports:
            - name: http
              containerPort: 5000
              protocol: TCP
```

## run helm app
```bash
helm install web webapp/
```

## see helm result
```bash
helm list
```
```console
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
web     default         1               2023-02-03 00:37:17.351491174 +0300 +03 deployed        webapp-0.1.0    1.16.0 
```
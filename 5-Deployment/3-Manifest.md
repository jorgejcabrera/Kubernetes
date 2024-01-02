## Manifest

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-test
  labels:
    app: front
spec:
  # acá comienza la especificación del rs
  replicas: 3
  selector:
    matchLabels:
      app: front
  # acá termina la especificación del rs

  # acá comienza la especificación del pod
  template:
    metadata:
      labels:
        app: front
    spec:
      containers:
        - name: nginx
          image: nginx:1.7.9
  # acá termina la especificación del pod
```

Si ejecutamos:

```shell
$ kubectl api-resources | grep -i deployments
NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND
deployments                       deploy       apps/v1                                true         Deployment
```

vamos a poder ver el _apiVersion_ y el _kind_ que se debe poner en el manifiesto.

Una vez que se haga el apply del manifest se van a crear rs con sus respectivos pods.

```shell
kubectl get rs
NAME                         DESIRED   CURRENT   READY   AGE
deployment-test-749675769f   3         3         3       13m
```

y si buscamos el manifest de cualquiera de esos pods vamos a ver que el _ownerReferences_ se va a referenciar al
_deployment_:

```shell
kubectl get rs deployment-test-749675769f -o yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  annotations:
    deployment.kubernetes.io/desired-replicas: "3"
    deployment.kubernetes.io/max-replicas: "4"
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2024-01-02T00:59:06Z"
  generation: 1
  labels:
    app: front
    pod-template-hash: 749675769f
  name: deployment-test-749675769f
  namespace: default
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: Deployment
    name: deployment-test
    uid: 6d0aad57-89bf-454a-991d-5b7fc945e1bd
  resourceVersion: "9989"
  uid: 612fc36e-ce42-49e7-a25a-c650c857232b

```

es decir que éste _rs_ es propiedad del _deployment_ y él será el encargado de modificarlo. Lo mismo sucede con los
pods.
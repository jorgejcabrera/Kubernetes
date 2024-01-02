#### Recap

- **Pod**: es la unidad mínima que podemos crear en kubernetes
    - War: no es conveniente crear estas entidades a mano, porque si queremos tener disponibles siempre una cierta
      cantidad deberemos operarlo a mano.
- **Replica Set**: mantiene n cantidad de pods siempre disponible.
    - Soluciona la limitación de los pods. Los observa, y si alguno falla crea uno nuevo
    - War: hay problemas cuando hay que actualizar los pods a una versión nueva o imágen distinta.

## Deployment

Al igual que el _Replica Set (RS)_ es dueño de los _Pods_, el _Deployment_ es el dueño de los _RS_. Nos va a permitir
solucionar la limitación de los _RS_: no pueden actualizar los _pods_ si hay un cambio de versión. Cada vez que se crea
un _deployment_, se crea un _rs_ y lógicamente _pods_, todos asociados a una versión. Hay dos parámetros necesarios al
momento de crear un _deployment_:

- _Max unavailable_: cuantos pods permito que mueran, es decir qué porcentaje voy a permitir que esté indisponible, por
  defecto el valor es 25. Por ejemplo si tengo 4 pods y como _max unavailable_ un valor de 25 entonces voy a permitir
  que 1 pod esté indisponible. Eso me va a permitir actualizar el pod en otro _rs_ que pertenece a otro _deployment_
- _Max search_: cuanto voy a permitir adicional al 100% para que se creen pods nuevos. Por ejemplo si tengo 4 pods en mi
  rs - que representan el 100% -, y creo un nuevo pod en un rs nuevo voy a tener un 25% adicional.

El _deployment_ irá manteniendo _rs_ a medida que se vayan cambiando las versiones. Por defecto, Kubernetes guarda 10
rs. Esto es muy útil, va a permitir hacer rollbacks.

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

## New Version

Supongamos que queremos actualizar la versión de los pods, por ejemplo usar _nginx_:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-test
  labels:
    app: front
spec:

  replicas: 3
  selector:
    matchLabels:
      app: front
  template:
    metadata:
      labels:
        app: front
    spec:
      containers:
        - name: nginx
          image: nginx
```
Tenemos que aplicar una actualización a nuestro deployment:
```shell
kubectl apply -f deployment.yml 
deployment.apps/deployment-test configured
```

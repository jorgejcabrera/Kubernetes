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

## History

Por default kubernetes va a guardar 10 rs del deployment. Podemos modificar el campo _revisionHistoryLimit_ para guardar
menos:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-test
  labels:
    app: front
spec:
  revisionHistoryLimit:
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

## Change-Cause

Nos va a permitir saber qué originó un deploy. Para eso podemos usar el parámetro _--record_ cuando hacemos el apply.

```shell
kubectl apply -f deployment.yml --record
```

y luego:

```shell
kubectl rollout history deployment deployment-test
deployment.apps/deployment-test 
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl apply --filename=deployment.yml --record=true
```

Otra forma de hacerlo es con _annotations_ en el manifest.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    kubernetes.io/change-cause: "Changes port to 110"
  name: deployment-test
  labels:
    app: front
spec:
  revisionHistoryLimit:
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
          ports:
            - containerPort: 110
```

luego:

```shell
kubectl rollout history deployment deployment-test
deployment.apps/deployment-test 
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl apply --filename=deployment.yml --record=true
3         Changes port to 110
```

ahí se ve el mensaje del template. Esta es una mejor forma de hacerlo, es similar a un mensaje de commit.
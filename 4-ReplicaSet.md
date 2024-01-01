Se encarga de mantener un número n de réplicas del mismo pod corriendo en determinado tiempo. Los pods deben tener un label para que el _replica set (rs)_ garantice la existencía de una cantidad de pods. Además el _replica set_ indicará mediante el uso de labels cual es e owner de un pod, y será útil en caso de overlapping de labels.

### Commands
```yaml
apiVersion: apps/v1 # apps es el api group de los replica set
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: rs-test
spec:
  replicas: 5
  selector:
    matchLabels:
      app: pod-label
  template:
    metadata:
      labels:
        app: pod-label
    spec:
      containers:
      - name: cont1
        image: python:3.6-alpine
        command: ['sh', '-c', 'echo cont1 > index.html && python -m http.server 8082']
	  - name: cont2
        image: python:3.6-alpine
        command: ['sh', '-c', 'echo cont2 > index.html && python -m http.server 8083']
```

Esto va a crear 5 pods con 2 containers cada uno. Si quisiéramos borrar los pods obteniendo el  nombre para luego hacer _kubectl delete pod ${POD_NAME}_ lo que va a pasar es que el rs, que es el owner del pod, va a volver a crear otro pod con dos containers.

### Owner references
Los pods tienen metadata y saben quienes son sus dueños. 
Una vez que un pod fue creado, podría modificar sus labels con el siguiente comando:
```shell
kubectl label pods my-pod app=pod-label
```
Es importante destacar que ese label es el que usamos para el _selector_ del _rs_ en el ejemplo anterior, con lo cual para el _rs_ estos dos _pods_ formarán parte de su conjunto. Esto puede ser un problema porque podríamos tener _pods_ totalmente distintos, pero que para el _rs_ sean iguales debido a que tienen el mismo label.

<div style="background-color: #ffeeba; padding: 10px; border: 1px solid #ffc107; border-radius: 5px; line-height: 1.0;">  
    <strong><span style="color: black;">Nota:</span></strong>  
    <p><span style="color: black;">Los pods siempre deberían ser creados por objetos de mayor nivel, como rs.</span></p>  
</div>

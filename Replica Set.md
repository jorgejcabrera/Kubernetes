Se encarga de mantener un número n de réplicas del mismo pod corriendo en determinado tiempo. Los pods deben tener un label para que el _replica set_ garantice la existencía de una cantidad de pods. Además el _replica set_ indicará mediante el uso de labels cual es e owner de un pod, y será útil en caso de overlapping de labels.

## Commands
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

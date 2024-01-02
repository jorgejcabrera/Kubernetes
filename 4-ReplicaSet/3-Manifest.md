### Manifest

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
          command: [ 'sh', '-c', 'echo cont1 > index.html && python -m http.server 8082' ]
    - name: cont2
        image: python:3.6-alpine
        command: [ 'sh', '-c', 'echo cont2 > index.html && python -m http.server 8083' ]
```

Esto va a crear 5 pods con 2 containers cada uno. Si quisiéramos borrar los pods obteniendo el nombre para luego hacer
_kubectl delete pod ${POD_NAME}_ lo que va a pasar es que el rs, que es el owner del pod, va a volver a crear otro pod
con dos containers.

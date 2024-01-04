## Manifest

```yml
## Deployment
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
          image: nginx:alpine
---
## Service
apiVersion: v1
kind: Service
metadata:
  name: service-test      # esto va a poder ser usado como dns
  labels:
    app: front            # son los labels que se van a utilizar para observar los pods creados por el deployment
spec:
  type: ClusterIP
  selector:
    app: front
  ports:
    - protocol: TCP
      port: 8080          # puerto que va a estar escuchando el servicio
      targetPort: 80      # indica el puerto del pod voy a consumir. El Service recibe en port y direcciona a targetPort
```

Primero vamos a específico un _deployment_ para poder tener _pods_ a los cuales referenciar. Es importante usar labels
para poder tener control sobre lo que estamos creando.


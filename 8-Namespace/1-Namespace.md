## Namespace

Es una separación lógica que nos limita ciertas cosas y accesos a recursos. Además, podemos poner limitaciones a los
recursos creados en los namespaces, por ejemplo: limites a la cantidad de pods creados, o memoria ram utilizada. Por
último podrías ayudar a implementar mecanismos de control y autorización a recursos.

### Objetos dentro de NS

```yml
apiVersion: v1
kind: Namespace
metadata:
  name: prod
  labels:
    name: prod
---
apiVersion: v1
kind: Namespace
metadata:
  name: dev
  labels:
    name: dev
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-dev
  namespace: dev            # de esta forma indicamos el namespace en el cual queremos crear recursos
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
          image: nginx:1.7.9
```

### DNS

¿Cómo es la comunicación entre recursos de distintos namespaces? Cuando los servicios que estamos creando están dentro
de un namespace se crea un dns que tiene la siguiente estructura: `{serviceName}.{namespaceName}.svc.cluster.local` ese
es el dns que por defecto todos los servicios reciben.

### Context

```shell
>kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/jorge/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Thu, 04 Jan 2024 10:21:34 -03
        provider: minikube.sigs.k8s.io
        version: v1.32.0
      name: cluster_info
    server: https://127.0.0.1:39361
  name: minikube
contexts:
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Thu, 04 Jan 2024 10:21:34 -03
        provider: minikube.sigs.k8s.io
        version: v1.32.0
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/jorge/.minikube/profiles/minikube/client.crt
    client-key: /home/jorge/.minikube/profiles/minikube/client.key
```

Para crear un nuevo contexto podemos hacer

```shell
>kubectl config set-context ci-context --namespace=ci --cluster=minikube --user=minikube
Context "ci-context" created.
```

una vez creado el contexto podemos usarlo:

```shell
>kubectl config use-context ci-context
Switched to context "ci-context".
```

Cuando estemos en el contexto no va a ser necesario pasar el nombre del namespace, por ejemplo para listar los pods.
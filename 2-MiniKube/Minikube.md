Primero [instalar](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/) _kubectl_.  Una vez que tenemos el binario darle permisos de ejecución y mover el archivo:
```shell
chmod +x ./kubectl
sudo mv ./kubectl /usr/bin/kubectl
```

Luego seguir [esta](https://minikube.sigs.k8s.io/docs/start/) guía de instalación de _minikube_. Vamos a hacer lo mismo que antes, le damos permisos de ejecución al binario y lo movemos
```shell
chmod +x minikube
sudo mv minikube /usr/bin
```

Una vez instalado, ejecutar el siguiente comando en una terminal:
```shell
minikube start
```
con esto vamos a crear un kluster de kubernetes local. Para ver el estado del cluster podemos usar:
```shell
$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

Para detener o apagar el cluster podemos usar
```shell
minikube stop
minikube delete
```

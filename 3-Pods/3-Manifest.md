 Ver el manifiesto de un pod
```shell
kubectl get pod podtest -o yaml
```
Esto me va a mostrar lo que _kubectl_ le envió a la _api_ de _kubernetes_ para crear el recurso. La ventaja de usar manifiestos es que los manifiestos los podríamos reutilizar, por ejemplo si tendríamos que crear 50 pods y no tenemos manifiestos, deberíamos ejecutar el comando 50 veces. Se vuelve complicado, realizar estos cambios de manera imperativa porque:
- no tenemos un buen control sobre los recursos creados (no tenemos un histórico en el tiempo)
- la administración individual de cada recurso es más compleja.
Estos problemas los resuelven los **manifiesto**, allí definimos todo lo que queremos, la cantidad de recursos y podemos operarlo, por ejemplo guardar versiones distintas en el tiempo.
Para crear pods a partir de un manifiesto, primero hay que crear el archivo en formato yaml:
```yml
apiVersion: v1
kind: Pod
metadata:
  name: podtest2
  labels:
    app: myapp
spec:
  containers:
    - name: cont1
      image: nginx:alpine
---
apiVersion: v1
kind: Pod
metadata:
  name: podtest3
  labels:
    app: myapp
spec:
  containers:
    - name: cont2
      image: nginx:alpine
```
En este caso se está definiendo la creación de dos containers. Luego hay que hacer el apply:
```shell
kubectl apply -f pod.yaml 
```
De la misma forma si queremos hacer un _delete_ de todos los recursos que están en un manifiesto:
```shell
kubectl delete -f prod.yaml
```

Este enfoque es más eficiente en relación a ejecutar 2 veces el comando con _kubectl_, de manera imperativa, para decir qué es lo que queremos hacer. Además permite tener una "foto", con los componentes productivos y mejor _tracking_ sabemos quién y como se crearon los recursos.

<div style="background-color: #ffeeba; padding: 10px; border: 1px solid #ffc107; border-radius: 5px; line-height: 1.0;">  
    <strong><span style="color: black;">Nota:</span></strong>  
    <p><span style="color: black;">Siempre es mejor usar manifiestos de kubernetes para crear objectos en el cluster.</span></p>  
</div>

## Dos Containers en un POD
```yml
apiVersion: v1
kind: Pod
metadata:
  name: podtest2
  labels:
    app: myapp
spec:
  containers:
    - name: cont1
      image: python:3.6-alpine
      command: ['sh', '-c' , 'echo "cont1" > index.html && python -m http.server 8082']
	- name: cont2
	  image: python:3.6-alpine
	  command: ['sh', '-c', 'echo "cont2" > index.html && python -m http.server 8083']
```
Con esto vamos a crear un _pod_ con dos contenedores que usarán la imágen _python:3.6-alpine_. Los dos containers tienen la misma IP y se comunican por _localhost_. 
Para ver los logs de alguno de los dos containers podemos usar el siguiente comando:
```shell
kubectl logs podtest2 -c cont2
```
<div style="background-color: #ffeeba; padding: 10px; border: 1px solid #ffc107; border-radius: 5px; line-height: 1.0;">  
    <strong><span style="color: black;">Nota:</span></strong>  
    <p><span style="color: black;">Si se colocan más de un container en el mismo pod hay que asegurarse de no colisionar los puertos, porque comparten el namespace de red.</span></p>  
</div>

Para ingresar a un container específico podemos usar:
```shell
kubectl exec -it podtest1 -c cont1 -- sh
```

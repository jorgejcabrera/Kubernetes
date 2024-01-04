## Commands

- Crear un pod:

```shel
>kubectl run podtest --image=nginx:alpine
```

- Listar los pods:

```shell
>kubectl get pods
```

<div style="background-color: #ffeeba; padding: 10px; border: 1px solid #ffc107; border-radius: 5px; line-height: 1.0;">  
    <strong><span style="color: black;">Nota:</span></strong>  
    <p><span style="color: black;">En kubernetes la estrategia de 1 container por pod es lo más común.</span></p>  
</div>

Para ver los eventos de un pod, podemos usar el comando describe:

```shell
>kubectl describe pod podtest
``` 

- Borrar pods:

```shell
>kubectl delete pod podetest
```

<div style="background-color: #ffeeba; padding: 10px; border: 1px solid #ffc107; border-radius: 5px; line-height: 1.0;">  
    <strong><span style="color: black;">Nota:</span></strong>  
    <p><span style="color: black;">Si no estás pudiendo acceder al pod usando su ip, podrías hace run port forwarding: kubectl port-forward podtest 8080:80</span></p>  
</div>

- Entrar a la consola de un pod:

```shell
>kubectl exec -it podtest -- sh
```

en este caso solo tenemos un solo container. Es posible que si tenemos más de un container en el pod, se nos pregunté a
qué container queremos ingresar.

- Ver logs

```shell
kubectl logs pods
```

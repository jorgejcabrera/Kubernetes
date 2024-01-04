## Service

El servicio observa todos los pods con ciertos labels. Este servicio va a tener una _Ip_ única, que se va a mantener en
el tiempo y que el cluster va a garantizar. La ventaja principal, es que si los pods mueren el servicio lo va a saber y
va a direccionar el tráfico hacia otro pod (hará round robin entre los pods disponibles). Por último, hay que mencionar
que además de tener una ip única, el servicio también tendrá un dns.

![service](https://github.com/jorgejcabrera/Kubernetes/blob/main/6-Service/img/basic.png)

```shell
>kubectl get svc -l app=front
NAME           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service-test   ClusterIP   10.110.167.135   <none>        8080/TCP   9m51s
```

el _CLUSTER-IP_ podrá utilizarse como punto de acceso a los pods.

### Endpoints

Cada _pod_ tendrá asociada una _Ip_ que irá cambiando en la medida que se vayan creando/destruyendo nuevos pods. Sin
embargo, la _Ip_ del servicio que asocia a los pods que comparten labels no cambia, entonces ¿cómo hace el servicio para
mantener actualizado las ips de los pods?. Para eso existe otro objeto llamado _endpoint_ que se creará automáticamente
cuando creamos un servicio que tiene labels: cuando el servicio encuentre un pod que cumpla con los labels lo guarda en
el endpoint, es decir que básicamente el _endpoint_ es un objeto que tiene una lista de ips. De la misma forma, cuando
un pod muere, el servicio que lo está observando eliminará su ip de la lista almacenada en el _endpoint_.

```shell
>kubectl describe svc service-test
Name:              service-test
Namespace:         default
Labels:            app=front
Annotations:       <none>
Selector:          app=front
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.110.167.135
IPs:               10.110.167.135
Port:              <unset>  8080/TCP
TargetPort:        80/TCP
Endpoints:         10.244.0.50:80,10.244.0.51:80,10.244.0.52:80
Session Affinity:  None
Events:            <none>
```

notar que los _endpoints_ contiene la lista de los pods asociados al servicio. Podemos corroborarlo viendo las
direcciones ips de los pods asociados:

```shell
>kubectl get pods -l app=front -o wide
NAME                               READY   STATUS    RESTARTS   AGE   IP            NODE       NOMINATED NODE   READINESS GATES
deployment-test-6cf85c55cf-nkjw6   1/1     Running   0          29m   10.244.0.50   minikube   <none>           <none>
deployment-test-6cf85c55cf-vsklt   1/1     Running   0          29m   10.244.0.52   minikube   <none>           <none>
deployment-test-6cf85c55cf-xtql8   1/1     Running   0          29m   10.244.0.51   minikube   <none>           <none>
```

### Service Types

- ClusterIP: es una ip virtual, permanente, que kubernetes le asigna al servicio. Esta Ip es internal cluster, con lo
  cual por fuera del cluster no se va a poder acceder.

- NodePort: nos permite exponer un servicio fuera del cluster. El _node_ tendrá un ip asociada, con lo cual se podrá
  exponer un puerto del nodo al cual un usuario podrá llegar. Entonces esto es básicamente un puerto que se abre a nivel
  del nodo para permitir el ingreso externo al cluster. Este _type_ no va a reemplazar el ClusterIp, sino que va a crear
  uno nuevo y abrirá un puerto en el nodo para recibir peticiones externas.
  
  ![service](https://github.com/jorgejcabrera/Kubernetes/blob/main/6-Service/img/node_port.png)

- LoadBalancer: crearán balanceadores externos en algún cloud provider (Aws, Asure, etc). Lo que sucederá es
  que se aprovisionará un loan balancer en el cloud provider, para luego abrir node ports en cada nodo. La ruta es la
  siguiente:

> client -> cloud lb -> node port -> cluster ip -> pods


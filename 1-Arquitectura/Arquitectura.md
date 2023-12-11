
![arquitectura](https://github.com/jorgejcabrera/Kubernetes/blob/main/1-Arquitectura/img/Captura%20desde%202023-12-10%2022-27-28.png)

## Master
El master se comunica con los nodos, es el cerebro de kubernetes. Los nodos podrían pensarse como máquinas virtuales.

En el master encontramos los siguientes componentes:
1. **API**: me comunicaré con un servicio por medio de la API usando _kubectl_. La API también soporta _inputs_ con formato `JSON` con lo cual se podrían realizar _http calls_ convencionales.
2. **Scheduler**: es el que se encarga de colocar las cosas donde tienen que ir. La _API_ interactuará con este componente para decirle qué es lo que tiene que hacer. Este componente es el que va a inspeccionar cuál es el nodo que, por ejemplo, tienen los recursos necesarios para alocar el componente que quiero crear, o por ejemplo cuál es el nodo más libre.  Si por alguna razón, no hay nodos disponibles para hacer la tarea que necesitamos, el scheduler va a quedarse con el contenedor en estado pendiente sin colocarlo en ningún nodo, y recién podrá alocarlo cuando aparezca un nodo con la capacidad. 
![scheduler](https://github.com/jorgejcabrera/Kubernetes/blob/main/1-Arquitectura/img/Captura%20desde%202023-12-10%2022-40-47.png)
3. **Kube Controller**: tiene varias responsabilidades, entre ellas es el responsable de hacer que siempre haya nodos disponibles. 
	1. _Node controller_: va a encargarse de reemplazar aquellos nodos que se caigan.
	2. _Replication controller_: se encarga todo lo que sea réplica en el cluster.
	3. _Endpoint controller_: servicios y pod a nivel de redes
	4. _Service accounts y token controller_: para temas de autenticación cuando hagamos llamadas a la API
4. **Etcd**: base de datos _key-value_. Acá el cluster guarda el estado, los datos, backups de configuraciones sobre como se está moviendo todo en el cluster. Todo lo que sucede en el cluster se guarda en esta db, y por este motivo es fácil realizar un rollback en caso de que sea necesario hacia una versión antigua estable.

## Nodo
¿Cómo nos podemos comunicar desde el master hacia los nodos? Para eso vamos a describir los componentes de los nodos:
1. **Kubelet**: En cada máquina virtual o física que está registrada como un nodo dentro de _kubernetes_ existe este  que es responsable de:
	- recibir ordenes desde el master y enviarle información.
	- se comunicará con _docker_ (o con otro runtime de container) para crear los contenedores que solicitó el master.
	- envía estadísticas/informes.
![node](https://github.com/jorgejcabrera/Kubernetes/blob/main/1-Arquitectura/img/Captura%20desde%202023-12-10%2023-00-59.png)
2. **Kube Proxy**: se encarga de configurar la red, la red de contenedores de pods. En un mismo nodo puedo tener réplicas de  contenedores de distintos servicios.
3. **Container Runtime**: es el runtime que vamos a usar para manejar contenedores. Por ejemplo: si vamos a usar docker cada nodo debe tenerlo instalado.

<div style="background-color: #ffeeba; padding: 10px; border: 1px solid #ffc107; border-radius: 5px; line-height: 1.0;">
    <strong><span style="color: black;">Nota:</span></strong>
    <p><span style="color: black;">En kubernetes existe el concepto de PODS que puede malinterpretarse como un container. Sin embargo, no es así, porque un pod podría tener más de un contenedor.</span></p>
</div>

# Containers
Un contenedor es un proceso que se ejecuta de manera aislada en un namespace. Cuando creamos un contenedor se crean los siguientes namespaces:
1. **IPC (Inter Process Communication)**: dentro de un container todos los procesos pueden comunicarse entre ellos, pero entre dos containers distintos no habrá comunicación.
2. **CGroups**: es la manera que tenemos para controlar los recursos de un contenedor (ram, cpu, ancho de banda, etc). De esta forma puedo controlar el consumo de recursos de un container, evitar que consuma más de lo esperado y eventualmente detener el nodo.
3. **Network**: cada contenedor crea su propio namespace de network, tiene una ip única. Le hacemos creer al contenedor que tiene una red privada y solo puede ver los host de esa red. Cada contenedor es independiente en temas de red.
4. **Mount**: nos permite montar sistemas de archivos externos
5. **PID**: id que se les da a los procesos corriendo dentro del contenedor.
6. **User**: usuarios creados para cada contenedor.
7. **UTS (Unix Timesharing System)**: nos da la habilidad de colocarle un hostname al contenedor. Cada contenedor tendrá un hostname único.

<div style="background-color: #ffeeba; padding: 10px; border: 1px solid #ffc107; border-radius: 5px; line-height: 1.0;">  
    <strong><span style="color: black;">Nota:</span></strong>  
    <p><span style="color: black;">Cada contenedor está aislado.</span></p>  
</div>

# Pods
Es básicamente compartir procesos, namespaces (IPC, Network, UTS), entre contenedores. Cuando le pedimos a kubernetes que cree un pod, lo primero que hace es crear un contenedor, toma la red de cada container y lo comparte con los demás, de forma tal que todos lo containers tengan la misma ip: todos los containers pueden verse por _localhost_. 

<div style="background-color: #ffeeba; padding: 10px; border: 1px solid #ffc107; border-radius: 5px; line-height: 1.0;">  
    <strong><span style="color: black;">Nota:</span></strong>  
    <p><span style="color: black;">Son la unidad mínima de kubernetes, y pueden ser uno o más containers que comparten namespaces.</span></p>  
</div>

Como todos los contenedores tienen el mismo UTS, entonces tendrán el mismo hostname. Hay que destacar que como no se comparte el _Cgroup_ para todos los containers del pod, entonces la cantidad de recursos que usará cada container en el pod será independiente. Además como tampoco se comparte el namespace de mount, puedo controlar qué volumenes se comparte en cada contenedor.
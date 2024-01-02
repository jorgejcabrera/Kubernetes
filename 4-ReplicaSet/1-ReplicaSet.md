Se encarga de mantener un número n de réplicas del mismo pod corriendo en determinado tiempo. Los pods deben tener un label para que el _replica set (rs)_ garantice la existencía de una cantidad de pods. Además el _replica set_ indicará mediante el uso de labels cual es e owner de un pod, y será útil en caso de overlapping de labels.

### Owner references
Los pods tienen metadata y saben quienes son sus dueños. 
Una vez que un pod fue creado, podría modificar sus labels con el siguiente comando:
```shell
kubectl label pods my-pod app=pod-label
```
Es importante destacar que ese label es el que usamos para el _selector_ del _rs_ en el ejemplo anterior, con lo cual para el _rs_ estos dos _pods_ formarán parte de su conjunto. Esto puede ser un problema porque podríamos tener _pods_ totalmente distintos, pero que para el _rs_ sean iguales debido a que tienen el mismo label.

<div style="background-color: #ffeeba; padding: 10px; border: 1px solid #ffc107; border-radius: 5px; line-height: 1.0;">  
    <strong><span style="color: black;">Nota:</span></strong>  
    <p><span style="color: black;">Los pods siempre deberían ser creados por objetos de mayor nivel, como rs.</span></p>  
</div>

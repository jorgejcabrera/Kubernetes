> Siembre debe mantener un número contstante de pods. ¿Qué pasa si se modifica el manifiesto del rs? Por ejemplo, se
> cambia la imágen de los containers en el pod. Si hacemos el apply no va a pasar nada, porque kubernetes va a mirar los
> labels y como esa cantidad de pods esperados ya se cumple, no se van a crear pods nuevos. Lo que podríamos hacer es
> borrar los pods y dejar que el _rs_ los reconstruya. Para solucionar esto se usarán los deployments

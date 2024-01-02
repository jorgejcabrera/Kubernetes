- Listar deployment

```shell
kubectl get deployment
kubectl get deployment --show-labels
```

- Rollout

```shell
kubectl rollout status deployment ${DEPLOYMENT_NAME}
```

- Describir replicaset

```shell
kubectl describe deployment rs-test
```
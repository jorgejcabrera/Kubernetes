- Listar deployment

```shell
kubectl get deployment
kubectl get deployment --show-labels
```

- Rollout
  status:

```shell
kubectl rollout status deployment ${DEPLOYMENT_NAME}
```

history:

```shell
kubectl rollout history deployment ${DEPLOYMENT_NAME}
deployment.apps/${DEPLOYMENT_NAME} 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

revision: me dice los cambios que se realizaron en una revision.

```shell
kubectl rollout history deployment ${DEPLOYMENT_NAME} --revision 3
deployment.apps/deployment-test with revision #3
Pod Template:
  Labels:	app=front
	pod-template-hash=6c6b85c797
  Annotations:	kubernetes.io/change-cause: Changes port to 110
  Containers:
   nginx:
    Image:	nginx
    Port:	110/TCP
    Host Port:	0/TCP
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>
```

rollback:

```shell
kubectl rollout undo deployment ${DEPLOYMENT_NAME} --to-revision=${REVISION}
```

- Describir replicaset

```shell
kubectl describe deployment ${DEPLOYMENT_NAME}
```

- Create a namespace

```shell
>kubectl create namespace test-ns
```

- Get namespace

```shell
>kubectl get namespace
```

- Get resources by namespaces

```shell
>kubectl get pods -n default
>kubectl get all -n default
```

- Describe

```shell
>kubectl describe namespace test-ns
```

- Context

```shell
>kubectl config view
```
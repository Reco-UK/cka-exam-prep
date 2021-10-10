## Pods

## Useful Commands

```shell
kubectl explain Pod --recursive | less
```

```shell
kubectl explain Pod --recursive | grep -A5 tolerations
```

```shell
kubectl run bob --image=nginx --dry-run=client -o yaml
```


```shell
## Can't edit resource of running pod, just image
kubectl edit pod
```
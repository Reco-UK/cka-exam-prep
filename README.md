# cka-exam-prep
Collect of files and notes used in preparation of CKA exam.

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

#Add Taint
```shell
kubectl taint node node01 bob=bob:[NoSchedule,NoExecute,PreferNoSchedule]
```

#Remove Taint
```shell
kubectl taint node node01 bob=bob:[NoSchedule,NoExecute,PreferNoSchedule]-
```
#Add Label
```shell
kubectl label node node01 size=large (Used with Node Selectors (Limited as can't be used with operators i.e. OR AND etc))
```

```shell
kubectl get nodes node01 --show-labels
```
```shell
kubectl get deployments -o yaml
```

### Can't edit resource of running pod

to create a daemonset , you can create a deployment with dry-run -o yaml and then modify file.

```shell
kubectl edit pod
```

to get kublet config

```shell
ps -ef |grep kublet 
grep -i static /var/lib/kubelet/config.yaml

kubectl get events
```

## Deployments / Updates

recreate vs rolling update (seamless)

## Applications

```shell
kubectl get configmaps
```
```shell
kubectl create cm map --from-literal=A=1
```
Secrets:
```shell
kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=$(echo -n 'password123' | base64)
```
## Upgrades
```shell
kubectl drain / corden uncorden node
```
## Kubeadm
```shell
apt install kubadm=v1.20.0-00
```
```shell
kubeadm upgrade plan
```
```shell
kubeadm upgrade apply v1.20.0
```
```shell
apt install kubelet=v1.20.0-00
```
to upgrade node config
```shell
kubeadm upgrade node
```

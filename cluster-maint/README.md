# Cluster Maintenance 

##Add Taint
```shell
kubectl taint node node01 bob=bob:[NoSchedule,NoExecute,PreferNoSchedule]
```

##Remove Taint
```shell
kubectl taint node node01 bob=bob:[NoSchedule,NoExecute,PreferNoSchedule]-
```
##Add Label
```shell
kubectl label node node01 size=large (Used with Node Selectors (Limited as can't be used with operators i.e. OR AND etc))
```

```shell
kubectl get nodes node01 --show-labels
```

## Upgrades
```shell
kubectl drain / corden uncorden node
```


to get kublet config

```shell
ps -ef |grep kublet 
grep -i static /var/lib/kubelet/config.yaml

kubectl get events
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

```shell
# To upgrade node config
kubeadm upgrade node
```
## KubeConfig
```shell
# Useful Info
KubeConfig is used to link users and clusters.

You should always use full path for certificate-authority field.
Alternatively you can use certificate-authority-data instead and supply base64 encoded value.

# View Config
kubectl config view

# Set context
kubectl config use-context <NAME>
```

## Kubectl proxy
```shell
# Useful Info
Kubectl proxy is used to communicate with kube apiserver using the details from the kubeconfig to access the service.

kubectl proxy # Listens on 8001

curl http://localhost:8001 -k
```

# cka-exam-prep
Collect of kubectl commands used in preparation of CKA exam.

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

## Authorization

```shell
# Generate new role
kubectl create -f role.yaml
# Bind new role to users
kubectl create -f role-binding.yaml

# Roles and role bindings fall within the scope of namespaces.
# Add namespace to metadata if you wish to assign roles and bindings to specific namespace.

kubectl get roles
kubectl describe roles <NAME>

# You can restrict access further by adding resourceNames attribute to you role manifest.

kubectl get rolebindings
kubectl describe rolebindings <NAME>

# You can check access using the can-i command

kubectl auth can-i create deployments
kubectl auth can-i delete nodes

# You can also test different users

kubectl auth can-i delete nodes --as <USER>

# You can also specify namespace

kubectl auth can-i delete nodes --as <USER> --namespace <NAME>

 
```
# cka-exam-prep
Collect of files and notes used in preparation of CKA exam.


## TLS

### Useful Commands

```shell 
# Read contents of cert
openssl x509 -in <PATH> -tect -noout 

# Encoding with openssl
cat <FILE>.csr | openssl base64

# Disable line wrapping
base64 --wrap=0 <FILE>.csr
```


### Cluster CA

```shell
# Generate Key
openssl genrsa -out ca.key 2048

# Generate CSR
openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr

# Sign Certificate
openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
```

### Cluster Admin
```shell

# Generate Key
openssl genrsa -out admin.key 2048

# Generate CSR
openssl req -new -key admin.key -subj "/CN=Kube-admin/O=system:masters" -out admin.csr

# Sign Certificate
openssl x509 -req -in admin.csr -CAcreateserial -CA ca.crt -CAkey ca.key -out admin.crt
```

### Kube Scheduler
```shell

# Generate Key
openssl genrsa -out kube-scheduler.key 2048

# Generate CSR
openssl req -new -key kube-scheduler.key -subj "/CN=system:kube-scheduler/O=system:masters" -out kube-scheduler.csr

# Sign Certificate
openssl x509 -req -in kube-scheduler.csr -CAcreateserial -CA ca.crt -CAkey ca.key -out kube-scheduler.crt
```

### Kube Controller Manager
```shell

# Generate Key
openssl genrsa -out kube-controller-manager.key 2048

# Generate CSR
openssl req -new -key kube-controller-manager.key -subj "/CN=system:kube-controller-manager/O=system:masters" -out kube-controller-manager.csr

# Sign Certificate
openssl x509 -req -in kube-controller-manager.csr -CAcreateserial -CA ca.crt -CAkey ca.key -out kube-controller-manager.crt
```

### Kube Proxy
```shell
# Generate Key
openssl genrsa -out kube-proxy.key 2048

# Generate CSR
openssl req -new -key kube-proxy.key -subj "/CN=system:kube-proxy" -out kube-proxy.csr

# Sign Certificate
openssl x509 -req -in kube-proxy.csr -CAcreateserial -CA ca.crt -CAkey ca.key -out kube-proxy.crt
```

### ECTD Server
```shell
# Generate Key
openssl genrsa -out ectd-server.key 2048

# Generate CSR
openssl req -new -key ectd-server.key -subj "/CN=ectd-server" -out ectd-server.csr

# Sign Certificate
openssl x509 -req -in ectd-server.csr -CAcreateserial -CA ca.crt -CAkey ca.key -out ectd-server.crt
```

### Kube Api Server
```shell
# Generate Key
openssl genrsa -out kube-api-server.key 2048

# Generate CSR
openssl req -new -key kube-api-server.key -subj "/CN=kube-api-server" -out kube-api-server.csr -config kube-api-server-openssl.conf

# Sign Certificate
openssl x509 -req -in kube-api-server.csr -CAcreateserial -CA ca.crt -CAkey ca.key -out kube-api-server.crt
```

### Kube Api Server ETCD Client
```shell
# Generate Key
openssl genrsa -out kube-api-server-etcd-client.key 2048

# Generate CSR
openssl req -new -key kube-api-server-etcd-client.key -subj "/CN=kube-api-server-etcd-client" -out kube-api-server-etcd-client.csr

# Sign Certificate
openssl x509 -req -in kube-api-server-etcd-client.csr -CAcreateserial -CA ca.crt -CAkey ca.key -out kube-api-server-etcd-client.crt
```

### Kube Api Server Kubelet Client
```shell
# Generate Key
openssl genrsa -out kube-api-server-kubelet-client.key 2048

# Generate CSR
openssl req -new -key kube-api-server-kubelet-client.key -subj "/CN=kube-api-server-kubelet-client" -out kube-api-server-kubelet-client.csr

# Sign Certificate
openssl x509 -req -in kube-api-server-kubelet-client.csr -CAcreateserial -CA ca.crt -CAkey ca.key -out kube-api-server-kubelet-client.crt
```

### Kubelet Server Node01
```shell

# Generate Key
openssl genrsa -out kubelet-server-node01.key 2048

# Generate CSR
openssl req -new -key kubelet-server-node01.key -subj "/CN=node01" -out kubelet-server-node01.csr

# Sign Certificate
openssl x509 -req -in kubelet-server-node01.csr -CAcreateserial -CA ca.crt -CAkey ca.key -out kubelet-server-node01.crt
```

### Kubelet Client Node01
```shell

# Generate Key
openssl genrsa -out kubelet-client-node01.key 2048

# Generate CSR
openssl req -new -key kubelet-client-node01.key -subj "/CN=system:node:node01/O=system:nodes" -out kubelet-client-node01.csr

# Sign Certificate
openssl x509 -req -in kubelet-client-node01.csr -CAcreateserial -CA ca.crt -CAkey ca.key -out kubelet-client-node01.crt
```

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

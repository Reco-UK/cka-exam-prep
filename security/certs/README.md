# cka-exam-prep - Certs
Collect of [files](generated-files) and notes used to generate self-signed certs.

### Useful Commands

```shell 
# Read contents of cert
openssl x509 -in <PATH> -tect -noout 

# Encoding with openssl
cat <FILE>.csr | openssl base64

# Disable line wrapping
base64 --wrap=0 <FILE>.csr
```

## Self-signed Certs

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
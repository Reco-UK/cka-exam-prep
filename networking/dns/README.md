# DNS

DNS allows you to store all your ip to name mappings in one location, similar to /etc/hosts but centrally and shared across multiple systems.

Machines can then use this centralised server when attempting to look up another machine by name.

```shell
# names server config
cat /etc/resolv.conf

# Add name server
nameserver 192.168.1.100

nameserver 8.8.8.8 # public nameserver hosted by google
```

Entries in the /etc/hosts will be used over DNS records, as hosts files are checked first.

This order of priority can be configured within nsswitch.conf

```shell
# 
cat /etc/nsswitch.conf

# Change ordering by moving dns to before files
hosts: files dns
```

## Domain Names

.com .net .edu .org .io etc are the top level domain names.

. Root

.com Top level Domain

google Domain

www /mail / drive etc sub-

When request is made the request first hits the Org DNS -> Root DNS -> .com DNS -> domain DNS (Google) which returns the address.

This can be cached at the org dns level to prevent to bypass this process the next time this ip is requested, but this entry will have a TTL.

## Search Domains

Search domains can be added to the /etc/resolv.conf to prevent you having to specify the full domain name when communicating with another machine on the network.

```shell
search company.com  
```

### DNS Record Types

A = ipv4 name to address mapping
AAAA = ipv6 name to address mapping
CNAME = Holds alias to an existing entry (name to name)

### Tools

nslookup - does not support /etc/hosts
dig


### DNS in Kubernetes

DNS of the nodes within a Kubernetes cluster is handled either your org or te cloud provider.

When you provision a Kubernetes cluster it will come with a coreDNS server running, unless you're creating the cluster manually, and you are required the DNS server yourself. 

Kube-DNS is responsible for storing ips and names when pods and services are created.

Pods which exist in the same namespace as each other can communicate using the name of the services.

Pods which exist in separate namespaces must append the namespace when communicating with a services in another namespace. i.e. <SVC>.<NS>

Services are grouped under the sub-domain svc:

| Hostname | Namespace | Type | IP Address    |
| -------- | --------- | ---- | ------------- |
| web      | apps      | svc  | 10.107.37.188 |

Then finally all services and pods are grouped under the root domain cluster.local:

| Hostname | Namespace | Type | Root          | IP Address    |
| -------- | --------- | ---- | ------------- | ------------- |
| web      | apps      | svc  | cluster.local | 10.107.37.188 |

### Pod DNS

Pod dns is not enabled by default within Kubernetes, but

When this is enabled the hostname will be set to the ip address of the Pod but with dots replace with dashes and will be
grouped under the pod subdomain:

| Hostname    | Namespace | Type | Root          | IP Address  |
| ----------- | --------- | ---- | ------------- | ----------- |
| 10-244-10-3 | apps      | pod  | cluster.local | 10.244.10.3 |


## CoreDNS

Prior to version 1.12 the dns server was known as kube-dns, but from 1.12+ the preferred option is coreDNS. 

CoreDNS runs as a pod (replicaset) within the cluster.

Kubernetes coreDNS config can be found within /etc/coredns/Corefile, the config contains various plugins and configs, including the Kubernetes config.

When coredns is deployed a service will also be created and the ip address of this service is set within the /etc/resolv.conf on the pods, this is 
done automatically by the kubelet when the pods are created. (The kubelet has the dns service address in its config)

The resolv.conf will also have the following search domains:

```shell
search default.svc.cluster.local svc.cluster.local cluster.local
```

When working pods you will have to specify the full FQDN as pod domain is not defined within search list.
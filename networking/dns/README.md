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
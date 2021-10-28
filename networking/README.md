# Networking

## Switching

A switch allows multiple machine to communicate on a network providing the devices which are communicating have assigned ips.

```shell
# show interfaces
ip link 

# show ip assigned to interface
ip addr 

# add ip to interface
ip addr add 192.168.1.0/24 dev eth0 

# list entries in routing table
ip route 

# Add entry to routing table
ip route add 192.168.1.0/24 via 192.168.2.1 

# Check if ip forwarding is enabled
cat /proc/sys/net/ipv4/ip_forward
```

## Routing

Routers allow machines hosted on separate networks communicate with each by connecting the two networks.  

The route will get an assigned address from each of the connected networks.


## Gateway

Gateways need to be configured on the machines in the networks, the configuration tells the machine where to 
send the traffic for a given address.

```shell
# Add route for other network
ip route add 192.168.2.0/24 via 192.168.1.1

# add default route
ip route add default via 192.168.1.1
```



# Namespaces

Network namespaces work in a similar way to kubernetes namespace in that they shield a section of the network from outside network configuration
and allocated it, its own interface, routing and arp tables.

```shell
# Create namespaces
ip netns add red
ip netns add blue

# Run command within namespaces
ip netns exec red ip link
ip -n ref link
ip netns exec red arp
```

When you are working will multiple namespaces they can be connected using a pipe.

```shell
# Create pipe
ip link add veth-red type veth peer name veth-blue

# Connect pipe to namespaces
ip link set veth-red netns red
ip link set veth-blue netns blue

# Assign addresses
ip -n red addr add 192.168.15.1 dev veth-red
ip -n blue addr add 192.168.15.2 dev veth-blue

# Bring up interfaces
ip -n red link set veth-red up
ip -n blue link set veth-blue up

# Delete pipe
ip -v red link del veth-red
```

The above method only makes sense when working with a couple of namespaces. If there are a larger number of namespaces then it makes sense to 
connect them using a bridge.

```shell
# Create interface
ip link add v-net-0 type bridge

# Bring interface up
ip link set dev v-net-0 up


# Create pipe from ns to bridge
ip link add veth-red type veth peer name veth-red-br
ip link add veth-blue type veth peer name veth-blue-br

# Connect red ns to bridge
ip link set veth-red netns red
ip link set veth-red-br master v-net-0

# Connect blue ns to bridge
ip link set veth-blue netns blue
ip link set veth-blue-br master v-net-0

# Assign ip addresses
ip -n red addr add 192.168.15.1 dev veth-red
ip -n blue addr add 192.168.15.2 dev veth-blue

# Bring up interfaces
ip -n red link set veth-red up
ip -n blue link set veth-blue up
```

The above process can be repeated for all namespaces host of the machine.

If you need to hit the bridge, when on the host, then an ip address will need to be assigned.

```shell
ip addr add 192.168.15.5/24 dev v-net-0
```

If the namespaces require access to the outside world, you will need to add a route to the corresponding ns telling it to route that traffic to 
the bridge.

```shell
# Route traffic from blue ns for 192.168.1.0 via bridge
ip netns exec blue ip route add 192.168.1.0/24 via 192.168.15.5
```

Initially you will not get a response as the return address on the packet is the address assigned to the bridge (which is not know to the outside world) and not the ip assigned the hosts interface.

To resolve this you will need to create a NAT using ip tables.

```shell
iptables -t nat -A POSTROUTING -s 192.168.15.0/24 -j MASQUERADE
```

This will make it look like all traffic is coming from the hosts interface.

If you need to connect the ns to the internet, this can be done by creating a default route inside the ns to the host (bridge).

```shell
ip netns exec blue ip route add default via 192.168.15.5.
```

If you need to give access to one of the namespaces on a given, this can be achieved using a port forwarding rule in iptables on the host.

```shell
iptables -t nat -A PREROUTING --dport 80 --to-destination 192.168.15.2:80 -j DNAT
```
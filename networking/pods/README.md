# Pod Networking

Although Kubernetes does not come with a pod networking solution out of the box, it does expect the following criteria to be met by the 
implemented solution.

 - Every Pod should have its own ip address
 - Every Pod on should be able to communicate with every other Pod on the node using that ip.
 - Every Pod on a given node should be able to communicate with all other Pods running on other nodes in the cluster.

So if you where intending to implement this manually, you would need to do as follows:

```shell
# Node1
# Implement a bridge
ip link add v-net-0 type bridge
# Bring interface up
ip link set dev v-net-0 up
# Assign ip address range to interface
ip addr add 10.244.1.1/24 dev v-net-0
# For each Pod on the Node01
# Create namespaces
ip netns add <POD>
# Create pipe
ip link add veth-<POD> type veth peer name veth-<POD>-br
# Connect red ns to bridge
ip link set veth-<POD> netns <POD>
ip link set veth-<POD>-br master v-net-0
# Assign ip addresses
ip -n <POD> addr add 10.244.(1,2,3).2 dev veth-<POD>
# Add route
ip -n <POD> route add default via 10.244.(1,2,3).1
# Bring up interfaces
ip -n <POD> link set veth-<POD> up
#For node to node comms
# Add route for each ns on each node to each node interface
# But the preferred option would be to use a network router (for small networks)
ip route add 10.244.(1,2,3).2 via 192.168.1.(2,3,4) (Target Node eth)
```

It is obviously not practical to run the above commands everytime a Pod is created and the inverse when the Pod is destroyed.

This where the CNI comes in, it will detail what the container runtime should call in the above two scenarios.

So the above commands could be put into a script (net-setup.sh) which follows the CNI standards and contains an ADD and DEL function.

The kubelet running on the machine, will look at the following locations for config and scripts:

```shell
# Kubelet command line args
--network-plugin=cni
--cni-conf-dir=/etc/cni/net.d # Configs are red in an alphabetical order
--cni-bin-dir=/etc/cni/bin

# Kubelet would then be able to execute the script
./net-setup.sh add <CONTAINER> <NAMESPACE>
```
The relating network config would look something similar to the following:

```json
{
  "cniVersion": "0.2.0",
  "name": "net0",
  "type": "bridge",
  "bridge": "cni0",
  "isGateway": true,
  "isMasq": true,
  "ipam": {
    "type": "host-local",
    "subnet": "10.22.0.0/16",
    "routes": [
      {
        "dst": "0.0.0.0/0"
      }
    ]
  }
}
```

The type *local-host* means that the ips will be managed locally on the machine in question.
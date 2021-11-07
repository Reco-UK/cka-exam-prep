# CNI - Weave (Weaveworks)

Weave is a network plugin which implements the CNI standards and can be used within Kubernetes to manage network traffic
between Pods/Nodes, as appose to function being handed off to a network device such as a router.

The weave implementation install an agent on each of the nodes, which keeps track of all the addresses being assigned and this information is synchronized
across the agents (peers) running on each of the nodes.

So when a packet leave the private network on one node, it is intercepted by the agent and is wrapped in a new packet which has the destination address set and the inverse of this happens on the other side
when the packet reaches the destination and is again intercept by the agent on the other note, which delivers the packet.

The agents store the topology of the setup and therefore have a record of the pod and there ips on the nodes.

It will also create a bridge on each of the nodes which is called weave and assign address ranges.

When a pod sends a packet to a pod on another node the weave agent will intercept the packet and identify that the pod is on another networks. It will then
encapsulate the packet with a new source and destination.


## IPAM

By default, the weave implementation allocates 10.32.0.0/12 to be used when allocating ips to the pods. The peers / agents divide up the ips in this range between the nodes within the cluster, which is 
assigned to the weave bridge. The default range mentioned above, can be overridden by passing an environment variable to the weave pod.


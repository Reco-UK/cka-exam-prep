# CNI - Weave (Weaveworks)

The CNI or container network interface is a set of standards to create plugins for the container runtimes.

The CNI will define the requirements of both the plugin and the container runtimes, so what the plugin will expose and how the container runtimes will use them.

The container runtime is responsible for: 
 - Creating a network namespace.
 - Identifying the network for the container.
 - Invoking Network Plugin such as bridge when container is ADDed.
 - Invoking Network Plugin such as bridge when container is DELeted.
 - JSON format of the network config

CNI comes with a number of builtin plugins such as:
 - Bridge
 - VLAN
 - IPVLAN
 - MACVLAN
 - WINDOWS

The above plugins contains all the required steps but expose the functionality via a simple interface.

There are also third party plugins available, such as:
 - Flannel
 - cilium
 - NSX
 - weaveworks

But Docker itself does not follow the CNI standards, but instead has its own set of standards called CNM (Container Network Model).

Because of this, it is not possible to directly start a container and attach it to a cni based network.

To do this you would have to start the docker container on the none network and then invoke the plugin yourself. (This is how it works on Kubernetes)
# Ingress

ClusterIP - Pod exposed on the cluster using a predefined ip range known to kubelet.
 
NodePort - Pod exposed outside the cluster using a port forwarding rule. (Exposed port will be high port 30000+)

With the above implementation you would need front the cluster with a proxy which would listen 80 and forward to the node port. 

This works fine when only exposing one endpoint from the cluster and would be common practice when running an on-premise solution.

LoadBalancer - When using a cloud provider such as Google, you can utilise the LoadBalancer service type. In this scenario Kubernetes
would still expose the service using a high port as it did with the NodePort service, but would also make a request into GCP to provision the 
network loadbalancer to sit in front of the exposed node port.

The loadbalancer service works fine until you introduce a second service on the same cluster which you need to expose to end users and also what to use the same
DNS record for public access.

This is possible, but you would have to again introduce the concept of a reverse proxy which sits in front of the loadbalancer services and routed the traffic
based on the incoming requests, but this solution would have additional cost and overhead, tls etc.

And this is where ingress comes in, it allows you to handle all the above requirements in the Kubernetes as another resource with its own manifest and 
specification.

Ingress is basically a l7 loadbalancer built into the cluster, which can be configured like any other Kubernetes resource.

As this resource now lives within Kubernetes, it will need to be exposed to the outside via a NodePort or Loadbalancer service.

If an ingress was to be created manually you would need to use something, such as
 - nginx
 - haproxy
 - traefik

Which would then require all the routes to be configured to the different endpoints, as well as TLS termination.

When implementing this as a Kubernetes ingress, it is made up of an ingress controller and ingress resources (config).

**Kubernetes does not come with an ingress controller out of the box**

## Ingress Controller

As mentioned above Kubernetes does not come with an ingress deployed, so this must be done manually, using one of the following solutions:
- GCP HTTP(s) Load Balancer (Supported by Kubernetes project)
- Nginx ingress controller (Supported by Kubernetes project)
- Contour
- HAProxy
- Traefik
- Istio

The ingress controller is a loadbalancer with additional functionality built in, it is able to monitor the cluster for new resources and additional ingress resources.

In order for the ingress controller to make the required changes based on new resources, you will also need to set up a service account with the correct permissions.


## Ingress Resources

Ingress resources are used to create rules against the Ingress controller, which will details how the incoming traffic is to be routed to the backend services.


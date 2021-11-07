# Services

kubelet looking for pod related changes and create and deleting pods accordingly.

The kubelet watch changes in the cluster via the kube-api-server.

When the pod is requested the kubelet will create it, on its respective node. Once pod is created it will call into the cni plugin
to configure the network.


kube-proxy watches for changes in the cluster via the kube-api-server and when a new service is requested, it will create a forwarding rule using an ip address from a predefined range and the port of the running Pod.

**The range mentioned above is defined on the kube-api-server using the --service-cluster-ip-range option (Default 10.0.0.0.24), you will need to make sure the specified
range does not overlap other ranges in use by the cluster.**

The kube-proxy has a few different ways to achieve the above:
- userspaces
- iptables
- ipvs

The selected mode is set on the pod using the --proxy-mode option. (Default is iptables)

When the entry is added to iptables it will contain the service name in the rule name.

The rule will specify that that all traffic for the assigned ClusterIP, should be forwarded to the pod, which is achieved using a DNAT rule.

When the service is of type NodePort the rule will be configured to forward all traffic for a given port to the assigned ClusterIP which is then forwarded on to the pod in question.

```shell
#You can see these rules being created in the kube-proxy log:

kubectl logs kube-proxy -n kube-system
```
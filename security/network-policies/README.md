# cka-exam-prep - Network Policies

By default, all pods will be able to communicate which each other over the Kubernetes configured network.

To block access between pods, you must assign a network policy defining ingress and egress for a given pod, both ingress and egress can be defined under the same policy.

The policy will allow traffic as defined within the specification and block all other traffic.

The policy type is dictated by the location of where the request originates, and traffic will be enabled in both directions.

### Ingress

If you wish for the policy to only allow traffic from a given namespace, then the namespaceSelector/matchLabels must be supplied.

If the namespaceSelector is used in isolation then all pods within the namespace will have access.

**You much make sure that the namespace has the corresponding label used in the above selector.**

You can also allow traffic from a list of predefined ip ranges, which is handy when traffic is coming from outside the cluster.

The list of objects under the *from:* key are treated as individual rules and checked in turn using the OR operation.

If a rule contains multiple checks i.e. podSelector and namespaceSelector, they will be checked in turn using an AND operation.

### Egress



### Network Implementations

The following network implementations support network policies:
  - Kube-router
  - Calico
  - Romana
  - Weave-net

Flannel does not support network policies, but this open to change, and you would need to review the network documentation to confirm.

**Please note that you will not receive an error when using network policies on an implementation which does not support them, you will just not achieve the desired affect**
## Role Based Access Control

```shell
# Generate new role
kubectl create -f role.yaml
# Bind new role to users
kubectl create -f role-binding.yaml

# Roles and role bindings fall within the scope of namespaces.
# Add namespace to metadata if you wish to assign roles and bindings to specific namespace.

kubectl get roles
kubectl describe roles <NAME>

# You can restrict access further by adding resourceNames attribute to you role manifest.

kubectl get rolebindings
kubectl describe rolebindings <NAME>

# You can check access using the can-i command

kubectl auth can-i create deployments
kubectl auth can-i delete nodes

# You can also test different users

kubectl auth can-i delete nodes --as <USER>

# You can also specify namespace

kubectl auth can-i delete nodes --as <USER> --namespace <NAME>
 
```
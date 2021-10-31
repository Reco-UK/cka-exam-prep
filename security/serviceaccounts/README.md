# cka-exam-prep - Service Accounts

## Service Accounts

Service accounts wil be used by applications to auth and communicate with the kube api.

```shell
# Create service account
kubectl create serviceaccount <NAME>

# Get service account
kubectl get serviceaccount
```

*** Every namespace has a default service account and this will be mounted to the pods within the given namespace by default.

When the service account is created it will also have an associated token used to auth with the kube api.

You can look up the token by running describe against the service account.

```shell
# Describe service account
kubectl describe serviceaccount <NAME> | grep -i token
```

The generated token will be stored as a secret.

When the service account is used for an application running on the same cluster the secret can be mounted as a volume which the application can use to auth against the api.

You can get the location of the mount by describe the running pod, this usually be:

```/var/run/secrets/Kubernetes.io/serviceaccount```

which will contain the ca and token.


***You can not edit the service account of an existing pod, it must be removed and re-created.
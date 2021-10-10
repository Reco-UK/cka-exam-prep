# cka-exam-prep - Images

## Public Registries

Images follow the docker naming standard.

When you specify the following in your manifest:

```image: nginx```

You are actually stating ```docker.io/library/nginx```.

- docker.io = Registry
- library   = Account
- nginx     = Image/Repository

## Private Registries

Images follow the naming standard of your provider.

```image: private-registry.io/apps/nginx```

## Authentication

When working with any registry, you will need to first authenticate before you can start using the images.

To do this in a kubernetes cluster, you will first have to create a security with the registry details:

```shell
# The docker-registry secret is a built-in type used to store docker credentials.

kubectl create secret docker-registry regred \
  --docker-server=<SERVER> \
  --docker-username=<USER> \
  --docker-password=<PASSWORD> \
  --docker-email=<EMAIL>
```

nce this has been created, it can be added to the pod manifest as a pull secret.

```yaml
spec:
  imagePullSecrets:
    - name: regcred
```
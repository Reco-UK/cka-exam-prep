## Secrets


Secrets:
```shell
kubectl create secret generic db-secret --from-literal=DB_Host=sql01 --from-literal=DB_User=root --from-literal=DB_Password=$(echo -n 'password123' | base64)
```